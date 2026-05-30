---
name: Bug report
description: Report something inaccurate, broken, or confusing in the docs
labels: [bug]
body:
  - type: markdown
    attributes:
      value: |
        Thanks for reporting an issue. Please keep the report focused on the documentation or the public API reference.
  - type: textarea
    id: problem
    attributes:
      label: What is wrong?
      description: Describe the issue clearly and include the affected page URL if relevant.
      placeholder: The quickstart says X, but the API returns Y.
    validations:
      required: true
  - type: textarea
    id: expected
    attributes:
      label: What did you expect instead?
      placeholder: The page should explain...
    validations:
      required: true
  - type: input
    id: page
    attributes:
      label: Affected page
      description: URL or repository path
      placeholder: https://docs.routeur.ai/guides/quickstart
  - type: textarea
    id: context
    attributes:
      label: Additional context
      description: Screenshots, API responses, or anything else that helps reproduce the problem.
---
