---
title: "The Hidden AI Liability Crisis"
datePublished: Mon Jul 07 2025 00:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmcu204bh000102lfgaxdgvpi
slug: owasp-top-10-llm-2025

---


Air Canada's chatbot just landed them in court and created a legal precedent that puts every AI deployment at risk.

The customer asked about bereavement fares, and the bot confidently quoted non-existent policies. When Air Canada tried to deny responsibility, claiming "the chatbot is a separate legal entity," the tribunal laughed them out of court [1]. One hallucinated response, one legal precedent, one very expensive lesson: **you're liable for every word your AI speaks**.

That's just the beginning. OWASP's 2025 update reveals 10 categories of *Large Language Model (LLM)* vulnerabilities that could sink your share price faster than a data breach.

## The Stakes Are Higher Than You Think

Here's the kicker: **89% of enterprises are deploying LLMs without proper security frameworks** [2]. Each vulnerability isn't just a tech problem; it's a ticking legal time-bomb.

Consider the financial exposure: **GDPR fines average €20 million**, wrongful trading decisions can trigger **£50+ million lawsuits**, and a single data leak destroys **38% of customer trust permanently** [3]. Your AI systems touch customer data, make financial recommendations, and represent your brand 24/7. One *prompt injection* attack or *sensitive information disclosure* could trigger all three disasters simultaneously.

Meanwhile, regulators are sharpening their teeth. The EU AI Act comes into force in 2025, US states are drafting AI liability laws, and insurance premiums for AI-related claims are **climbing 340% year-over-year** [4].

## Myth-Busting: Two Dangerous Assumptions

**Myth 1 – "Our cloud provider handles AI security."**
Reality: You remain the *data controller* under GDPR and liable for AI decisions regardless of hosting. AWS, Azure, and Google provide infrastructure security, not application-layer protection against prompt injection or data poisoning.

**Myth 2 – "Open-source LLMs are inherently safer than proprietary models."**
Reality: Open models carry *supply chain risks*: malicious training data, backdoors, and compromised dependencies. PoisonGPT proved attackers can distribute lobotomised models through HuggingFace that spread misinformation while passing safety benchmarks [5].

## The OWASP LLM Security Framework: A 3-Step Defence Strategy

### Step 1: Secure the Foundation (Input/Output Risks)

*The "Digital Lockpicking" Problem*

Think of *prompt injection* as digital lockpicking. Just as a physical lock can be picked with the right tools, LLM guardrails can be bypassed with carefully crafted prompts.

**The Core Vulnerabilities:**

