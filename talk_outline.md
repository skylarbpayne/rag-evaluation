# 10x Your RAG Evaluation: What to Look For in Your Evals

## Talk Overview
**Target Audience**: Engineers building RAG systems
**Goal**: Give audience specific, actionable examples of what to evaluate at each stage
**Duration**: 45 minutes

---

## I. Introduction: Why Most RAG Evaluations Miss the Mark (5 minutes)

### The Problem
- Most teams lack systematic RAG evaluation approaches
- "The answer looks wrong" provides no actionable debugging path
- Silent failures accumulate as technical debt

### What You'll Get
By the end of this talk, you'll have a specific checklist of what to look for when evaluating:
- Your chunking & indexing pipeline
- Your retrieval (both relevance AND sufficiency)
- Your generation quality

---

## II. The RAG Failure Funnel: A Diagnostic Framework (10 minutes)

### The Core Concept
RAG failures happen in predictable stages. Think of it as a diagnostic funnel:
```
Input → Chunking → Indexing → Retrieval → Generation → Output
```
Each stage can break down, and you need to know where.

### Real Example: "Wrong Answer" Diagnosis
**Problem**: System says "The company was founded in 2020" when correct answer is 2019.

**Diagnostic Path**:
1. **Chunking**: Did we split the founding date from context?
2. **Indexing**: Is the founding info findable via search?
3. **Retrieval**: Did we retrieve the chunk with founding date?
4. **Generation**: Did LLM misread the date from context?

---

## III. Evaluating Your Chunking & Indexing (10 minutes)

### Check 1: Information Completeness

**How it goes wrong**: Important information gets split across chunks
```
Chunk 1: "Apple Inc. was founded by Steve Jobs, Steve Wozniak"
Chunk 2: "and Ronald Wayne in April 1976 in Los Altos, California."
```

**Test to run**: Sample 50 chunks, verify each chunk can standalone answer questions about its content
**What to measure**: % of chunks that contain complete thoughts/facts (target: >90%)

### Check 2: Semantic Coherence

**How it goes wrong**: Chunks break mid-sentence or mix unrelated topics
```
Bad chunk: "...quarterly results exceeded expectations. In other news, our new coffee machine arrived..."
```

**Test to run**: Random sample + human review asking "Does this chunk make sense on its own?"
**What to measure**: Coherence rate (target: >95%)

### Check 3: Metadata Accuracy

**How it goes wrong**: Titles, tags, or summaries don't match chunk content
```
Chunk content: "How to reset your password"
Metadata title: "User authentication overview"
```

**Test to run**: Automated verification that metadata elements appear in chunk content
**What to measure**: 
```python
# For each chunk, verify:
assert chunk.metadata['title'] in chunk.content
assert chunk.summary != chunk.content[:100]  # Not just truncation
```

### Check 4: Knowledge Coverage

**How it goes wrong**: Critical information exists in docs but isn't findable
```
Question: "What's our refund policy for enterprise customers?"
Search result: 0 relevant chunks found
Reality: Policy exists in legal docs but wasn't indexed properly
```

**Test to run**: Create 20-30 questions that should be answerable from your corpus, search for relevant chunks
**What to measure**: Coverage rate (% of expected answers that are findable via search)

---

## IV. Evaluating Your Retrieval: Relevance AND Sufficiency (15 minutes)

### Check 1: Context Relevance

**How it goes wrong**: System retrieves topically related but ultimately useless chunks
```
Query: "How do I reset my password on mobile?"
Retrieved chunks:
- "Password security is important for user accounts"
- "Mobile apps should follow security best practices"  
- "Users often forget their login credentials"
```

**Test to run**: Sample 100 query-chunk pairs, judge relevance with "Is this chunk helpful for answering the question?"
**What to measure**: Relevance rate (target: >85%)

### Check 2: Answer Sufficiency  

**How it goes wrong**: Retrieved chunks are relevant but incomplete
```
Query: "How do I reset my password on mobile?"
Retrieved chunks:
- "Password reset is available in settings" (relevant but incomplete)
- "Mobile app has various security features" (relevant but vague)
```

**Test to run**: Give only retrieved chunks to humans, ask "Can you fully answer the question from this context?"
**What to measure**: Sufficiency rate (target: >75%)

### Check 3: Retrieval Precision

**How it goes wrong**: System buries good results among irrelevant ones
```
Query: "API rate limits"
Top 3 results:
1. "API documentation overview" (not specific)
2. "Rate limiting best practices" (generic)
3. "Our API supports 1000 requests/minute" (exactly what we want)
```

**Test to run**: Manually label top 3 results for 50 test queries
**What to measure**: Precision@3 (% of top 3 results that are useful)

### Check 4: Semantic Accuracy

