# AI PRD · Juno

> Module 3 · Release readiness copilot for RocketShip PMs

## Problem & user

Before a go/no-go review, the PM has to bring together engineering updates, technical metrics, QA results, product input and beta feedback. These signals live in different places and tell different parts of the story. The PM needs to see what they say together about release readiness, where they disagree and what still needs checking before the team can make an informed decision.

Juno prepares the assessment seven calendar days before the planned release date in Jira. When the PM opens the release issue, they can read the draft, refresh it, inspect the sources, record a provisional position and flag a finding for correction. After a refresh, **What changed** shows which risks or open questions changed, what evidence explains the change and what the team still needs to do. The release team makes the go/no-go decision. The [Module 2 prototype](../02-prototype/prototype.md) demonstrates the review interaction with simulated data.

**RocketShip example.** The Q3 CSV export migration moves exports to an async worker. A customer reported that exports hang and crash, and Engineering reported failures on files over 50 MB. The supplied case has no post-migration QA result for large exports. Juno should surface the known risk and the missing result in the same summary.

### What good looks like

- **First review.** The draft is ready when the PM opens Jira after the scheduled run. Within five minutes, the PM can identify the main release risk and its customer impact, see what the evidence supports, and name the next action. They can do this from the panel and its source links without gathering the records themselves.
- **Refresh.** The PM can see what changed that could affect go/no-go, why it matters, and what to do next. Each change links to evidence. Unchanged findings and wording edits stay out of **What changed**. Open follow-ups stay visible.
- **Follow-through.** The PM can see what would resolve each open item and who owns it when the source names an owner. An item closes when the required evidence or a recorded decision resolves it. It reopens when new evidence makes the prior resolution invalid.
- **Trust.** The draft distinguishes confirmed facts, Juno's interpretation, and missing or conflicting evidence. A false QA pass, an unsupported resolution, or exposure of a restricted record is a pilot failure.

## The harness

**Archetype · Tool-Using Copilot.** Juno prepares a draft before the PM opens Jira. The PM reviews the draft and can refresh it. Juno can verify whether a QA record supports a particular test claim. The release team weighs that evidence alongside the remaining risks.

| Surface | Specification |
|---|---|
| 01 Context | Read the current release issue and pull related evidence from authorized company systems. |
| 02 Tools | Retrieve release records, live metrics and existing snapshots automatically, then draft a summary. The PM can explicitly save a review note. |
| 03 Loop | Run seven days before the Jira release date or on manual refresh. Make one assessment pass per run and hand unresolved checks back to the PM. |
| 04 Memory | Keep the latest draft, PM note and release follow-ups. Compare a refresh with the previous draft and carry open items forward until there is evidence or a decision that resolves them. |
| 05 Permissions | Juno reads and drafts automatically. Shared record changes and sends are blocked for Juno. The PM can save their own note. |
| 06 Verification | Check claims against their sources and match QA results to the release, test case and build. |

## 01 Context · Data Requirements

| Source | What Juno uses |
|---|---|
| Jira release issue and its designated Fix Version | Use the **Release date** on the version in Jira Releases to schedule the assessment. Use the issue for release ID, scope, candidate build if known, PM input on readiness and links to relevant evidence and checks. If the issue has more than one Fix Version, use the version designated for this review. |
| Linked bugs, engineering updates and beta or customer feedback | Known issues, fix status and what customers experienced. |
| Company metrics system and existing dashboard snapshots or reports | The technical metrics that matter for this release, broken down where an overall number could hide a problem. Juno queries the live source and retrieves any relevant snapshots already available. Include the measurement window, environment and build when available. Identify any baseline separately from measurements for the release under review. |
| QA test cases and results linked to the release issue | QA links each release check to its test case. Each result must identify the test case, build, conditions tested, outcome and source. Store these records on the issue or link to the QA system that supplies them. Confirm the QA system and field mapping before integration. |
| Juno review note, if one exists | The current release's last PM position, rationale and open checks. |

**RocketShip example.** For the CSV export migration, use the **Product Quarterly Roadmap**, **Engineering Capacity Note**, and the customer interview and backend thread in **Raw Signals**. The metrics to look for are export failure rate and completion time by file size, with particular attention to files over 50 MB. The supplied documents report failures at that size. They do not contain a breakdown of either metric.

**How Juno finds evidence.** Start with the Jira release ID. Follow the release's links and use configured mappings in approved systems, such as the service, environment and build that identify its metrics.

**Exclusions.** Leave out other release issues, unrelated tickets and broad chat or CRM history. A keyword match alone cannot bring a record into the review. Sales pipeline notes are outside the initial scope.

**Freshness.** Every run reads the latest available source records. Show the build reviewed and the time evidence was checked so the PM knows what the prepared draft reflects. Label snapshots with their capture time. New evidence appears after the next successful refresh. If the current Jira release date or build differs from the one reviewed, label the draft as out of date and offer a refresh.

**Source unavailable.** If a linked source is unavailable, draft from the sources that are accessible and name the missing source. If the build or QA result cannot be confirmed, mark the affected check as unverified and explain why.

