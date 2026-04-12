# Project Overview

## Hierarchical VLA Architecture for Long-Horizon Robotic Manipulation

---

## Background

The rise of Vision-Language-Action (VLA) models has opened new possibilities for robots that can interpret natural language instructions, perceive their environment visually, and execute physical actions in open-ended settings. However, deploying these models on real robotic arms for **long-horizon tasks** — tasks involving multiple dependent steps — remains a significant challenge.

Current end-to-end VLA approaches suffer from several critical limitations:

- **Error accumulation** across long action sequences leads to task failure in later steps
- **Tight coupling** between high-level reasoning and low-level control makes it difficult to optimize both simultaneously
- **Cloud inference latency** from large language models creates delays that are unacceptable for real-time robotic control
- **Lack of safety supervision** puts humans at risk in collaborative environments where robotic arms operate near people

This project addresses these limitations by proposing a **hierarchical, modular VLA framework** that decouples task planning, action execution, and safety monitoring into three specialized components.

---

## Architecture

The system is structured around a three-tier decision-making architecture inspired by the functional division of the human nervous system:

```
┌─────────────────────────────────────────────────┐
│                    BRAIN                        │
│       (High-Level Planning & Reasoning)         │
│         Qwen LLM + LoRA Fine-tuning             │
└──────────────────────┬──────────────────────────┘
                       │ Sub-task sequence
┌──────────────────────▼──────────────────────────┐
│                  CEREBELLUM                     │
│         (Low-Level Action Execution)            │
│           ACT / SmolVLA / Pi0                   │
└──────────────────────┬──────────────────────────┘
                       │ Action commands
┌──────────────────────▼──────────────────────────┐
│                  SUPERVISOR                     │
│       (Real-Time Safety Monitoring)             │
│              YOLO-based detection               │
└─────────────────────────────────────────────────┘
```

Each module operates independently and communicates through well-defined interfaces, allowing components to be upgraded or swapped without affecting the rest of the system.

---

## Modules

### 🧠 Brain — Task Planning Module

The Brain module serves as the high-level coordinator of the system. It is responsible for:

- Receiving natural language task instructions from the user
- Interpreting real-time visual feedback from the robot's camera
- Decomposing long-horizon tasks into ordered sub-task sequences
- Dynamically replanning when environmental conditions change

**Implementation:** The Brain is built on the **Qwen large language model**, fine-tuned using **LoRA (Low-Rank Adaptation)** on robot-specific task data. This approach leverages Qwen's pre-trained language reasoning and multimodal understanding capabilities while adapting it for robotic scene comprehension and instruction decomposition.

---

### 🦾 Cerebellum — Action Execution Module

The Cerebellum module handles the translation of high-level sub-tasks into precise physical actions. It is responsible for:

- Receiving sub-task commands from the Brain module
- Generating smooth, accurate robotic arm motion sequences
- Executing primitive actions: grasping, moving, placing, resetting
- Ensuring execution reliability and motion precision

**Implementation:** The Cerebellum has been prototyped on the **SO100 robotic arm** platform with three candidate models evaluated:

| Model | Description |
|-------|-------------|
| **ACT** | Action Chunking with Transformers |
| **SmolVLA** | Lightweight Vision-Language-Action model |
| **Pi0** | Generalist robot policy model |

All three models have been fine-tuned and tested. A working demo system is already operational. Further evaluation will compare task success rate, motion accuracy, and real-time performance to determine the best fit for long-horizon tasks.

---

### 👁️ Supervisor — Safety Monitoring Module

The Supervisor module ensures safe operation in open, human-populated environments. Unlike traditional emergency-stop systems, it implements a **recoverable supervision loop**:

```
Detect risk → Pause execution → Monitor environment
     → Confirm safe → Resume task
```

It is responsible for:

- Continuously monitoring the camera feed for hazards (e.g., human hands entering the workspace)
- Immediately pausing the robotic arm upon detecting a potential risk
- Waiting and re-evaluating the scene until the hazard is resolved
- Resuming the interrupted task seamlessly once safety is confirmed

**Implementation:** The Supervisor is built on **YOLO object detection** for real-time identification of hands and other risk targets. This design prioritizes task continuity alongside safety, making it well-suited for human-robot collaboration scenarios such as home assistance and collaborative manufacturing.

---

## Research Directions

Beyond the core architecture, the project investigates several supporting research problems:

### Long-Horizon Task Decomposition
Designing structured representations for multi-step tasks and robust interfaces between the Brain and Cerebellum, including mechanisms for local replanning when sub-tasks fail.

### Data Collection & Model Training
Exploring diverse teleoperation methods to build richer training datasets:
- **Leader-follower arm teleoperation** (current baseline)
- **VR-based teleoperation** for greater operator freedom and task diversity
- **UMI (Universal Manipulation Interface)** for scalable imitation learning data collection

### Local Inference Deployment
Moving LLM and VLA inference from cloud to local hardware to eliminate network latency. Research focuses on model compression, quantization, and optimized inference pipelines to achieve real-time closed-loop operation on edge devices.

---

## Current Status

| Component | Status |
|-----------|--------|
| SO100 arm platform setup | ✅ Complete |
| ACT / SmolVLA / Pi0 fine-tuning | ✅ Complete |
| Demo system (Cerebellum only) | ✅ Operational |
| Brain module (Qwen LoRA) | ✅ Operational |
| Supervisor module (YOLO) | 🔄 In progress |
| Full three-module integration | 📋 Planned |
| Local LLM + VLA deployment | 📋 Planned |
| Hardware upgrade (new arm) | 📋 Planned |

---

## Expected Outcomes

- A functional prototype of the full hierarchical VLA system on a robotic arm platform
- Experimental validation across multiple task types and environments
- A standardized data collection and model training pipeline
- Local deployment of LLM + VLA models with real-time performance benchmarks
- Project report, technical documentation, and open-source release
- Potential publications, patents, or further open-source contributions