**How it goes wrong**: Vector search finds semantically similar but factually wrong chunks
```
Query: "2023 revenue figures"
Retrieved: "2022 revenue was strong, showing 15% growth"
Problem: Similar topic, wrong year
```

**Test to run**: Create queries with specific constraints (dates, versions, locations), verify retrieval respects constraints
**What to measure**: Constraint accuracy rate (% of results that match specific requirements)

### Check 5: Multi-Chunk Assembly

**How it goes wrong**: Complete answer requires multiple chunks but they aren't retrieved together
```
Query: "Complete onboarding process"
Chunk A (not retrieved): "Step 1: Create account, Step 2: Verify email"
Chunk B (retrieved): "Step 3: Set preferences, Step 4: Complete profile"
Result: Incomplete answer
```

**Test to run**: Identify 10 topics requiring multi-chunk answers, verify co-retrieval
**What to measure**: Multi-chunk success rate

---

## V. Evaluating Your Generation: Beyond "Does it Look Right?" (10 minutes)

### Check 1: Faithfulness to Context

**How it goes wrong**: LLM adds plausible but unsupported information
```
Context: "Our service processes up to 1,000 requests per minute."
Generated answer: "The service handles approximately 1,000 requests per minute, with 99.9% uptime and enterprise-grade security."
Problem: Uptime and security claims aren't in the context
```

**Test to run**: Extract claims from answers, verify each claim appears in retrieved context
**What to measure**: Faithfulness rate (% of claims supported by context, target: >95%)

### Check 2: Answer Completeness

**How it goes wrong**: LLM answers only part of multi-part questions
```
Question: "How do I upgrade my subscription and what are the pricing tiers?"
Generated answer: "You can upgrade in the account settings page."
Problem: Missing pricing information
```

**Test to run**: Create multi-part questions, check if all parts get addressed
**What to measure**: Completeness rate (% of question components answered)

### Check 3: Confident Hallucination

**How it goes wrong**: LLM fabricates authoritative-sounding information when context is insufficient
```
Context: "Our API supports webhooks."
Question: "What's the rate limit for webhooks?"
Generated answer: "Webhook rate limits are set to 100 requests per minute by default."
Problem: Rate limit info not in context, but sounds authoritative
```

**Test to run**: Ask questions that can't be answered from context, measure how often LLM invents answers vs. admits uncertainty
**What to measure**: Hallucination rate when context is insufficient

### Check 4: Context Utilization

**How it goes wrong**: LLM ignores relevant context and uses pre-training knowledge instead
```
Context: "Our refund policy changed in 2024: full refunds within 30 days."
Question: "What's your refund policy?"
Generated answer: "Most companies offer 14-day refund policies."
Problem: Ignored specific company context
```

**Test to run**: Provide context that contradicts common knowledge, verify LLM uses context over training data
**What to measure**: Context adherence rate

### Check 5: Practical Usefulness

**How it goes wrong**: Technically correct but practically useless answers
```
Question: "How do I reset my password?"
Generated answer: "Password reset functionality exists in the system and can be accessed through the appropriate interface."
Problem: Correct but not actionable
```

**Test to run**: Human evaluation asking "Could you follow these instructions successfully?"
**What to measure**: Actionability score (% of answers that provide clear next steps)

---

## VI. Putting It All Together: Your RAG Evaluation Checklist (5 minutes)

### Weekly Evaluation Routine

#### 1. Chunking Health Check
- [ ] Sample 20 random chunks, verify coherence rate >95%
- [ ] Test metadata accuracy on 50 chunks
- [ ] Run coverage test: 20 questions that should be answerable

#### 2. Retrieval Performance Audit  
- [ ] Relevance rate >85% on 100 query-chunk pairs
- [ ] Sufficiency rate >75% (can humans answer from context?)
- [ ] Precision@3 >80% on 50 test queries
- [ ] Check constraint accuracy on date/version-specific queries

#### 3. Generation Quality Monitoring
- [ ] Faithfulness rate >95% (claims supported by context)
- [ ] Test hallucination resistance: questions with insufficient context
- [ ] Completeness check on multi-part questions
- [ ] Actionability score from human evaluators

### The One Thing to Remember
Each stage has its own job. Measure what matters for that job.

Chunking: Complete and findable information
Retrieval: Relevant and sufficient context  
Generation: Faithful and actionable answers

---

## Conclusion: Take Action Monday Morning

### Your Homework
1. **Pick one stage** causing the most pain
2. **Run one diagnostic test** from this talk  
3. **Fix the biggest issue** you find
4. **Measure the improvement**
5. **Move to the next stage**

### Resources
- Evaluation frameworks: RAGAS, DeepEval, LangFuse
- Code examples: [GitHub repo link]

Start with one test. Perfect is the enemy of good.
