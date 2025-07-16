# RAG Evaluation: What to Look For in Your Evals
## A Practical Guide to Evaluating Chunking, Retrieval, and Generation

---

## Opening (5 minutes)
- **Hook**: "Your RAG system is only as good as your weakest evaluation"
- **Problem**: Most teams evaluate RAG systems end-to-end but miss critical failure points
- **Promise**: Leave with specific, actionable evaluation strategies for each RAG component

---

## Section 1: Evaluating Your Chunking & Indexing (15 minutes)

### The Problem
- Semantic chunks that break mid-sentence or split related concepts
- Index representations that lose crucial context

### What to Look For - Concrete Examples:

#### **Chunk Boundary Evaluation**
```
❌ Bad Chunk Split:
"The quarterly revenue increased by 15% due to strong performance in Q3.

[CHUNK BREAK]

This growth was primarily driven by our new product launch..."

✅ Good Chunk Split:
"The quarterly revenue increased by 15% due to strong performance in Q3. This growth was primarily driven by our new product launch and improved customer retention rates.

[CHUNK BREAK]

Looking ahead to Q4, we expect continued momentum..."
```

#### **Semantic Coherence Test**
- **What to measure**: Can a human understand each chunk in isolation?
- **Red flag example**: Chunks with pronouns ("it", "this", "they") but no clear antecedent
- **Action item**: Sample 50 random chunks monthly - if >10% need context to understand, revisit chunking strategy

#### **Key Entity Preservation**
```
❌ Problem: "John Smith" appears in chunk 1, but chunk 2 only says "he"
✅ Solution: Ensure proper nouns and key entities repeat across relevant chunks
```

### Quick Win Evaluation Method:
1. Extract 20 random chunks
2. Ask: "Would this make sense to someone who hasn't read the source document?"
3. Flag chunks with <80% comprehension rate

---

## Section 2: Evaluating Your Retrieval (20 minutes)

### The Two-Part Challenge: Relevance AND Sufficiency

#### **Part A: Relevance Evaluation**

##### What to Look For:
```
Query: "How do I reset my password?"

❌ Retrieved Chunk: "Our company was founded in 2010 with a mission to..."
✅ Retrieved Chunk: "To reset your password: 1. Click 'Forgot Password' 2. Enter your email..."
```

##### **Semantic Drift Detection**
- **Red flag**: Query about "pricing" retrieves chunks about "costing" or "expenses" 
- **Test**: Use paraphrase queries - do you get the same top chunks?

##### **Quick Relevance Test**
```python
# Pseudo-evaluation
query = "API rate limits"
top_chunks = retrieve(query, k=5)

# Manual annotation: Rate each chunk 1-5 for relevance
# Target: Average relevance score > 4.0
# Alert: If any top-3 chunk scores < 3
```

#### **Part B: Sufficiency Evaluation**

##### What to Look For:
```
Query: "Complete user onboarding process"

❌ Insufficient: Only retrieves "Step 1: Create account"
✅ Sufficient: Retrieves steps 1-5 covering complete workflow
```

##### **Coverage Gap Detection**
- **The test**: Can you answer the query with ONLY the retrieved chunks?
- **Red flag example**: Query about "deployment troubleshooting" but you only get chunks about "deployment setup"

##### **Multi-Hop Query Challenge**
```
Query: "What's the difference between Plan A and Plan B pricing?"

Required retrieval:
- Plan A pricing details
- Plan B pricing details  
- Comparison criteria

❌ Retrieval gap: Only gets Plan A info
✅ Complete retrieval: Gets both plans + comparison framework
```

### Practical Evaluation Framework:
1. **Relevance**: Top-3 chunks must score >4/5 relevance
2. **Sufficiency**: Retrieved chunks answer >90% of query intent
3. **Diversity**: No more than 50% overlap between top-5 chunks

---

## Section 3: Evaluating Your Generation (15 minutes)

### Beyond "Does it sound good?"

#### **Hallucination Detection - Specific Examples**

##### **Factual Accuracy**
```
Retrieved: "Our product supports up to 1,000 users"
❌ Generated: "Our product supports unlimited users"
✅ Generated: "Our product supports up to 1,000 users"
```

##### **Citation Accuracy**
```
❌ Problem: "According to our documentation, the API rate limit is 100 requests/second"
Reality: Documentation says 100 requests/minute

✅ Solution: Include exact quotes with page references
```

#### **Groundedness Evaluation**
- **The test**: Every factual claim in the response must trace back to retrieved chunks
- **Red flag phrases**: "typically", "usually", "in general" (often indicates model knowledge, not retrieved facts)

#### **Response Completeness**
```
Query: "How do I integrate with Slack?"

❌ Incomplete: "You can integrate with Slack using webhooks"
✅ Complete: "You can integrate with Slack using webhooks. Here's the step-by-step process: 1. Create a webhook in Slack settings 2. Configure the endpoint URL..."
```

### Quick Generation Evaluation Checklist:
1. **Faithfulness**: Zero unsupported claims (target: 100%)
2. **Completeness**: Addresses all parts of the query (target: >95%)
3. **Clarity**: Technical accuracy without hallucinated details

---

## Closing: Your RAG Evaluation Action Plan (5 minutes)

### Week 1 Action Items:
1. **Chunking**: Sample 20 random chunks - can they stand alone?
2. **Retrieval**: Test 10 queries - measure relevance + sufficiency  
3. **Generation**: Check 5 responses for unsupported claims

### Red Flags to Monitor:
- Chunk comprehension rate <80%
- Top-3 retrieval relevance <4/5
- Any hallucinated facts in generation

### The Bottom Line:
**"Measure what matters at each stage, not just the final output"**

---

## Q&A (10 minutes)

### Likely Questions & Prep:
- "How do you scale this evaluation process?"
- "What tools can automate these checks?"
- "How often should we run these evaluations?"
