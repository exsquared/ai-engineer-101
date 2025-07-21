# 🧪 Checkpoint Project #3: LLM-Powered FAQ Bot

This is your first real-world mini-project using an LLM.  
You’ll apply everything you learned in Chapters 8a–8c:

✅ Instructional prompting  
✅ Context injection  
✅ Fallback handling  
✅ Confidence-aware prompting

---

## 📍 Where You Are

You’ve just finished:

- 8a: How LLMs work (tokens, hallucination, context windows)
- 8b: Prompting styles (zero-shot, few-shot, train-of-thought)
- 8c: Structured prompting + graceful fallbacks

Before we jump into scaling, APIs, and agents — let’s make something real.

---

## 🚀 Your Mission

Build a simple LLM assistant that answers questions using only a company FAQ file.

It should:

- Inject relevant answers into the prompt
- Tell the user if it’s not sure
- Be callable via CLI or Streamlit

---

📄 **Project Link**: [project_3_faq_bot.md](project_3_faq_bot.md)

This project does *not* require vector search or LangChain.  
You’ll simulate retrieval with basic keyword matching.

---

## 🔜 After This

Once you complete this checkpoint, you’ll be ready to:

- Turn your assistant into an API (Chapter 9)
- Add retry, logging, and caching (Chapter 10)
- Plug it into tools and memory systems (Chapters 11+)