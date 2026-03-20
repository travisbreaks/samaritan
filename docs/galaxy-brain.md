# Galaxy Brain Strategy

Earn GitHub's Galaxy Brain achievement by answering unanswered Q&A discussions with detailed, code-rich answers.

## Requirements

- **Bronze**: 1 marked answer on a Discussion in a repo you don't own
- **Silver**: 8 marked answers
- **Gold**: 16 marked answers

The answer must be **marked as the answer** by the discussion author or a repo maintainer. Simply posting a helpful reply is not enough.

## What works

### 1. Target repos with active answer-marking

Not all repos mark answers. Before investing time, verify:

```bash
# Check if the repo has Q&A discussions with marked answers
gh api graphql -f query='
{
  repository(owner: "OWNER", name: "REPO") {
    discussions(first: 10, categoryId: "Q&A_CATEGORY_ID", answered: true) {
      totalCount
    }
  }
}'
```

If the answered count is zero or very low, the repo does not mark answers. Move on.

**High-marking repos** (verified): vitejs/vite, TanStack/query, TanStack/router, pmndrs/react-three-fiber, pmndrs/drei

### 2. Find unanswered questions matching your expertise

```bash
# Search for unanswered Q&A discussions
gh api graphql -f query='
{
  search(query: "repo:OWNER/REPO is:discussion is:unanswered category:Q&A", type: DISCUSSION, first: 10) {
    nodes {
      ... on Discussion {
        number
        title
        body
        createdAt
        url
      }
    }
  }
}'
```

Filter for:
- **Specific, answerable technical questions** (not feature requests or vague asks)
- **Matches your expertise** (TypeScript, React, Three.js, Vite, Node.js, etc.)
- **Unanswered for 2+ days** (gives you time to research without racing)
- **Zero or weak existing replies** (your answer has the best chance of being marked)

### 3. Write detailed answers with code examples

Every answer should include:
- A clear explanation of the root cause
- Working code examples (not pseudocode)
- Links to relevant documentation or source code
- Alternative approaches where applicable

### 4. Post via GraphQL

```bash
# Get the discussion node ID
gh api graphql -f query='
{
  repository(owner: "OWNER", name: "REPO") {
    discussion(number: NUMBER) {
      id
      title
    }
  }
}'

# Post the answer
gh api graphql -f query='
mutation {
  addDiscussionComment(input: {
    discussionId: "NODE_ID",
    body: "YOUR ANSWER"
  }) {
    comment { url }
  }
}'
```

### 5. Track and follow up

If the author responds with a follow-up question, answer it. Engagement increases the chance of getting marked.

## What doesn't work

- **Short answers**: "Try X" without explanation. These don't get marked.
- **Stale discussions**: Questions older than 6 months where the author has likely moved on.
- **Feature requests**: These have no "answer." Only Q&A discussions qualify.
- **Crowded threads**: If 5 people have already replied with partial solutions, your answer is unlikely to stand out.
- **Repos with inactive maintainers**: If no one is marking answers, no one will mark yours.
