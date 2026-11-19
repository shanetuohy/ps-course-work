# Prototype · Juno

> Module 2 · Prototype the Idea Worth Building. The working build that tests your idea, made with your build tool of choice.

## Prototype link

The prototype runs locally; a public share URL is not currently available. The html file is in the folder. 

## What it demonstrates

A PM preparing for a feature’s go/no-go review opens a Juno readiness assessment within the feature’s Jira issue. It brings together engineering bugs, test results and beta feedback, highlighting material customer risks, missing evidence and suggested next steps.

The PM can inspect the supporting sources, flag inaccurate findings and record a provisional release position with a rationale.

The fictional example shows why a medium-priority bug could matter more than the overall number of open issues. All evidence and assessments are simulated.

## Debrief

- **What worked:** The prototype connects scattered signals into a release assessment, with supporting evidence available for inspection. Revising the surrounding interface to resemble Jira made the intended workflow clearer.

- **What broke / felt like a toy:** The first version looked too much like a standalone tool. Even within Jira, a report could go unnoticed without a connection to the team’s Webex space and release review process. The custom formatting also needs validation against what the target Jira setup supports.

- **What I'd change next pass:** Include transcripts from meetings associated with the feature’s Webex space as another evidence source. Have Juno notify the space when the report is ready, with key findings and a link to Jira. Explore scheduling the go/no-go meeting with the relevant participants so the gate review happens and its decision is recorded. Adapt the report to Jira’s supported fields, formatting and links. These workflow integrations are proposed improvements and are not implemented in the current prototype.
