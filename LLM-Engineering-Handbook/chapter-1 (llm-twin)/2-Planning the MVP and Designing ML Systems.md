

# Chapter 1: Part 2 – Planning the MVP and Designing ML Systems

## Learning Objectives

By the end of this section, you should understand:

* What an MVP is
* Why companies build MVPs first
* How to define an MVP for an LLM product
* Why ML systems are difficult to build
* What Feature, Training, and Inference (FTI) pipelines are
* Why FTI architecture is considered a best practice

---

# Before Writing Code...

Many beginners think building an AI application means:

```
Collect data
↓

Train model
↓

Deploy
```

Professional AI engineers know that this almost never works in production.

Before writing code, they ask:

* What problem are we solving?
* What features are absolutely necessary?
* What can wait for version 2?
* How can we validate our idea quickly?

This is where the concept of an **MVP** comes in.

---

# What is an MVP?

**MVP = Minimum Viable Product**

Let's break this down.

## Minimum

Only the essential features.

Nothing extra.

---

## Viable

The product must actually work.

Users should be able to complete an end-to-end task.

---

## Product

Something real that users can use.

Not just a prototype.

---

## Simple Definition

An MVP is:

> **The smallest working version of a product that provides value to users.**

Notice something important.

It is **NOT**

* incomplete
* buggy
* half-built

It should solve **one important problem very well**.

---

# Why Build an MVP?

Imagine you want to build the world's best AI writing assistant.

You have 100 ideas.

* Voice generation
* AI avatar
* Image generation
* Social media posting
* Email automation
* Calendar integration
* Memory
* RAG
* Fine-tuning
* Analytics

If you try to build everything...

You'll probably never launch.

Instead,

professional startups ask:

> **What's the smallest version that users would still find useful?**

That becomes Version 1.

---

# Benefits of an MVP

The book highlights several advantages.

## 1. Faster Time-to-Market

Instead of spending a year building,

launch in one or two months.

Real users start using it sooner.

---

## 2. Validate the Idea

Maybe people don't even want your product.

Better to learn this early.

---

## 3. Learn from Users

Users will tell you:

> "This feature is amazing."

or

> "I don't need this."

That feedback guides future development.

---

## 4. Reduce Risk

Building software is expensive.

An MVP minimizes wasted effort if the idea doesn't succeed.

---

# MVP of the LLM Twin

The authors intentionally keep the first version simple.

Instead of trying to clone an entire human personality,

their MVP focuses on one specific task:

**Writing personalized content.**

For example:

User enters:

```
Today's topic:
Fine-tuning Llama models
```

The Twin produces

* LinkedIn post
* Medium article
* X thread

in the user's own writing style.

Notice what they deliberately exclude:

❌ Voice cloning

❌ Video generation

❌ AI avatar

❌ Human conversations

❌ Autonomous agents

Why?

Because those features aren't necessary to validate the core idea.

---

# Important Lesson

Professional engineers don't ask:

> "What can we build?"

They ask:

> **"What is the smallest thing users will actually love?"**

This mindset saves companies millions of dollars.

---

# Building ML Systems

Now the chapter changes direction.

Instead of product design,

it starts discussing engineering.

The authors ask:

> Why are ML systems so difficult?

The answer:

Because machine learning is **not traditional software**.

---

# Traditional Software

Traditional software looks like this:

```
Input

↓

Rules

↓

Output
```

Example:

Calculator

```
2 + 5

↓

Code

↓

7
```

The programmer writes every rule.

---

# Machine Learning

Machine Learning looks different.

```
Input

↓

Model

↓

Prediction
```

The programmer doesn't write all the rules.

The model learns them from data.

---

# Why This Makes Things Hard

Suppose your model performs poorly.

What caused it?

Could be:

* bad data
* missing data
* wrong preprocessing
* wrong hyperparameters
* wrong architecture
* wrong deployment
* wrong evaluation

Finding the real problem becomes much harder.

---

# ML Systems Have Many Components

A production AI system is much more than a trained model.

It contains:

```
Data Collection

↓

Data Cleaning

↓

Feature Engineering

↓

Training

↓

Evaluation

↓

Deployment

↓

Monitoring

↓

Retraining
```

Each component can fail independently.

That's why engineering discipline matters.

---

# Problems with Older ML Architectures

The book explains that many teams used to build ML systems like this:

```
Everything

↓

One huge project

↓

One huge script

↓

One deployment
```

This created many issues.

---

## Problem 1

Everything depends on everything.

Changing one line can break the whole system.

---

## Problem 2

Code duplication.

Different teams copy the same preprocessing code.

Eventually,

different pipelines produce different results.

---

## Problem 3

