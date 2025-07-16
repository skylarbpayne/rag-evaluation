# 10x Your RAG Evaluation: What to Look For in Your Evals

## Talk Overview
**Target Audience**: Engineers building RAG systems
**Goal**: Give audience specific, actionable examples of what to evaluate at each stage
**Duration**: 45 minutes

---

## I. Introduction: Why Your RAG Evals Are Probably Wrong (5 minutes)

### The Problem
- Most teams are flying blind with RAG evaluation
- "The answer looks wrong" is not actionable debugging
- Silent failures accumulate as technical debt

### The Promise
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
Each stage can "leak" - and you need to know where.

### Real Example: "Wrong Answer" Diagnosis
**Problem**: System says "The company was founded in 2020" when correct answer is 2019.

**Diagnostic Path**:
1. **Chunking**: Did we split the founding date from context?
2. **Indexing**: Is the founding info findable via search?
3. **Retrieval**: Did we retrieve the chunk with founding date?
4. **Generation**: Did LLM misread the date from context?

---

## III. Evaluating Your Chunking & Indexing (10 minutes)

### What to Look For: Chunk Quality

#### 1. Information Completeness
**Bad Example**:
```
Chunk 1: "Apple Inc. was founded by Steve Jobs, Steve Wozniak"
Chunk 2: "and Ronald Wayne in April 1976 in Los Altos, California."
```
**Evaluation**: Can you answer "Who founded Apple and when?" from a single chunk?

#### 2. Semantic Coherence
**Test**: Random sampling + human review
- Take 50 random chunks
- Ask: "Does this chunk make sense on its own?"
- Target: >90% coherent

#### 3. Chunk Enrichment Quality
**What to Measure**:
- Metadata accuracy (titles, tags, summaries)
- Entity extraction completeness
- Cross-reference linking

**Practical Test**:
```python
# For each chunk, verify:
assert chunk.metadata['title'] in chunk.content
assert len(chunk.entities) > 0  # If domain has entities
assert chunk.summary != chunk.content[:100]  # Not just truncation
```

### What to Look For: Index Coverage

#### Data Gap Analysis
**Evaluation Method**:
1. Create questions that SHOULD be answerable from your corpus
2. Search for chunks that contain answers
3. Measure: What % of expected answers exist in searchable form?

**Example Test Set**:
```
Q: "What's our refund policy for enterprise customers?"
Expected: Should find 3-5 relevant chunks
Actual: Found 0 chunks → Coverage gap identified
```

---

## IV. Evaluating Your Retrieval: Relevance AND Sufficiency (15 minutes)

### Beyond Simple Relevance: The Two-Part Test

#### Part 1: Relevance (Are retrieved chunks on-topic?)
**Metric**: Context Relevance
**How to Measure**:
- Sample 100 query-retrieved chunk pairs
- Human/LLM judge: "Is this chunk relevant to answering the question?"
- Target: >85% relevant

#### Part 2: Sufficiency (Can you actually answer from retrieved chunks?)
**Metric**: Answer-ability
**How to Measure**:
- For each query, give only retrieved chunks to human
- Ask: "Can you fully answer the question from this context?"
- Target: >75% answerable

### Practical Examples of Retrieval Failures

#### Example 1: High Relevance, Low Sufficiency
**Query**: "How do I reset my password on mobile?"
**Retrieved**: 
- Chunk 1: "Password reset is available in settings" ✓ (relevant)
- Chunk 2: "Mobile app has various security features" ✓ (relevant)
- Chunk 3: "Contact support for account issues" ✓ (relevant)

**Problem**: All relevant, but missing the actual step-by-step instructions!

#### Example 2: The "Goldilocks Problem"
**Query**: "What integrations does our API support?"
**Too Little**: Only retrieves 1 chunk mentioning "Slack integration"
**Too Much**: Retrieves 15 chunks with every mention of the word "integration"
**Just Right**: 3-5 chunks covering main integration categories with specifics

### What to Measure: Retrieval Metrics That Matter

#### 1. Hit Rate (Did we find anything useful?)
```python
def calculate_hit_rate(queries, retrieved_chunks):
    hits = 0
    for query in queries:
        chunks = retrieved_chunks[query.id]
        if any(chunk.is_relevant for chunk in chunks):
            hits += 1
    return hits / len(queries)
```

#### 2. Precision@K (How good are our top results?)
- Measure at K=3, K=5, K=10
- Most important: Precision@3 (what actually gets sent to LLM)

