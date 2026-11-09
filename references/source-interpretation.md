# Consolidation and source precedence

Internal to EXL, not for external distribution. Upstream: https://ucgithub.exlservice.com/Alan345307/arb-agents, branch main, commit 1300c7ab6961671cc079718faed321e18c0dd5b7. This package consolidates Alan's document/diagram guidance with the AWS conversational reviewer and single findings template. No repository changes were pushed.

## Sources
The five review standards under references/standards are exact snapshots from the above GitHub commit. Principles, AWS services and AWS Well-Architected guidance match the local canonical versions. Approved libraries and agentic guidance have newer GitHub changes; they are used here as the user-specified committed baseline, without overwriting local canonical files. Templates are exact local canonical snapshots. Record both origins when reproducing a review.

## Policy interpretation
- GitHub approved-libraries adds Rust, LiteLLM, Langfuse and CI/CD entries and changes several restrictions. Use those actual entries. Do not use the old local duplicate-Go note.
- CodePipeline is Prohibited in approved-libraries.md#cicd but Approved in approved-services-aws.md#devops; the AWS lens also recommends it. CloudFormation has a restriction/approval discrepancy. Flag the conflicting sources and escalate the approval decision; never silently pick a permissive result.
- Next.js status `Restriced`, duplicate Jenkins entries with differing notes, `Foritfy`, and Prisma's ORM/security contexts need exact source interpretation. Do not assume spelling corrections or same-name technologies are equivalent.
- technology-catalogue-upstream.md is stale for Langfuse, LiteLLM, Jenkins and other now-listed entries. It is advisory discovery evidence only. The actual approval registers take precedence over this catalogue. Named in principles is not approval-list membership; unlisted technology still requires ARB exception. Azure/GCP catalogue examples are outside AWS review scope.
- Agentic section 8 explicitly says pending ratification. Its additional recommendations are Advisory unless an independently ratified principle/section supplies the requirement. Sections 1–7 remain baseline guidance; section 6 records approved model/version rather than forcing latest Claude.
- The host enforces read authorization despite legacy unrestricted-read wording. No agent approval or external-write authority is granted.
- Workbook Cyber Risk Status is supplementary evidence, not service authorization. Proposed/blank is not Approved; show conflicts with registers. Parent service approval does not imply all subproducts are approved.

## Adaptation
Alan's sdd-sections.json explicitly says structure is from memory. Use it for navigation and helper hints, not as a ratified IEEE requirement. The bundled canonical SDD template governs structure; missing headings alone are not proof of missing design content. Do not automatically grade every missing view/placeholder Blocker: use impact, stage and applicable standard. All recommendations use Blocker / Major / Minor / Advisory.

Use diagram-views.md to assess context, containers and deployment information in any notation. A combined diagram can meet multiple views; do not require a different notation. Count diagrams actually opened or source diagrams actually inspected; inaccessible figures remain unchecked. Prefer readable source where available and report disagreements with rendered images.

One output template is retained: assets/templates/aws-architecture-findings-template.md. Alan's separate readiness-report template is not imported. One shared system prompt and goal are used in both variants.