## 02 Tools · System Capabilities

| Verb | Class | What it does |
|---|---|---|
| `read_release(release_id)` | Read | Gets release scope, the designated Fix Version and its Release date, the candidate build, and linked record IDs. |
| `read_release_evidence(release_id)` | Read | Pulls authorized engineering records, QA results, beta feedback and PM input from configured sources using the release ID and its links. Reports any source it could not reach or match to the release. |
| `read_release_metrics(release_id)` | Read | Queries the approved metrics system using the release's configured scope and retrieves relevant snapshots or reports already in company systems. Keeps the source and measurement window with each result. |
| `read_review_note(release_id)` | Read | Loads the latest PM note for this release, if authorized. |
| `draft_readiness_summary(evidence, review_note, material_changes, follow_ups)` | Draft | Explains the main risk and why the verified changes matter. Shows current follow-ups and suggested next steps with source links. |

**Optional PM action.** The PM can review and refresh the assessment without recording a position. If they want to record their view, they choose **Ready**, **Hold**, or **Need more evidence** and can add a rationale. Clicking **Record provisional position** saves the PM's choice, rationale, any corrections and follow-ups, and the assessment reference to Juno's review note for the release. Only an authorized PM can save or change this position. The release team owns the final go/no-go decision.

**Deliberate omissions.** Juno has no unrestricted company search, Jira status or assignment tool, or messaging tool. Evidence retrieval is scoped to the release through its links and configured mappings. The release team owns status and assignments, and the PM decides what to share.

## 03 Loop · AI Costs & Latency

- **Automatic start.** Prepare one draft for an active release seven calendar days before its planned date in Jira, using the project's configured time zone. Check the date daily. If a date is added or moved inside the seven-day window, run on the next daily check. Deduplicate scheduled runs for the same release and planned date. If the date is missing or ambiguous, show why automatic preparation is unavailable and keep manual refresh available.
- **Manual start.** The PM can use **Refresh assessment** from the Jira release issue to run the same workflow early or after new evidence arrives. Use the issue ID from Jira and ask for no extra input. While a run is in progress, show that state and avoid starting a duplicate.
- **Turn ceiling.** One assessment pass per run. Fetch the scoped records, check what changed and which follow-ups the evidence resolves, make at most one model call to draft, check the output and stop.
- **Escalation.** Stop trying to resolve a release check when its evidence is missing, belongs to another build, or conflicts with another applicable result and QA has not marked one as superseded. Show the issue in the panel and identify the record or decision the PM needs from QA or Engineering. No automatic retries or messages.
- **Failure or timeout.** If a run fails or reaches 60 seconds, show its status in Jira and offer a refresh. Keep the last successful draft with its original timestamp if one exists. Otherwise show any evidence safely retrieved and identify the gaps.
- **Proposed targets.** Complete a run within 10 seconds at p95. Cap model cost at US$0.05 per run, including failed attempts. At 1,000 automatic and manual runs a month the model budget would be US$50, excluding storage and integrations. Validate the targets in the pilot.