Training and inference become inconsistent.

Example:

Training:

```
Lowercase text

↓

Remove punctuation

↓

Tokenize
```

Inference:

```
Tokenize only
```

The model now receives different inputs.

Performance drops.

---

## Problem 4

Very difficult to maintain.

As projects grow,

the code becomes impossible to understand.

---

# The Solution: FTI Architecture

The authors propose a cleaner design called:

# FTI

Feature

Training

Inference

These become three independent pipelines.

Think of them as three factory departments.

---

# Pipeline 1: Feature Pipeline

Purpose:

Prepare data.

Responsibilities include:

* collecting data
* cleaning data
* preprocessing
* embedding
* feature engineering
* storing processed features

It transforms raw information into useful inputs for the model.

Think of it like washing and cutting vegetables before cooking.

---

# Pipeline 2: Training Pipeline

Purpose:

Train the model.

Responsibilities:

* load processed features
* train model
* validate
* evaluate
* save trained model
* version model

It does **not** collect raw data.

It assumes the Feature Pipeline has already prepared everything.

---

# Pipeline 3: Inference Pipeline

Purpose:

Serve predictions to users.

Responsibilities:

* receive request
* preprocess input
* retrieve features if needed
* run inference
* return response

This is the pipeline users interact with.

---

# Why Separate Them?

Imagine your Feature Pipeline changes.

Maybe you discover a better tokenizer.

Good architecture allows you to update that pipeline without rewriting your deployment logic.

Similarly,

you might train ten different models using the same processed features.

Or deploy the same model in multiple applications.

Each pipeline becomes reusable.

---

# Visualizing FTI

```
Raw Data
      │
      ▼
Feature Pipeline
      │
      ▼
Processed Features
      │
      ├────────► Training Pipeline
      │                 │
      │                 ▼
      │          Trained Model
      │                 │
      └────────────► Inference Pipeline
                            │
                            ▼
                      User Response
```

---

# Benefits of FTI

The book highlights several important benefits.

## 1. Separation of Responsibilities

Each pipeline has one clear job.

This makes systems easier to understand.

---

## 2. Reusability

One Feature Pipeline can support many different models.

---

## 3. Easier Maintenance

Need to improve preprocessing?

Update only the Feature Pipeline.

Need a better model?

Update only the Training Pipeline.

---

## 4. Better Collaboration

Different teams can work independently.

Data engineers improve features.

ML engineers improve training.

Backend engineers improve inference.

Everyone doesn't touch the same codebase.

---

## 5. Better Reproducibility

Because pipelines are separated,

experiments become easier to reproduce.

This is essential for MLOps.

---

# Real Example

Suppose you're building your own AI Customer Support chatbot (similar to your fine-tuned DistilBERT + Qwen project).

Your FTI architecture could look like this:

### Feature Pipeline

* Collect support tickets
* Clean text
* Remove duplicates
* Generate embeddings
* Store in a vector database

---

### Training Pipeline

* Fine-tune DistilBERT for intent classification
* Evaluate accuracy
* Save the best checkpoint
* Register the model

---

### Inference Pipeline

* User sends a question
* Predict intent
* Retrieve relevant documents
* Send context to Qwen
* Generate the final response

Notice how each stage has a clear responsibility and can evolve independently.

---

# Key Takeaways (Revision)

* An **MVP** is the smallest complete product that delivers value to users.
* The LLM Twin MVP focuses on personalized writing assistance, not replicating every aspect of a person.
* Production ML systems are complex because they involve data, models, deployment, and monitoring—not just training.
* Monolithic ML architectures are hard to maintain and often lead to inconsistent preprocessing.
* The **FTI architecture** separates the system into three pipelines:

  * **Feature Pipeline:** Collects and prepares data.
  * **Training Pipeline:** Trains and evaluates the model.
  * **Inference Pipeline:** Serves predictions to users.
* Separating pipelines improves maintainability, reusability, collaboration, and reproducibility.

---

## Interview Questions

1. What is an MVP, and why is it important for AI product development?
2. Why should an AI startup build an MVP before implementing advanced features?
3. Why are production ML systems more complex than traditional software systems?
4. What problems arise from monolithic ML architectures?
5. Explain the Feature, Training, and Inference (FTI) architecture.
6. Why should preprocessing logic be shared between training and inference?
7. How does FTI architecture support MLOps best practices?

In **Part 3 (the final part of Chapter 1)**, we'll cover the complete **LLM Twin system architecture**, including the **data collection pipeline**, **feature pipeline**, **training pipeline**, **inference pipeline**, how they connect together, and finish with a full **Chapter 1 revision sheet** that you can use before interviews or exams.
