# ZenML Production Stack: How Real ML Pipelines Run in Production

> **This is probably the most important ZenML section for AI/ML Engineer interviews.**
>
> In Part 4, we learned:
>
> * Pipeline
> * Step
> * Artifact
> * Metadata
> * Caching
> * DAG
>
> Those concepts explain **what** a pipeline is.
>
> In this part, we'll learn **how ZenML actually executes pipelines in production**.
>
> This is where ZenML transforms from "a Python library" into an **MLOps platform**.

---

# Section 1 — The Big Picture

Let's begin with one question.

When you execute

```
Run Pipeline
```

Who actually does the work?

Does ZenML train the model?

No.

Does ZenML run Docker?

No.

Does ZenML store models?

No.

Does ZenML deploy models?

No.

So...

**What exactly does ZenML do?**

The answer is:

> **ZenML coordinates specialized tools instead of replacing them.**

This is a fundamental MLOps concept.

---

# The Orchestra Analogy

Imagine an orchestra.

```
            Orchestra

Violin

Drums

Piano

Flute

Trumpet
```

Who makes music?

Not the conductor.

The musicians do.

The conductor coordinates them.

ZenML is exactly like the conductor.

---

Instead of musicians

we have

```
Docker

AWS

Comet ML

Kubeflow

S3

MLflow

TensorFlow

PyTorch
```

ZenML coordinates all of them.

---

# Section 2 — What is a ZenML Stack?

This is one of the most important interview questions.

## Definition

A **Stack** is a collection of infrastructure components that work together to execute ML pipelines.

Think of it as

> **The runtime environment of your ML pipeline.**

---

## Analogy

Imagine building a restaurant.

You need

```
Kitchen

Chef

Storage

Cash Register

Delivery Service
```

Together

they form

the restaurant.

Similarly,

ZenML needs multiple components.

---

# ZenML Stack Architecture

```
                  ZenML Stack

                        │

        ┌───────────────┼───────────────┐

        │               │               │

  Orchestrator     Artifact Store   Experiment Tracker

        │               │               │

        ├───────────────┼───────────────┤

                        │

                 Model Deployer

                        │

                  Secret Manager
```

Notice

ZenML itself is not replacing these tools.

It simply connects them.

---

# Why Not One Giant Tool?

Many beginners ask:

> "Why doesn't ZenML include everything?"

Because each problem is difficult.

Companies specialize.

For example

AWS specializes in cloud.

Docker specializes in containers.

Comet specializes in experiment tracking.

Qdrant specializes in vector search.

ZenML connects them together.

This follows another software engineering principle:

> **Do one thing well.**

---

# Section 3 — Orchestrator

One of the most important components.

---

## What is an Orchestrator?

Definition

An orchestrator decides

* when steps run
* where they run
* in what order
* what depends on what

Think of it as

the project manager.

---

# Example

Imagine

```
Load Data

↓

Train

↓

Evaluate

↓

Deploy
```

The orchestrator knows

```
Training cannot begin

until

Load Data

finishes.
```

---

# Real Production Example

Suppose

Training

takes

12 hours.

Evaluation

takes

15 minutes.

Deployment

takes

3 minutes.

The orchestrator schedules everything automatically.

---

Without orchestration

you manually execute

```
python train.py

↓

python evaluate.py

↓

python deploy.py
```

Every time.

---

With orchestration

```
Run Pipeline

↓

Everything happens automatically
```

---

# Common Orchestrators

ZenML supports many.

Examples

```
Local

Airflow

Kubeflow

Vertex AI

AWS

Azure

```

Why?

Because every company has different infrastructure.

---

# Airflow

Very popular.

Designed originally

for data engineering.

Example

```
Extract

↓

Transform

↓

Load
```

(ETL)

Later

companies started using it for ML.

---

Advantages

* Mature
* Reliable
* Huge community

Limitations

* Originally not built specifically for ML.

---

# Kubeflow

Built specifically for Kubernetes.

Designed for

large-scale

cloud-native ML.

Example

Google

Uber

large enterprises.

---

Advantages

* Massive scalability
* Kubernetes integration

