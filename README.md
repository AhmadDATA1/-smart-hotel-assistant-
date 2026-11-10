# Smart Hotel Assistant

A generative AI assistant for a hotel, built as the final project of the **Developing Generative AI Solutions** program (SDAIA Academy).

The assistant answers guest questions from the hotel manual using RAG, handles bookings through tools, remembers the conversation, and protects guest data. A manager can ask for hotel-wide statistics computed from **119,390 real hotel bookings**, and a dashboard shows hotel and assistant KPIs.

## Notebooks

| File | Description |
|---|---|
| `Smart_Hotel_Assistant_Short.ipynb` | Compact version of the project (19 cells) |
| `Smart_Hotel_Assistant_Final_Project.ipynb` | Full version with extra demos, tests and analytics (31 cells) |

Open either notebook in Google Colab and choose **Runtime > Run all**. A T4 GPU runtime is recommended, but the project also runs on CPU.

## How a request flows

```
Guest or manager question
  -> Guardrail (blocks prompt injection attempts)
  -> Planner (booking tool, knowledge search, manager insights, or a mix)
  -> LoopDetector + max_steps (stops repeated or endless tool calls)
  -> Tools: check_availability, check_booking, book_room, cancel_booking, get_hotel_insights
  -> RAG: ChromaDB search -> LLM answer from retrieved sources only
  -> Cache, retry with backoff, safe fallback
  -> SQLite memory (summary + last messages) and request logging
  -> Tracing, evaluation and dashboard
```

## Features

- **RAG over the hotel manual**: cleaning, recursive chunking with overlap, multilingual embeddings (Arabic and English), ChromaDB with metadata filters.
- **Booking tools**: availability, booking status, booking and cancellation, validated with Pydantic and exposed through a ToolRegistry with JSON tool schemas.
- **Human-in-the-loop**: bookings and cancellations show the price or fee first and only run after the guest confirms.
- **Privacy and security**: guests only see their own bookings, hotel statistics are limited to managers, prompt injection is blocked in both questions and retrieved documents, and SQL uses parameterized queries.
- **Memory**: SQLite sessions, sliding window, recursive summary, fidelity gate and hybrid compaction.
- **Reliability**: cache, retry with backoff, timeout and a safe fallback when the model is unavailable.
- **Observability**: `@observe` trace tree with timings for every step.
- **Evaluation**: integration tests for tools, retrieval Hit Rate@3 and MRR@3, and answer evaluation by keywords and semantic similarity.
- **Real data analytics**: cancellation rate by month, top guest countries, cancellations by booking lead time, and resort vs city hotel comparison.
- **Dashboard**: bookings and revenue by room type, occupancy forecast, ADR, and assistant request types.

## What was used from each lab

| Lab | Used in the project |
|---|---|
| Lab 1: Model | Arabic vs English tokenization, token cost analysis (full manual vs RAG) |
| Lab 2: API | Local LLM, system prompt with chat template, retry and timeout, cache, safe fallback |
| Lab 3: Tools | Pydantic schema, tool schema, two-call pattern, ToolRegistry, integration testing |
| Lab 4: Documents | Document cleaning and model, recursive chunking, embeddings |
| Lab 5: RAG | Vector storage, RAG service, retrieval evaluation, metadata filters |
| Lab 6: Memory | SQLite session, sliding window, recursive summarisation, fidelity gate and hybrid compactor |
| Lab 7: Agents | max_steps and LoopDetector, `@observe` tracing, evaluation |

## Tech stack

Python, Hugging Face Transformers (`LiquidAI/LFM2.5-350M`), Sentence Transformers (`paraphrase-multilingual-MiniLM-L12-v2`), ChromaDB, LangChain Text Splitters, Pydantic, tiktoken, SQLite, pandas, Matplotlib.

## Data

- **Hotel demo data**: rooms, guests, bookings and the hotel manual are sample data created for this project.
- **Real booking data**: *Hotel Booking Demand* dataset by Antonio, Almeida and Nunes (Data in Brief, 2019), with bookings from two hotels in Portugal between 2015 and 2017. The notebooks load it from the [TidyTuesday repository](https://github.com/rfordatascience/tidytuesday/tree/master/data/2020/2020-02-11).
