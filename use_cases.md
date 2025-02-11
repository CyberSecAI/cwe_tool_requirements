

# **Root Cause Mapping “Use Cases”**  

This content is extracted RCM-WG-250129.pptx which will be available https://github.com/Root-Cause-Mapping-Working-Group/RCM-WG/tree/main/meeting_slides

---

## **Possible Use Cases for a “CWE Support” Tool**
- **Root cause mapping:** Helps users accurately map root causes to CWE entries.
- **Learning assistance:** Guides users in understanding vulnerabilities and conducting root cause analysis.
- **Weakness comprehension:** Provides explanations of CWE weaknesses, common consequences, and code examples.
- **Navigational support:** Helps users explore CWE views, hierarchies, and relationships.

---

## **Context of a Root Cause Mapping Assistance Tool**
- Understanding **why** someone would use an LLM or mapping assistance tool.
- **Factors influencing use:**
  - **Activity:** What the user is doing that requires CWE mapping.
  - **Available Information:** What data the user has for mapping decisions (Slack messages, emails, reports, etc.).
  - **Coding Knowledge:** Skill level and familiarity with vulnerabilities.
  - **Time Pressures:** Urgency of mapping decisions.
  - **Correctness Importance:** How crucial it is to get the mapping correct.
- Information varies **person-to-person, role-to-role** (e.g., a PSIRT member vs. a developer).

---



## **User Scenarios**


| Role | Available Information | Coding Knowledge | Time Pressures | Correctness Importance | Additional Considerations/Special Case |
|------|---------------------|------------------|----------------|----------------------|----------------------------------|
| PSIRT Member Writing a Vulnerability Advisory | Bug reports, issue summaries, access to developers | General knowledge of vulnerability types and programming | High | TBD (considering "correctness" vs. "completeness") | - |
| Developer Fixing a Vulnerability | Source code, patches, bug reports, test results, proof-of-concept exploits | Strong understanding of programming, protocols, technology stacks, and vulnerabilities | High | Low (focus is on fixing issues rather than precise CWE mapping) | Developers may learn of CWE mappings through external reports or tools |
| Vulnerability Researcher / Bug Bounty Hunter Reporting Vulnerabilities | May not have direct access to source code or patches | Varies widely depending on expertise | Minimal | Moderate | Academic researchers mapping findings as part of their research |
| Product Manager Addressing Weaknesses at Scale | Bug reports, issue summaries, custom in-house weakness taxonomies | - | - | - | Likely dependent on correct mappings but does not do direct mapping themselves |


---

## **Mapping Bot – Next Steps**
- **Development Priorities:**
  - **Walkthrough mockups**.
  - **Determine requirements:**
    - Prevent incorrect CWE name hallucinations.
    - Prevent malicious manipulation of bot.
    - Ensure precise, accurate CWE recommendations.
  - **Enable backtracking and specificity improvements.**
  - **Confidentiality:** Prevent data leaks.
  - **User Guidance:** Provide accurate responses and transparent reasoning.
  - **Explain decisions:** Show why a particular CWE was chosen over another.
  - **Identify the user base:** Define target users for implementation.
  - **Technical Considerations:** General architecture and implementation choices.
  - **Minimum viable product definition.**


# User Interaction

## Questions

1. Is there AuthN/AuthZ or can ANY user use?
2. Is a user's context preserved e.g. when a user accesses the tool, do they see their previous context?
3. How does a user report an error or provide feedback in general to improve the system?
   1. how is this fedback into the tool?
4. Is the tool available via an API, or browser only / interactive use?
5. Note: the tool should be resilient i.e. auto-fallover if a model becomes inaccessible

TODO: add user scenarious for tool maintainer to:
1. update the knowledge base e.g. to new version of CWE list
2. update the knowledge base e.g. based on user input, responses, and logs



## Model