Disadvantages

* Steep learning curve.

---

# Local Orchestrator

During development

everything runs

on your laptop.

Example

```
Python

↓

Local CPU

↓

Local GPU
```

Later

you switch

to cloud

without rewriting pipelines.

This portability is one of ZenML's strengths.

---

# Section 4 — Artifact Store

Remember artifacts?

```
Dataset

Model

Metrics

Embeddings
```

Where are they stored?

Inside

Artifact Store.

---

## Definition

An artifact store is persistent storage for pipeline outputs.

---

Example

```
Pipeline

↓

Train

↓

Model

↓

Artifact Store
```

---

Common Storage

```
Local Disk

AWS S3

Google Cloud Storage

Azure Blob

MinIO
```

---

Why?

Suppose

Model

=

8 GB

You don't want

every step

retraining

every execution.

---

Instead

```
Store Once

↓

Reuse Forever
```

---

# Production Example

Your RAG project

```
Chunk Documents

↓

Generate Embeddings

↓

Artifact Store
```

Tomorrow

reuse embeddings.

No recomputation.

---

# Section 5 — Experiment Tracker

This is another huge concept.

---

Imagine

you train

100 models.

Questions

```
Which one was best?

Which learning rate?

Which GPU?

Which dataset?

Which prompt?

Which seed?
```

Memory isn't enough.

---

Experiment tracking exists

to answer these questions.

---

## What is an Experiment Tracker?

It records

everything

about

training.

---

Typical information

```
Parameters

Metrics

Accuracy

Loss

Artifacts

GPU

Time

Model

Dataset Version
```

---

Popular Experiment Trackers

```
Comet ML

MLflow

Weights & Biases

Neptune
```

The book focuses on **Comet ML**, which we'll cover in depth in the next part.

---

# Why This Matters

Imagine

Fine-tuning

Qwen

five times.

Results

```
Run 1

95%

Run 2

97%

Run 3

89%

Run 4

98%

Run 5

96%
```

Which settings produced

98%?

Experiment tracking remembers.

---

# Section 6 — Model Deployer

Training

is only half the job.

Eventually

users need predictions.

---

Pipeline

```
Train

↓

Evaluate

↓

Deploy

↓

Users
```

The deployment component

handles

moving models

into production.

---

Possible destinations

```
AWS SageMaker

Docker

Kubernetes

Vertex AI

Azure ML
```

---

# Section 7 — Secret Manager

Production systems

need

API keys.

Examples

```
OpenAI Key

AWS Key

MongoDB Password

Postgres Password

Qdrant API Key
```

Never write

```
password="12345"
```

inside code.

---

Instead

Secret Managers

store credentials securely.

Examples

```
AWS Secrets Manager

Hashicorp Vault

Azure Key Vault
```

ZenML can integrate with these services so pipelines access secrets securely rather than embedding them in source code.

---

# Why Secrets Matter

Imagine

your GitHub repository becomes public.

If

AWS keys

are inside

the code

attackers can

access your infrastructure.

Real companies have suffered costly security incidents because of exposed credentials.

---

# Section 8 — Putting Everything Together

Now let's understand

what actually happens.

```
                User

                  │

           Run Pipeline

                  │

                  ▼

              ZenML

                  │

     ┌────────────┼─────────────┐

     │            │             │

Orchestrator   Experiment    Artifact

                Tracker        Store

     │            │             │

     └────────────┼─────────────┘

                  │

          Execute Pipeline

                  │

       PyTorch / TensorFlow

                  │

          Train Model

                  │

       Store Artifacts

                  │

       Track Metrics

                  │

      Deploy to Cloud
```

Notice

ZenML itself

never trains models.

PyTorch does.

ZenML coordinates.

---

# Section 9 — Complete Production Example

Let's build

an end-to-end

LLM pipeline.

Imagine your

Customer Support AI.

