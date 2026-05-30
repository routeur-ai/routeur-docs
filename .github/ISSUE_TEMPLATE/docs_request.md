---
name: Documentation request
description: Propose a new page, example, clarification, or guide
labels: [documentation]
body:
  - type: markdown
    attributes:
      value: |
        Use this template for gaps in the docs, not product feature requests.
  - type: input
    id: topic
    attributes:
      label: Topic
      placeholder: Add a TypeScript quickstart for responses API
    validations:
      required: true
  - type: textarea
    id: gap
    attributes:
      label: What is missing or unclear?
      placeholder: There is no page that explains...
    validations:
      required: true
  - type: textarea
    id: audience
    attributes:
      label: Who is this for?
      placeholder: New users migrating from the OpenAI API
  - type: textarea
    id: proposal
    attributes:
      label: Proposed improvement
      placeholder: Add a short guide with curl, JavaScript, and Python examples.
---
