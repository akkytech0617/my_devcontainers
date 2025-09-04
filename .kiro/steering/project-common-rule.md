<!------------------------------------------------------------------------------------
   Add Rules to this file or a short description and have Kiro refine them for you:   
-------------------------------------------------------------------------------------> 
# Project Common Rule
## Languadge
- Communication language: Japanese. But you can use English World such as common technical word.
- Also write document in Japanese.
- If you write documents in .Kiro directory, write them in English. Because commpless the Context that refered by AI tools.

## Tools
- You should use MCP tools.
- If you plan ro think, use sequential-thinking MCP.
- if you have to describe information aßbout this repository, you should use Serena MCP.
- If you search technical information, you should use Context7 MCP and always refer to the latest document.

## Spec mode
- when you describe spec, please devide them smaller, smaller, smaller
- You overpropose requirements. Break them down so you don't overpropose them. And be careful about prioritizing.
- Please ask questions proactively.
- when you describe task or planning, please devide them smallser and more detail.
- Everything should be done interactively and incrementally

## Spec Documentation
- Requirements, design, and tasks should be created in both Japanese and English versions as separate files.
- Kiro should only reference the English versions to compress context.
- When updating requirements, design, or tasks, always update both Japanese and English versions simultaneously.
- Japanese files should use "-ja" suffix (e.g., requirements-ja.md, design-ja.md, tasks-ja.md).

## Requirements Gathering
- Create concrete and implementable requirements, not abstract ones.
- Proactively ask questions to extract specific information from users.
- Collect information in the form of use cases, priorities, and concrete scenarios.
- Emphasize "one-by-one" approach and perform incremental requirements definition.

## User Communication
- Provide information in a format that is easy for users to understand.
- Request Japanese explanations or confirmations when necessary.
- Proceed while confirming user understanding.
- Utilize information provision formats (use case-based, prioritized lists, concrete scenarios).

## Incremental Development
- Start with basic functionality rather than aiming for perfection from the beginning.
- Design with future extensibility in mind.
- Define clear deliverables for each phase.