**Jira integration to validate.** A daily scheduled job reads the selected Jira version's **Release date** and passes eligible release issue IDs to Juno. On Jira Cloud, the first manual entry point can be a Jira Automation rule named **Refresh assessment** in the issue's **Actions** menu. It calls the same Juno service with the issue ID. For the embedded experience, a Forge app can put a refresh button inside its Juno panel on the issue. Atlassian limits the standard panel launcher to users who can edit the issue. Confirm the Jira deployment, available placement, user permissions and service connectivity with the Jira admin before building. [Manual automation trigger](https://support.atlassian.com/cloud-automation/docs/test-a-jira-automation-rule-using-the-manual-trigger/) · [Automation actions](https://support.atlassian.com/cloud-automation/docs/jira-automation-actions/) · [Jira version dates](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-project-versions/) · [Forge issue panel](https://developer.atlassian.com/platform/forge/manifest-reference/modules/jira-issue-panel/)

## 04 Memory · Data Requirements

| What is kept | Scope and expiry |
|---|---|
| Retrieved evidence and latest prepared draft | Discard the working copy of the source records after each run. Keep the latest checked draft, release date, build, preparation time and a compact snapshot of tracked signals and follow-ups. Store the item ID, last observed value or status, and source reference so Juno can compare the next refresh. Replace the draft and its comparison after a successful refresh. Delete them when the release issue closes. |
| Latest PM review note | One note per release issue, visible to authorized reviewers. Store the PM's position, optional rationale and corrections, PM-recorded follow-ups, author, time and references to the build and evidence reviewed. Keep it while the release issue is open and for 90 days after closure. Review this proposed retention period in the pilot. |

On refresh, Juno reads the Jira release date, candidate build and linked evidence again. The saved draft is the comparison point. The PM owns follow-ups and can revise them after seeing the new evidence.

**What changed.** Compare the refreshed assessment with the last successful one for the same release. For each material change, show what moved, why it matters to the release, and any next action. Link the evidence. Hide the section on the first assessment or when there are no material changes. Keep open follow-ups visible even when nothing changed. If refresh fails, keep the previous assessment and timestamp and show the refresh error.

| Signal | Show in What changed when |
|---|---|
| QA | A release check now passes, fails, or needs fresh evidence for the candidate build. |
| Engineering | A linked fix moves past an agreed cutoff, or the candidate build changes what still needs verifying. |
| Technical metrics | A comparable measurement crosses an agreed threshold or changes enough to meet the release team's criteria for review. |
| Beta or customer feedback | A new or worsening issue linked to the release meets the team's criteria for customer impact. |
| PM input or release plan | A recorded change to scope, timing or a release requirement changes what the team needs to verify or decide. |

Set the criteria with the release team for the pilot. Leave out wording changes, repeated reports of the same issue and metric movement below the agreed bar. Show an unavailable source as an evidence gap. If a metric lacks a comparable baseline or an agreed threshold, show its source value and flag the missing context.

**Follow through on open items.** For each material open item, show the evidence or decision needed next. Show the recorded owner when known, or suggest the team to ask. Carry the item forward on each refresh using its linked record or check ID. Mark it resolved when fresh evidence satisfies the stated requirement, or show the disposition recorded by the release team when it needs a judgment call. Keep any underlying QA failure visible. Reopen the item if later evidence invalidates the resolution. A missing or unreadable source leaves the item open. The PM's recorded position stays as entered until the PM changes it.

## 05 Permissions · AI Risks & Mitigations

| Side-effect class | Tier | Rule |
|---|---|---|
| Read | Auto | Use the release's links and configured source mappings. Scheduled runs use an approved service identity scoped to eligible releases and their sources. |
| Draft | Auto | Prepare and cache the draft in Juno. Before display, verify the viewer can read the release and the records used in the draft. If access to a source cannot be confirmed, withhold that draft and offer a refresh using the viewer's access. |
| Write to a shared record | Blocked for Juno | Juno cannot change Jira fields or status, post comments, assign work, or set the PM's position. The PM can save their own note through **Record provisional position**. |
| Send | Blocked | The PM decides what to share and sends it themselves. |

Juno stores its generated assessment in its own draft storage so it can display it in the panel. It replaces that draft on a successful refresh. The PM's note is a separate record authored and saved by the PM. Jira status changes, assignments and messages stay with the team.

## 06 Verification · AI Testing & Measurement

**What Juno verifies before display.**

1. **Source and scope.** Each factual finding must point to a record linked or mapped to the release that the viewer can access. The system displays the source field or excerpt directly. For metrics, show the measurement window, environment and build when available. Label Juno's interpretation separately and show the evidence behind it.
2. **QA outcome.** The system determines the status of each release check outside the model. It uses a result only if QA linked the test case to that check, the result is for the candidate build under review, and the test conditions cover the check. Show the recorded pass or failure. If matching results disagree, use the result QA explicitly marked as replacing the other. If there is no matching result or the conflict remains unresolved, show **Unverified** and explain why.
3. **Changes since the last assessment.** Match tracked items by stable source or check ID. Base changes and resolutions on source records and the pilot criteria. For metrics, check the definition, units, environment, population and measurement windows before comparing. Show the respective builds where known. If a result only applies to an older build, show the current check as **Unverified** and say fresh evidence is needed. Apply the viewer's source permissions to both sides of the comparison.

**What still needs human judgment.** PM, QA and Engineering judge whether the evidence covers how customers will use the release, which risks matter and what to do when different sources point in different directions. Juno shows those signals side by side and identifies open questions. The release team decides whether the evidence is sufficient, what risk to accept and whether to ship.

**When a check fails.** Keep the supported findings visible. If Juno references a missing record or excerpt, omit the finding and show **“A draft finding could not be verified against the available records.”** If a QA result is missing, show **“Unverified. No matching QA result for this release check and build.”** If results conflict or a source is unavailable, show that reason instead. Point the PM to the missing test, build or source in the panel. If the release issue itself is unavailable, show **“Juno couldn’t load this release. Reopen the Jira issue and try again.”** If access is denied, show **“You don’t have access to review this release.”**

## Eval plan

Module 5 will define the [human rubric and scoring](../05-evals/human-rubric.md). Before the pilot, have PM and QA reviewers agree on the main risk, evidence gap, next action and expected changes for each case. Compare PM reviews on comparable releases with Juno and with their current workflow. Measure time to identify the right risk, gap and next action, and check that any time saved comes with at least the same accuracy.

Use cases with a valid pass, a valid failure, no matching result, the wrong build, an unrelated ticket with similar wording, and conflicting QA results. For refreshes, include a metric crossing its threshold while QA statuses stay the same, a material beta issue, a fix slipping past its cutoff, and a wording-only change. Include an open item that resolves on new evidence and reopens when a later build invalidates that evidence. Check that the PM can distinguish what changed from what is still open.

## Out of scope

Automatic go/no-go decisions, Jira workflow changes, work assignments, messaging or publishing, running QA tests, continuous monitoring of evidence changes and memory shared across releases.
