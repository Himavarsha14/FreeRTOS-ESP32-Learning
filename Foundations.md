# Phase 1 — FreeRTOS Foundations (Theory Only)

## What is FreeRTOS?
FreeRTOS is a Real-Time Operating System used in microcontrollers to run multiple tasks with deterministic timing.

## Why we use FreeRTOS?
- Multitasking support
- Better structure than super-loop coding
- Supports scheduling, queues, semaphores, timers
- Very lightweight

## Important Concepts
- Task: independent thread
- Scheduler: decides which task runs
- Tick: time unit for scheduling
- Context switching: switching from one task to another
- Priority: high priority → runs first

## Task States
- Running
- Ready
- Blocked (waiting for delay or semaphore)
- Suspended
- Deleted

## Scheduling
- Preemptive: high priority interrupts low priority
- Cooperative: tasks must yield

## Priority Inversion
Low priority holds resource, high priority waits → problem.
Solution: Mutex with priority inheritance.

## ISR Rules
- ISR must be short
- Use xQueueSendFromISR, xSemaphoreGiveFromISR
- Use portYIELD_FROM_ISR when needed

## Memory Concepts
- Each task has its own stack + TCB
- Heap types: heap_1 ... heap_5
- Static allocation avoids fragmentation

