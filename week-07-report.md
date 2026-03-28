# Week 7: RAG Security Knowledge Assistant — Evaluation Report

## 1. Setup Summary
- **LLM:** llama-3.3-70b-versatile via Groq
- **Embeddings:** sentence-transformers/distilbert-base via HuggingFace Inference API
- **Vector Store:** In-Memory Vector Store
- **Chat Link:**
- https://cloud.flowiseai.com/chatbot/b6a05771-d7ce-41b6-acb3-341df48933e3
- **Documents loaded:**
  - mitre-exfiltration.txt (4 techniques)
  - mitre-resource-development.txt (3 techniques)
  - mitre-reconnaissance.txt (4 techniques)

## 2. Test Results
| # | Question | Used Documents? | Quality | Notes |
|---|----------|----------------|---------|-------|
| 1 | What is automated exfiltration and which threat groups have used it? | Yes | Partial | Found T1020 description but missed specific threat groups like APT28 and Gamaredon |
| 2 | How do adversaries use data transfer size limits to avoid detection during exfiltration? | Yes | Good | Correctly explained chunking technique from the exfiltration document |
| 3 | What is scheduled transfer and how do malware like Cobalt Strike use it? | Yes | Partial | Explained concept correctly but could not find Cobalt Strike specifics despite being in the document |
| 4 | How do adversaries use search engines and CDNs during reconnaissance? | Yes | Partial | Retrieved reconnaissance docs but gave vague answer without citing specific techniques |
| 5 | What are initial access brokers and how do adversaries acquire access through them? | No | Wrong | Responded with "Hmm, I'm not sure" despite IAB content being in the resource development document |

## 3. Edge Case Observations
- **Unrelated question:** Asked "What color is the sky?" and "How are you feeling today?" the chatbot responded with "Hmm, I'm not sure" both times and did not hallucinate. It correctly refused to answer outside its knowledge base.
- **Topic not in documents:** Asked "What are the latest CVEs from 2026?" the chatbot admitted it didn't know rather than making up an answer. This is good RAG behavior it constrains the LLM to only use uploaded documents.

## 4. Settings Experiments (if completed)
- **Temperature change:** Not tested. Temperature was kept at 0.3 throughout, which produced factual and concise answers.
- **Chunk size change:** Not tested. Used default of 1000 characters. Smaller chunks might improve retrieval precision for specific threat group lookups.
- **Top K change:** Not tested. Used default of 4. Increasing Top K might help retrieve more relevant chunks and improve answer completeness.

## 5. Reflection
- What surprised you about how RAG works? It was surprising that the chatbot could retrieve correct source document chunks even when it failed to generate a complete answer. The retrieval and generation steps are clearly separate — the system showed correct MITRE ATT&CK source tags even while saying "I'm not sure," which means retrieval was working but the LLM generation was the bottleneck.
- How could you improve this chatbot for real-world use? Use a persistent vector store like Pinecone or Chroma so documents don't need reprocessing on every load. Add more detailed documents per technique. Improve the system prompt to encourage the LLM to use retrieved chunks more aggressively instead of defaulting to "I'm not sure." Along with allowing the model to come up with follow up questions
- How might you use RAG in your capstone project? RAG could power a threat intelligence assistant that answers questions about attack techniques, CVEs, or threat actor TTPs based on uploaded security reports. It could also support an incident response assistant that references internal runbooks to guide analysts through response procedures. Since I am interested in bug bounty and security research, I could use RAG to build a knowledge assistant that ingests public vulnerability disclosure reports. This would allow users to query real-world findings, learn common attack patterns, and discover techniques used by other researchers making it a practical learning and reconnaissance tool for security professionals
