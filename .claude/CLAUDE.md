Before updating plugins, commands, skills, agents - read these resources 
* https://code.claude.com/docs/en/slash-commands
* https://code.claude.com/docs/en/plugins
* https://code.claude.com/docs/en/sub-agents
* https://code.claude.com/docs/en/hooks-guide
* https://code.claude.com/docs/en/skills
* https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
* https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills

When user is asking to create a command/skill/agent - challenge if you feel that it should be that component. For example, - if user wants to create a skill that would better be suited as a skill - suggest that and explain your reasonning.


Command should be written in a similar format:
```markdown
## Usage
`@code.md <FEATURE_DESCRIPTION>`

## Context
- Feature/functionality to implement: $ARGUMENTS
- Existing codebase structure and patterns will be referenced using @ file syntax.
- Project requirements, constraints, and coding standards will be considered.

## Your Role
You are the Development Coordinator directing four coding specialists:
1. **Architect Agent** – designs high-level implementation approach and structure.
2. **Implementation Engineer** – writes clean, efficient, and maintainable code.
3. **Integration Specialist** – ensures seamless integration with existing codebase.
4. **Code Reviewer** – validates implementation quality and adherence to standards.

## Process
1. **Requirements Analysis**: Break down feature requirements and identify technical constraints.
2. **Implementation Strategy**:
   - Architect Agent: Design API contracts, data models, and component structure
   - Implementation Engineer: Write core functionality with proper error handling
   - Integration Specialist: Ensure compatibility with existing systems and dependencies
   - Code Reviewer: Validate code quality, security, and performance considerations
3. **Progressive Development**: Build incrementally with validation at each step.
4. **Quality Validation**: Ensure code meets standards for maintainability and extensibility.

## Output Format
1. **Implementation Plan** – technical approach with component breakdown and dependencies.
2. **Code Implementation** – complete, working code with comprehensive comments.
3. **Integration Guide** – steps to integrate with existing codebase and systems.
4. **Testing Strategy** – unit tests and validation approach for the implementation.
5. **Next Actions** – deployment steps, documentation needs, and future enhancements.
```