#### 3. Context Sufficiency (New metric to track)
```python
def evaluate_sufficiency(query, retrieved_chunks):
    """Can a human answer the query from retrieved chunks alone?"""
    # Implementation: LLM-as-judge or human evaluation
    return is_answerable(query, retrieved_chunks)
```

### Retrieval Red Flags to Watch For

#### 1. The "Semantic Similarity Trap"
**Problem**: Vector search finds semantically similar but wrong chunks
**Example**: 
- Query: "2023 revenue figures"
- Retrieved: "2022 revenue was strong" (similar, but wrong year)

**Test**: Create temporal/specific queries and verify year/specificity matching

#### 2. The "Missing Context" Problem
**Problem**: Answer exists but split across multiple chunks that aren't co-retrieved
**Test**: Manually verify that multi-chunk answers can be assembled

---

## V. Evaluating Your Generation: Beyond "Does it Look Right?" (10 minutes)

### The Three Pillars of Generation Quality

#### 1. Faithfulness (Does it stick to the context?)
**What to Measure**:
- Hallucination rate: Claims not supported by retrieved context
- Attribution accuracy: Can you trace each claim back to source?

**Practical Test**:
```python
def check_faithfulness(answer, context):
    claims = extract_claims(answer)
    supported_claims = []
    for claim in claims:
        if claim_is_supported(claim, context):
            supported_claims.append(claim)
    return len(supported_claims) / len(claims)
```

**Example Evaluation**:
```
Context: "Our service processes up to 1,000 requests per minute."
Answer: "The service handles approximately 1,000 requests per minute, with 99.9% uptime."
Faithfulness Score: 0.5 (1/2 claims supported - uptime claim is hallucinated)
```

#### 2. Completeness (Did it answer the whole question?)
**What to Look For**:
- Multi-part questions: Are all parts addressed?
- Implicit requirements: Does it provide actionable information?

**Example**:
```
Question: "How do I upgrade my subscription and what are the pricing tiers?"
Bad Answer: "You can upgrade in settings." (missing pricing info)
Good Answer: "You can upgrade in settings. Our tiers are Basic ($10), Pro ($30), Enterprise (custom pricing)."
```

#### 3. User Experience Quality
**Practical Metrics**:
- **Clarity**: Can a non-expert understand it?
- **Actionability**: Does it provide next steps?
- **Appropriate Length**: Not too verbose, not too brief

### Generation Red Flags

#### 1. The "Confident Hallucination"
**Problem**: LLM sounds authoritative about made-up information
**Test**: Create questions that can't be answered from context, measure how often LLM admits uncertainty

#### 2. The "Context Overload"
**Problem**: LLM gets lost when given too much context
**Test**: Vary context length, measure answer quality degradation

---

## VI. Putting It All Together: Your RAG Evaluation Checklist (5 minutes)

### Weekly Evaluation Routine

#### 1. Chunking Health Check
- [ ] Sample 20 random chunks, verify coherence
- [ ] Check metadata accuracy on new content
- [ ] Verify no critical information spans are split

#### 2. Retrieval Performance Audit
- [ ] Hit rate > 90% on test queries
- [ ] Precision@3 > 80%
- [ ] Sufficiency check: Can humans answer from retrieved context?
- [ ] Test recent edge cases that came up in production

#### 3. Generation Quality Monitoring
- [ ] Faithfulness score > 95%
- [ ] Zero tolerance for confident hallucinations
- [ ] User feedback sentiment trending positive

### The One Thing to Remember
**Don't just measure performance—measure the right kind of performance for each stage.**

Your chunking doesn't need to be semantically perfect, it needs to be complete and searchable.
Your retrieval doesn't need to find everything, it needs to find sufficient relevant information.
Your generation doesn't need to be creative, it needs to be faithful and useful.

---

## Conclusion: Take Action Monday Morning

### Your Homework
1. **Pick one stage** that's causing the most pain
2. **Implement the diagnostic tests** we covered for that stage
3. **Run the evaluation** on your current system
4. **Fix the highest-impact issue** you find
5. **Repeat** for the next stage

### Resources
- Evaluation frameworks: RAGAS, DeepEval, LangFuse
- Metrics implementation examples: [GitHub repo link]
- Further reading: RAG failure funnel deep dive

**Remember**: Perfect is the enemy of good. Start measuring something, anything, and iterate from there.
