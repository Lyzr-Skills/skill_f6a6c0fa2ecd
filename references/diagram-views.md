# Diagram views the SDD must show

Principle 15 requires a C4 diagram: Context and Container, with Component where useful. The ARB also expects to see where each container runs. Any notation or image format is acceptable; the test is the information shown, not the diagram's name or tool.

| View | Must show |
|---|---|
| Context | The system boundary, its users and the external systems it talks to |
| Containers | The deployable parts and data stores, what each does, and how they communicate (direction, protocol) |
| Deployment | Where each container runs (account or subscription, region, network zone or cluster), trust and network boundaries, and which parts are managed services |

A combined diagram counts for each view whose information it contains. If it mixes logical components with infrastructure such as load balancers and subnets, ask the author to split it; raise a finding only if you cannot tell how containers communicate or where they run. The diagrams must agree with the text and with each other: every container in the text is in the diagram, and the reverse.
