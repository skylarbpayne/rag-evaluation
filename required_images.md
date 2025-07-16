# Required Images for RAG Evaluation Presentation

## Slide: "The Core Concept" (RAG Failure Funnel)
**Image Description**: A visual flowchart/pipeline diagram showing the RAG system stages as a funnel or pipeline with potential failure points. Should show:
- Input query at the top
- Sequential boxes for: Chunking → Indexing → Retrieval → Generation → Output
- Red "X" or warning icons at each stage to indicate potential failure points
- Each stage should be a distinct color with clear arrows between them
- Include small icons for each stage (document chunks, database, search, brain/AI, output text)
- Style: Clean, professional diagram with clear visual hierarchy

## Slide: "Real Example: Wrong Answer Diagnosis"
**Image Description**: A step-by-step diagnostic flowchart showing the investigation process for the "company founded in 2020 vs 2019" example. Should show:
- A decision tree or flowchart format
- Four main branches corresponding to the diagnostic questions
- Each branch should have a question bubble and a yes/no path
- Visual indicators showing where the actual problem was found
- Use green checkmarks for passed stages and red X for the failure point
- Include small document/text snippets showing the problematic chunk
- Style: Investigative/detective theme with magnifying glass icons

## Slide: "Check 1: Information Completeness" (Chunking)
**Image Description**: A before/after comparison showing bad vs good chunking. Should display:
- Side-by-side comparison layout
- Left side: "Bad Chunking" with the Apple Inc. example split across two incomplete chunks
- Right side: "Good Chunking" with a complete, coherent chunk containing all founding information
- Visual chunk boundaries (boxes or containers) around text
- Red warning indicators on bad chunks, green success indicators on good chunks
- Text should be clearly readable and properly highlighted
- Style: Clean comparison layout with clear visual differentiation

## Slide: "Check 2: Semantic Coherence" (Chunking)
**Image Description**: A visual representation showing incoherent vs coherent chunks. Should show:
- Example of the "quarterly results + coffee machine" bad chunk in a fragmented/broken visual style
- Contrast with a well-formed, coherent chunk shown as a solid, unified block
- Use visual metaphors like puzzle pieces that don't fit vs pieces that align perfectly
- Include coherence quality indicators (possibly a coherence meter/gauge)
- Highlight the jarring topic transition with visual breaks or color changes
- Style: Abstract/conceptual with clear good vs bad visual language

## Slide: "Check 4: Knowledge Coverage" (Chunking)
**Image Description**: A knowledge gap visualization showing the enterprise refund policy example. Should depict:
- A large document repository/database icon
- A search query bubble ("enterprise refund policy")
- Empty search results or "0 results found" display
- A highlighting or callout showing where the policy actually exists in the documents
- Visual representation of the gap between available information and findable information
- Include icons for different document types (legal docs, user guides, etc.)
- Style: Information architecture diagram with clear gap visualization

## Slide: "Check 1: Context Relevance" (Retrieval)
**Image Description**: A relevance scoring visualization for the password reset mobile query example. Should show:
- The user query at the top ("How do I reset my password on mobile?")
- Three retrieved chunks displayed as cards or containers
- Each chunk should have a relevance score or rating (low/irrelevant)
- Visual indicators showing semantic similarity but practical irrelevance
- Use color coding: red for irrelevant, yellow for partially relevant, green for highly relevant
- Include a "relevance meter" or scoring system
- Style: Card-based layout with clear scoring indicators

## Slide: "Check 3: Retrieval Precision" (Retrieval)
**Image Description**: A search results ranking visualization showing the API rate limits example. Should display:
- Search results list format (like Google search results)
- Three results with ranking numbers (1, 2, 3)
- Each result should show title and brief content
- Visual indicators showing which results are useful vs not useful
- The good result (#3) should be highlighted as being buried
- Include relevance scores or usefulness ratings for each result
- Style: Familiar search engine results interface with quality indicators

## Slide: "Check 4: Semantic Accuracy" (Retrieval)
**Image Description**: A temporal mismatch visualization for the 2023 vs 2022 revenue example. Should show:
- Timeline or calendar visual showing 2022 vs 2023
- Query bubble asking for "2023 revenue figures"
- Retrieved result clearly labeled as "2022 revenue data"
- Visual mismatch indicators (wrong year highlighted in red)
- Semantic similarity meter showing high similarity despite temporal incorrectness
- Include calendar icons or timeline elements to emphasize the date constraint
- Style: Timeline-based layout with clear temporal mismatch indicators

## Slide: "Check 1: Faithfulness to Context" (Generation)
**Image Description**: A fact-checking visualization showing context vs generated claims. Should display:
- The provided context in one container/box
- The generated answer in another container
- Lines or connectors linking supported claims to their sources in context
- Unsupported claims (uptime, security) highlighted in red with no connections
- Supported claims (1,000 requests/minute) connected with green lines
- Include fact-check icons (checkmarks and X marks)
- Style: Evidence-linking diagram with clear supported vs unsupported distinctions

## Slide: "Check 3: Confident Hallucination" (Generation)
**Image Description**: A confidence vs accuracy visualization for the webhook rate limit example. Should show:
- A confidence meter or gauge showing high confidence
- The generated answer with authoritative language highlighted
- The actual context showing insufficient information
- A "hallucination detection" alert or warning
- Visual contrast between confident tone and lack of supporting evidence
- Include warning icons or alert symbols
- Style: Dashboard-style with confidence meters and alert indicators

## Slide: "Weekly Evaluation Routine" (Checklist)
**Image Description**: A comprehensive dashboard showing all evaluation metrics and checkboxes. Should display:
- Three main sections: Chunking, Retrieval, Generation
- Each section with its key metrics and target percentages
- Progress bars or gauges showing current vs target performance
- Checklist items with checkboxes (some filled, some empty)
- Color-coded status indicators (green for good, yellow for needs attention, red for failing)
- Include a weekly calendar or schedule indicator
- Style: Professional dashboard/KPI display with clear metric tracking

## Slide: "Your Homework" (Conclusion)
**Image Description**: A step-by-step action plan visualization showing the 5 homework steps. Should show:
- Five numbered steps in a linear progression or circular improvement cycle
- Each step represented by an icon (target for "pick stage", magnifying glass for "run test", wrench for "fix", chart for "measure", arrow for "move to next")
- A feedback loop showing continuous improvement
- Visual emphasis on "one variable at a time" principle
- Include progress indicators or workflow arrows
- Style: Process flow diagram with clear action-oriented icons and progression indicators
