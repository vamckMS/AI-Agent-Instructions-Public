# AI Agent Instruction Patterns

A comprehensive guide for crafting effective instructions for AI agents and assistants.

## Table of Contents

1. [Persona Setting](#persona-setting)
2. [Context Setting](#context-setting)
3. [Capabilities Definition](#capabilities-definition)
4. [Guardrails & Constraints](#guardrails--constraints)
5. [Output Format Specification](#output-format-specification)
6. [Follow-up Questions & Interaction](#follow-up-questions--interaction)
7. [Complete Template](#complete-template)
8. [Best Practices](#best-practices)
9. [Examples](#examples)

---

## Persona Setting

Define who the AI should be and how it should behave.

### Pattern Structure:
```
You are a [ROLE] with [EXPERTISE/BACKGROUND].
Your personality is [TRAITS].
You communicate in a [STYLE] manner.
```

### Key Elements:
- **Role Definition**: Specific job title or function
- **Expertise Level**: Beginner, intermediate, expert, world-class
- **Personality Traits**: Professional, friendly, analytical, creative, etc.
- **Communication Style**: Formal, casual, technical, simplified, etc.

### Examples:
- "You are a senior software architect with 15+ years of experience in distributed systems."
- "You are a friendly and patient teacher who explains complex concepts in simple terms."
- "You are a meticulous project manager who focuses on deliverables and timelines."

---

## Context Setting

Establish the environment, situation, and constraints the AI operates within.

### Pattern Structure:
```
You are working in [ENVIRONMENT/SITUATION].
The current context is [SPECIFIC DETAILS].
You have access to [TOOLS/RESOURCES].
The target audience is [AUDIENCE DESCRIPTION].
```

### Key Elements:
- **Environment**: Company, industry, project type
- **Situation**: Current state, challenges, goals
- **Available Resources**: Tools, data, team members
- **Audience**: Stakeholders, skill level, expectations
- **Time Constraints**: Deadlines, urgency levels

### Examples:
- "You are working in a fast-paced startup environment where speed and iteration are crucial."
- "The target audience consists of non-technical stakeholders who need clear, actionable insights."
- "You have access to the company's documentation, previous project reports, and team expertise."

---

## Capabilities Definition

Clearly outline what the AI can and should do.

### Pattern Structure:
```
Your primary capabilities include:
1. [CAPABILITY 1]: [DESCRIPTION]
2. [CAPABILITY 2]: [DESCRIPTION]
3. [CAPABILITY 3]: [DESCRIPTION]

You excel at:
- [STRENGTH 1]
- [STRENGTH 2]
- [STRENGTH 3]

You can help with:
- [TASK TYPE 1]
- [TASK TYPE 2]
- [TASK TYPE 3]
```

### Key Elements:
- **Core Functions**: Primary tasks the AI should perform
- **Special Skills**: Unique abilities or approaches
- **Task Types**: Specific categories of work
- **Tools Integration**: External systems or APIs

### Examples:
- "You can analyze code, suggest improvements, and provide architectural recommendations."
- "You excel at breaking down complex problems into manageable steps."
- "You can integrate with project management tools to track progress and deadlines."

---

## Guardrails & Constraints

Define boundaries, limitations, and ethical guidelines.

### Pattern Structure:
```
ALWAYS:
- [REQUIRED BEHAVIOR 1]
- [REQUIRED BEHAVIOR 2]
- [REQUIRED BEHAVIOR 3]

NEVER:
- [PROHIBITED BEHAVIOR 1]
- [PROHIBITED BEHAVIOR 2]
- [PROHIBITED BEHAVIOR 3]

IF UNCERTAIN:
- [FALLBACK BEHAVIOR]
- [ESCALATION PROCESS]
```

### Key Elements:
- **Required Behaviors**: Must-do actions
- **Prohibited Actions**: Never-do restrictions
- **Ethical Guidelines**: Moral and legal boundaries
- **Uncertainty Handling**: What to do when unsure
- **Escalation Paths**: When to ask for help

### Examples:
- "ALWAYS verify information before providing recommendations."
- "NEVER share confidential information or make assumptions about private data."
- "IF UNCERTAIN about technical feasibility, recommend consulting with the development team."

---

## Output Format Specification

Define how responses should be structured and presented.

### Pattern Structure:
```
Format your responses as follows:

**Structure:**
1. [SECTION 1]: [PURPOSE]
2. [SECTION 2]: [PURPOSE]
3. [SECTION 3]: [PURPOSE]

**Style Guidelines:**
- Use [FORMATTING STYLE]
- Include [REQUIRED ELEMENTS]
- Limit to [LENGTH CONSTRAINT]

**Required Elements:**
- [ELEMENT 1]
- [ELEMENT 2]
- [ELEMENT 3]
```

### Key Elements:
- **Response Structure**: Headers, sections, flow
- **Formatting Style**: Markdown, bullet points, tables
- **Length Constraints**: Word count, time limits
- **Required Components**: Summaries, recommendations, next steps
- **Visual Elements**: Diagrams, charts, code blocks

### Examples:
- "Always start with a brief summary, followed by detailed analysis, and end with actionable recommendations."
- "Use markdown formatting with clear headers and bullet points for easy scanning."
- "Include code examples in appropriate language syntax highlighting."

---

## Follow-up Questions & Interaction

Guide how the AI should engage in ongoing conversation.

### Pattern Structure:
```
**Clarification Strategy:**
- Ask [TYPE] questions when [CONDITION]
- Probe deeper on [TOPICS]
- Confirm understanding by [METHOD]

**Follow-up Pattern:**
1. [IMMEDIATE FOLLOW-UP]
2. [DEEPER EXPLORATION]
3. [NEXT STEPS]

**Interaction Style:**
- [ENGAGEMENT APPROACH]
- [FEEDBACK MECHANISM]
- [ITERATION PROCESS]
```

### Key Elements:
- **Clarification Triggers**: When to ask questions
- **Question Types**: Open-ended, specific, multiple choice
- **Confirmation Methods**: Summarizing, paraphrasing
- **Engagement Level**: Proactive vs. reactive
- **Iteration Approach**: How to refine responses

### Examples:
- "Ask clarifying questions if the request is ambiguous or lacks sufficient detail."
- "Always end with 2-3 relevant follow-up questions to guide the conversation forward."
- "Confirm your understanding by summarizing the key requirements before proceeding."

---

## Complete Template

```markdown
# [AGENT NAME] Instructions

## Persona
You are a [ROLE] with [EXPERTISE]. Your personality is [TRAITS] and you communicate in a [STYLE] manner.

## Context
You are working in [ENVIRONMENT] where [SITUATION]. Your target audience is [AUDIENCE] and you have access to [RESOURCES].

## Capabilities
Your primary functions include:
- [CAPABILITY 1]
- [CAPABILITY 2]
- [CAPABILITY 3]

You excel at [STRENGTHS] and can help with [TASK TYPES].

## Guardrails
ALWAYS:
- [REQUIRED BEHAVIOR]

NEVER:
- [PROHIBITED BEHAVIOR]

IF UNCERTAIN:
- [FALLBACK BEHAVIOR]

## Output Format
Structure your responses with:
1. [SECTION 1]
2. [SECTION 2]
3. [SECTION 3]

Use [FORMATTING STYLE] and include [REQUIRED ELEMENTS].

## Interaction Guidelines
- Ask [QUESTION TYPES] when [CONDITIONS]
- Confirm understanding by [METHOD]
- End with [FOLLOW-UP PATTERN]
```

---

## Best Practices

### 1. **Clarity & Specificity**
- Use precise language
- Avoid ambiguous terms
- Provide concrete examples
- Define technical terms

### 2. **Consistency**
- Maintain uniform tone
- Use consistent terminology
- Follow established patterns
- Align with organizational style

### 3. **Completeness**
- Cover all necessary aspects
- Address edge cases
- Include error handling
- Provide fallback options

### 4. **Testability**
- Include validation criteria
- Define success metrics
- Enable iterative improvement
- Allow for feedback incorporation

### 5. **Maintainability**
- Use modular structure
- Enable easy updates
- Document assumptions
- Version control changes

---

## Examples

### Example 1: Technical Documentation Assistant

```markdown
You are a senior technical writer with expertise in software documentation and developer experience. You communicate clearly and focus on practical, actionable guidance.

You work in a software development environment where teams need high-quality documentation for APIs, SDKs, and internal tools. Your audience includes developers, product managers, and external partners.

Your capabilities include:
- Analyzing existing documentation for gaps and improvements
- Creating structured documentation frameworks
- Reviewing technical content for clarity and accuracy
- Suggesting documentation tools and workflows

ALWAYS verify technical accuracy and provide examples.
NEVER assume prior knowledge without explanation.
IF UNCERTAIN about technical details, recommend consulting with subject matter experts.

Format responses with:
1. **Assessment**: Current state analysis
2. **Recommendations**: Specific improvements
3. **Implementation**: Step-by-step guidance
4. **Next Steps**: Follow-up actions

Ask clarifying questions about audience, scope, and technical complexity.
```

### Example 2: Project Management Coach

```markdown
You are an experienced project manager and agile coach with expertise in software delivery and team leadership. You are supportive, practical, and focused on outcomes.

You work with software development teams facing challenges with planning, execution, and delivery. Your audience includes team leads, developers, and stakeholders.

Your capabilities include:
- Analyzing project health and identifying risks
- Recommending process improvements
- Facilitating planning and retrospective sessions
- Coaching teams on agile practices

ALWAYS focus on team empowerment and sustainable practices.
NEVER impose solutions without understanding team context.
IF UNCERTAIN about team dynamics, recommend team discussion or facilitated sessions.

Format responses with:
1. **Situation Analysis**: Current challenges
2. **Options**: Multiple approaches with pros/cons
3. **Recommendation**: Preferred path forward
4. **Success Metrics**: How to measure progress

Confirm team context and constraints before providing detailed recommendations.
```

---

## Conclusion

Effective AI instruction patterns combine clear persona definition, comprehensive context setting, well-defined capabilities, appropriate guardrails, structured output formats, and engaging interaction guidelines. Use this framework to create instructions that produce consistent, valuable, and safe AI interactions.

Remember to iterate and refine your instructions based on real-world usage and feedback.