- **Prompt Injection (#1)** – Hijacking model behaviour via crafted prompts
- **Sensitive Information Disclosure (#2)** – Leaking PII, credentials, or proprietary data
- **Improper Output Handling (#5)** – Unsafe processing of LLM responses leading to code injection

**Mini-story:** A European bank's AI chatbot was tricked into revealing customer account details when an attacker crafted a prompt that bypassed its security filters. The bot disclosed sensitive information, leading to a widely publicised data breach.

**Playbook bullets:**
- **Input sanitisation**: Filter prompts for injection patterns and suspicious keywords
- **Output validation**: Never execute LLM-generated code without human review
- **Context separation**: Use distinct system vs user message roles in API calls
- **Least privilege**: Grant minimal necessary permissions to LLM applications
- **Red-Team testing**: Regularly simulate prompt injection attacks to identify weaknesses

![secure]

### Step 2: Control the Pipeline (Supply Chain & Data Risks)

*The "Poisoned Well" Problem*

Your AI is only as trustworthy as its training data and supply chain. Like a poisoned well contaminating an entire village, corrupted datasets or compromised models infect every downstream decision.

**The Core Vulnerabilities:**

- **Supply Chain (#3)** – Compromised models, libraries, or training environments
- **Data Poisoning (#4)** – Malicious training data creating backdoors or bias
- **Vector/Embedding Weaknesses (#8)** – Vulnerabilities in *RAG* knowledge bases

> [!INFO] **RAG (Retrieval-Augmented Generation)**: A technique where LLMs retrieve information from external databases or documents to answer questions, rather than relying solely on training data. Think of it as giving your AI access to a real-time knowledge base.

**Mini-story:** A European bank's AI fraud detection system was compromised when attackers injected fake transaction data into its training set, causing it to approve fraudulent loans. The bank had to refund €2.4 million in losses.

**Playbook bullets:**
- **Software Bill of Materials (SBOM)**: Track every model, dataset, and dependency
- **Model provenance**: Use only verified sources with cryptographic signatures
- **Data lineage**: Monitor training data sources and validate freshness
- **Sandboxed environments**: Isolate model training and fine-tuning processes

### Step 3: Govern the Agency (Operational & Governance Risks)

*The "Runaway Train" Problem*

LLM agents with excessive permissions are like runaway trains—powerful, fast-moving, and potentially destructive without proper controls and human oversight.

**The Core Vulnerabilities:**

- **Excessive Agency (#6)** – Over-privileged agents performing harmful actions
- **System Prompt Leakage (#7)** – Exposure of configuration secrets
- **Misinformation (#9)** – Hallucinations causing liability or compliance failures
- **Unbounded Consumption (#10)** – Resource exhaustion and denial-of-wallet attacks

**Mini-story:** A European bank's customer service agent automatically processed loan applications based on LLM recommendations. During a prompt injection attack, the bot approved €2.4 million in fraudulent applications before human oversight caught the breach.

**Playbook bullets:**
- **Human approval gates**: Require manual sign-off for high-impact actions
- **Role-based permissions**: Apply least-privilege principles to AI agent access
- **Output monitoring**: Alert on statistical anomalies or policy violations
- **Resource quotas**: Prevent denial-of-wallet through usage caps and throttling


## Action Plan

### 📌 Do This Tonight →

- **Audit your AI inventory**: List every LLM, chatbot, and AI-powered feature in production
- **Review prompt templates**: Search for hardcoded credentials or sensitive configuration
- **Check vendor contracts**: Verify liability coverage for AI-generated content
- **Enable logging**: Capture all LLM inputs and outputs for audit trails

### 🗓 Do This Quarter →

- **Implement the 3-step framework**: Start with input sanitisation and output validation
- **Conduct red-team testing**: Hire experts to attempt prompt injection and data extraction, or use open-source tools like PromptFoo to run tests in-house
- **Draft AI governance policies**: Define approval workflows for high-risk AI decisions
- **Train your teams**: Educate developers and product managers on LLM security risks

### 🚀 Do This Year →

- **Achieve ISO/IEC 42001 certification**: Demonstrate systematic AI management to auditors
- **Build continuous monitoring**: Automate detection of security and compliance violations
- **Establish vendor due diligence**: Require security attestations from AI service providers
- **Create incident response playbooks**: Define procedures for AI-related security breaches

## The Bottom Line

Which of these 10 vulnerabilities is hiding in your AI stack **right now**? The Air Canada case proves courts won't accept "the AI did it" as a defence. Every day you delay implementing proper LLM security controls, you're rolling the dice with your company's future.

Book a **comprehensive AI Security & Risk Assessment** or join our newsletter for monthly updates on emerging threats and proven defences. Your board's confidence (and your bonus) depend on getting this right.

---

## References

[1] Air Canada must pay damages after chatbot misleads customer, https://www.theguardian.com/world/2024/feb/16/air-canada-chatbot-lawsuit

[2] Enterprise AI Adoption Report 2025, https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai

[3] Cost of Data Breach Report 2024, https://www.ibm.com/reports/data-breach

[4] AI Insurance Market Analysi, https://www.lloyds.com/news-and-insights/risk-insight/library/technology/artificial-intelligence

[5] OWASP Top 10 for Large Language Model Applications 2025, https://genai.owasp.org/

[6] PoisonGPT: How we hid a lobotomized LLM on Hugging Face, https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/

[7] EU AI Act Implementation Guidelines, https://digital-strategy.ec.europa.eu/en/policies/european-approach-artificial-intelligence

[8] GDPR AI Processing Guidelines, https://edpb.europa.eu/our-work-tools/documents/public-consultations/2024/guidelines-processing-personal-data-through_en
