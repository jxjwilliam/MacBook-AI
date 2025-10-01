# BMAD-METHOD

- "Breakthrough Method for Agentic Development” or “Breakthrough Method for Agile AI-Driven Development.”
- Agentic (代理的; 代理的，像代理人的；（心理学）代理人的，服从权威的；（心理学）与表现或地位有关的；（心理学）主体的) Planning
- Context-Engineered Development

## Agents

- analyst
- architect
- bmad-master
- bmad-orchestrator
- dev
- pm
- po
- qa
- sm
- ux-expert


```mermaid
graph TD
    subgraph "Phase 1: Agentic Planning (Specification & Design)"
        A[Start: BMad Orchestrator] --> B[Analyst: Project Brief]
        B --> C[PM/PO: Product Requirements Document PRD]
        C --> D[Architect: Architecture Document]
        D --> E[UX-Expert: Optional Design Input]
    end

    subgraph "Phase 2: Context-Engineered Development (Implementation)"
        D --> F[Scrum Master: Shard Epics into User Stories]
        F -- 1.1-browse-books.md --> G{Developer: Implement User Story}
        G --> H{QA Agent: Test User Story}
        H --> I{Scrum Master: Update Status / Next Story}
        I -- Repeat for remaining stories --> G
    end

    A --> Z[End: Project Complete]

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#add8e6,stroke:#333,stroke-width:1px
    style C fill:#add8e6,stroke:#333,stroke-width:1px
    style D fill:#add8e6,stroke:#333,stroke-width:1px
    style E fill:#f0e68c,stroke:#333,stroke-width:1px
    style F fill:#90ee90,stroke:#333,stroke-width:1px
    style G fill:#98fb98,stroke:#333,stroke-width:1px
    style H fill:#98fb98,stroke:#333,stroke-width:1px
    style I fill:#90ee90,stroke:#333,stroke-width:1px
    style Z fill:#f9f,stroke:#333,stroke-width:2px

    linkStyle 0 stroke-width:2px,fill:none,stroke:green;
    linkStyle 1 stroke-width:2px,fill:none,stroke:green;
    linkStyle 2 stroke-width:2px,fill:none,stroke:green;
    linkStyle 3 stroke-width:1px,fill:none,stroke:gray,stroke-dasharray: 5 5;
    linkStyle 4 stroke-width:2px,fill:none,stroke:blue;
    linkStyle 5 stroke-width:2px,fill:none,stroke:blue;
    linkStyle 6 stroke-width:2px,fill:none,stroke:blue;
    linkStyle 7 stroke-width:2px,fill:none,stroke:blue;
    linkStyle 8 stroke-width:1px,fill:none,stroke:purple,stroke-dasharray: 5 5;

```
