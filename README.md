# Apithy

Data is solved.

You can define objects quite well. Let's imagine a simple Todo App.

`Task.object.yaml`
```
singular: Task
plural: Tasks
behaviors:
  timestamped: ~ # by default everything gets created_at, updated_at
  softdelete: ~ # by default should be enabled
structure: # Using a name other 'properties' because I'm super-setting JSON Schema
  title:
    type: string # string means specifically a single conceptual line of text
    required: true
  description:
    type: text # text means specifically a block of text, more than "string"
  state:
    type: enum
    enum:
      - todo
      - done
    default: todo
  is_completed:
    type: expression # could also be cached-expression but that would be redundant since this does not aggregate
    expression: state == "done" # we'll constrain to a simpler domain of equations for portability
                                # but in reality this will just pass through to the API implementation for now.
events:
  complete:
    from_state: todo
    to_state: complete
  todo:
    from_state: complete
    to_state: todo
```

We could then envision the Repository definition for this object in `todolist.repository.yaml`:
```
object_paths:
 - ../objects/
objects:
  task: ~ # leave the specific path able to be defined to override auto-location finding, or to enable renaming for sanity
repository:
  task:
    orderBy: title
```

The inferred config would end up looking something like:
```
object_paths:
 - ../objects/
objects:
  task: ~ # leave the specific path able to be defined to override auto-location finding, or to enable renaming for sanity
repository:
  task:
    indexes:
      id: ~
      state:
        include:
          - title
```

From this, we know we need a data store named task, and that we will look it up by id, or by state, ordered by title.

We also have enough information to generate a basic admin system for this data.

Some WIP diagrams explaining this in another way:

![Apithy Use Case](docs/images/apithy-use-case.jpg)

![Why start at customer data](docs/images/why-start-at-customer-data.jpg)