```
Raw Support Tickets

        │

        ▼

Data Cleaning

        │

        ▼

Generate Embeddings

        │

        ▼

Store in Qdrant

        │

        ▼

Fine-tune DistilBERT

        │

        ▼

Evaluate

        │

        ▼

Push Model

        │

        ▼

Deploy FastAPI

        │

        ▼

Users Chat

        │

        ▼

Collect Feedback

        │

        ▼

Retraining Pipeline
```

Now map it

to ZenML.

```
Pipeline

│

├── Data Step

├── Cleaning Step

├── Embedding Step

├── Training Step

├── Evaluation Step

├── Deployment Step

└── Monitoring Step
```

Each box

becomes

an independent,

reusable,

testable step.

---

# Section 10 — ZenML vs Airflow vs Kubeflow

One interview question you might face is:

> **"Why choose ZenML instead of Airflow or Kubeflow?"**

Here's a practical comparison:

| Feature                         | ZenML                        | Airflow                        | Kubeflow                        |
| ------------------------------- | ---------------------------- | ------------------------------ | ------------------------------- |
| Primary Focus                   | ML pipelines                 | General workflow orchestration | Kubernetes-native ML            |
| Beginner Friendly               | ✅ Yes                       | Moderate                       | Difficult                       |
| Local Development               | Excellent                    | Good                           | Limited                         |
| Experiment Tracking Integration | Built-in ecosystem           | External                       | External                        |
| Cloud Integration               | Excellent                    | Excellent                      | Excellent                       |
| Kubernetes Required             | ❌ No                        | ❌ No                          | ✅ Yes                          |
| Best For                        | Small to enterprise ML teams | Data engineering + workflows   | Large cloud-native ML platforms |

**Rule of thumb:**

* **ZenML** → Excellent for ML-first teams.
* **Airflow** → Excellent when your organization already uses it for ETL.
* **Kubeflow** → Best for organizations deeply invested in Kubernetes.

---

# Production AI Engineer Insight

When you're interviewing for an AI/ML Engineer role, don't describe ZenML as:

> "A pipeline framework."

Instead, say something like:

> "ZenML is an MLOps orchestration framework that enables reproducible ML pipelines by coordinating components such as orchestrators, artifact stores, experiment trackers, and deployment targets while keeping the pipeline definition infrastructure-agnostic."

That answer demonstrates you understand **why** the framework exists, not just **what** it does.

---

# Key Takeaways

* A **ZenML Stack** is a collection of infrastructure components used to execute ML pipelines.
* ZenML coordinates specialized tools rather than replacing them.
* **Orchestrators** control execution order and dependencies.
* **Artifact Stores** persist outputs like models, datasets, and embeddings.
* **Experiment Trackers** record metrics, parameters, and metadata for every run.
* **Model Deployers** move validated models into production.
* **Secret Managers** securely provide credentials without exposing them in code.
* ZenML's modular architecture makes it adaptable to local development and cloud deployments.

---

# Interview Questions

1. What is a ZenML Stack?
2. Why doesn't ZenML implement its own experiment tracker?
3. What is the role of an orchestrator?
4. What is the purpose of an artifact store?
5. Why are experiment trackers essential for ML?
6. Why should secrets never be stored directly in source code?
7. How does ZenML integrate with cloud infrastructure?
8. Compare ZenML with Airflow and Kubeflow.

---

# Common Beginner Mistakes

❌ Thinking ZenML replaces frameworks like PyTorch.

❌ Assuming the orchestrator performs model training.

❌ Treating artifact storage as simple file storage instead of versioned pipeline outputs.

❌ Hardcoding API keys into pipeline code instead of using a secret manager.

❌ Choosing orchestration tools without considering team size, infrastructure, and deployment requirements.

---

## End of Part 5

The next section of Chapter 2 moves to **Comet ML**, one of the most valuable tools for **experiment tracking** in modern AI engineering.

We'll go much deeper than the book by covering:

* Why experiment tracking is indispensable.
* Every component of a Comet ML experiment.
* Hyperparameter comparison.
* Metrics visualization.
* Artifact management.
* Model Registry.
* Team collaboration.
* Production workflows.
* Comparison with **Weights & Biases (W&B)** and **MLflow**.
* How Comet ML integrates with ZenML to build a complete MLOps pipeline.