```mermaid

sequenceDiagram
    participant U as User
    participant T as CWE Support Tool
    participant K as Knowledge Base
    
    Note over U,T: Initial Input Phase
    U->>+T: Submits Available Information
    Note right of U:  - Vulnerability Description<br/> - Bug reports<br/>- Source code<br/>- Issue summaries<br/>- Patch information<br/>- Test results<br/>- PoC exploits
    
    T->>K: Query CWE Database
    K-->>T: Retrieve Relevant CWEs
    
    Note over T: Analysis Phase
    T->>T: Process Information
    
    T-->>U: Initial Assessment
    Note left of T: - Potential CWE mappings<br/>- Confidence levels<br/>- Related weaknesses
    
    Note over U,T: Interactive Q&A Phase
    rect rgb(290, 290, 290)
        U->>T: Clarifying Question
        T->>K: Reference Knowledge
        K-->>T: Retrieved Context
        T-->>U: Targeted Response
        Note right of T: - Explanation of choice<br/>- Comparative analysis<br/>- Code examples
    end
    
    Note over U,T: Refinement Loop
    loop Until Satisfactory Mapping
        U->>T: Additional Context
        T->>T: Refine Analysis
        T-->>U: Updated Mapping
    end
    
    Note over U,T: Final Output
    T-->>-U: Final Recommendation
    Note right of T: - Confirmed CWE mapping<br/>- Detailed rationale<br/>- Related resources<br/>- Mitigation suggestions <br/>-  reference links to cwe.mitre.org site

```` 


## With Guardrails and Logging




```mermaid

sequenceDiagram
    participant U as User
    participant G as Input Guardrails
    participant T as CWE Support Tool
    participant K as Knowledge Base
    participant L as Logging System
    participant O as Output Validator
    
    Note over U,O: Secure Input Phase
    U->>+G: Submit Information
    Note right of U: Raw Input:<br/>- Vulnerability Description<br/>- Bug reports<br/>- Source code
    
    G->>L: Log input attempt
    G->>G: Validate & Sanitize Input
    Note right of G: - Check for malicious content<br/>- Remove sensitive data<br/>- Verify input format
    
    G->>T: Sanitized Input
    T->>L: Log validated input
    
    T->>K: Query CWE Database
    K-->>T: Retrieve Relevant CWEs
    T->>L: Log CWE query results
    
    Note over T: Analysis Phase
    T->>T: Process Information
    T->>L: Log analysis steps
    
    T->>O: Proposed Response
    O->>O: Validate Output
    Note right of O: - Verify CWE accuracy<br/>- Check for data leaks<br/>- Ensure response quality
    
    O-->>U: Safe Initial Assessment
    O->>L: Log output delivery
    
    Note over U,O: Secure Q&A Phase
    rect rgb(240, 240, 240)
        U->>G: Ask Question
        G->>G: Validate Question
        G->>T: Safe Question
        T->>K: Reference Knowledge
        K-->>T: Retrieved Context
        T->>O: Proposed Answer
        O->>O: Validate Answer
        O-->>U: Safe Response
        Note right of O: Each Q&A interaction logged
    end
    
    Note over U,O: Refinement with Logging
    loop Until Satisfactory Mapping
        U->>G: Additional Context
        G->>L: Log refinement attempt
        G->>T: Validated Context
        T->>T: Refine Analysis
        T->>O: Updated Mapping
        O->>L: Log refinement result
        O-->>U: Validated Update
    end
    
    Note over U,O: Secure Final Output
    T->>O: Final Recommendation
    O->>O: Final Validation
    Note right of O: - Verify final CWE accuracy<br/>- Check completeness<br/>- Validate links & resources
    O-->>U: Secure Final Output
    O->>L: Log session completion
    
    Note over L: Continuous Logging
    Note right of L: Log entries include:<br/>- Timestamp<br/>- User ID (anonymized)<br/>- Action type<br/>- Input/Output summary<br/>- Validation results<br/>- Error states
```` 
