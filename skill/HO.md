# Task specs in a Ho System project

Apply these changes to the [Dandori format](FORMAT.md) when the target project uses the Ho System. Kamae-5 and the practitioner decide whether a ho needs child tasks. For maintenance or exploration between hos, use Dandori directly.

## Filing the spec

Write both kinds of task in `ho-process/agent-tasks/`, alongside `ho-process/hos/`. If the project designates another canonical process root, use its `agent-tasks/` directory. In a project with a generated public process record, write to the private source.

| | Child of a ho | Standalone task in a Ho project |
|---|---|---|
| Filename | `Ho-NN-AT-MM.md` | `Standalone-AT-YYYY-MM-DD-slug.md` |
| `type` | `agent-task` | `standalone-agent-task` |
| Parent binding | Replace `parent` with `parent-ho: "NN"` and `task: "MM"`. List the child's filename under `agent-tasks` in the parent ho's frontmatter. | Omit the parent and task-number fields. |
| Commit | One commit with subject `ho-NN: <summary>` and the full task ID in the body. | One commit with a prefix appropriate to the work, such as `fix:` or `docs:`. |

Keep the parent's identifier, including any sub-ho suffix. Quote the numeric identifiers so YAML preserves leading zeros (for example, `parent-ho: "01"` and `task: "02"` for `Ho-01-AT-02.md`). For standalone tasks use the authored date and Dandori's usual descriptive slug.

## Choosing the model

Both kinds of task require `project` and `model` in frontmatter alongside `created`, `type`, and `status`. Record the actual executor's model ID. Use the practitioner's model choices for the work: a strong reasoning model for design, a coding model for implementation and tests, the most capable available model for review, or a fast, inexpensive model for mechanical changes. If the choice is unknown, ask before marking the spec ready.

## From Think to Execute

For a child task, take the decisions from the parent ho's resolved Think phase or the agreed solution in a ri ho. Apply the translation moves in FORMAT.md to those decisions. For example, if Think chose strict parsing but deferred the response to an unfamiliar export field, specify strict parsing in Required Changes and make the unfamiliar field a Stop Condition. Order sibling tasks so the artifacts each one verifies already exist. For a standalone task, the authorizing decisions come from the planning conversation.

The five [kokoroe guidelines](KOKOROE.md) apply throughout. Under *Spec as authorization* and *Honor the boundary*, the executor implements the decisions the practitioner authorized and brings any discovery that would reopen one back to the practitioner. A child spec records enough of the decision and its boundary to execute independently.

See an [implementation child](examples/ho/ho-child-implementation.md) and an [inspection child with a stop condition](examples/ho/ho-child-with-stop-condition.md). For standalone maintenance, use the [utility example](examples/standalone-utility.md) with the standalone filename, fields and commit convention above.
