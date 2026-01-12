# GitHub Developer RAG Analyzer

A **document-driven Retrieval-Augmented Generation (RAG)** system that analyzes a GitHub developer’s profile, repositories, and activity to generate **high-level summaries** and answer structured questions using LLMs.

This project focuses on **correct data modeling and intent-aware retrieval**, rather than letting an LLM guess where information lives.

---

## What this project does

Given a GitHub username, the system:

- Collects profile, repository, README, and commit data
- Converts that data into **explicit document types**
- Routes user questions to the **right documents**
- Uses an LLM to **generate high-level summaries and grounded answers** from retrieved context

The system provides both:
- **high-level summaries** of a developer’s work and focus areas, and  
- **targeted answers** to specific profile, repository, and activity-related questions

All outputs are strictly derived from the documents created during ingestion.

---

## Core idea: documents, not guessing

The system works entirely on **documents we explicitly create**.

The LLM:
- does **not** search GitHub
- does **not** decide what data to use
- only reads the documents retrieved for a question

If the required information does not exist as a document, the system does not fabricate an answer.

---

## Document types

The system creates the following documents:

- **User Profile**  
  Global GitHub facts such as repository count, bio, and metadata.

- **Repository Summary (one per repository)**  
  A short **narrative description** of each repository, derived from:
  - repository description
  - primary language
  - commit activity heuristics
  - inferred technical domain

- **Repository Documentation (optional)**  
  README content stored as supporting evidence.

- **Commit Behavior (one per repository)**  
  Contribution patterns based on commit messages, additions, deletions, and activity level.

- **User Summary (derived document)**  
  A high-level summary of the developer, created by aggregating information across repositories and domains.

---

## What the summary document contains

The **user summary** is a first-class document in the system.

It captures:
- dominant technical domains
- types of projects the developer works on
- overall activity and contribution style
- high-level patterns across repositories

This document enables questions such as:
- “Tell me something about this developer”
- “What kind of work do they focus on?”
- “What stands out about their GitHub profile?”

---

## Intent routing with Valhalla

The system does **not** rely on the main LLM to decide where to look.

Instead, it uses a **lightweight Hugging Face zero-shot classifier**
(`valhalla/distilbart-mnli-12-1`) to map questions to relevant document types.

Why Valhalla:
- fast and inexpensive (CPU-friendly)
- deterministic and debuggable
- prevents scope confusion (profile vs repos vs commits)
- keeps the LLM focused only on reasoning and language generation

This separation avoids many common RAG failure modes.

---

## What users can get from this project

Using this system, users can ask:

- **High-level questions**  
  “Tell me about this developer”  
  “What domains do they work in?”

- **Repository-focused questions**  
  “Tell me something about the user’s repositories”  
  “Which repository stands out?”

- **Activity-focused questions**  
  “How active is this developer?”  
  “What does their contribution pattern look like?”

All answers are grounded in explicitly created documents.

---

## Tech stack

- Python
- GitHub API
- LlamaIndex
- Hugging Face (Valhalla intent router)
- Gemini (LLM for answer synthesis)

---

## Status & scope

This project focuses on:
- correct document design
- intent-aware retrieval
- explainable RAG behavior

It intentionally does **not** perform deep source-code analysis or validate README claims against code (yet).

---

> **One-line takeaway:**  
> This project demonstrates how to build a **correct, explainable GitHub analysis system** by separating intent detection, retrieval, and reasoning — instead of letting an LLM guess.
