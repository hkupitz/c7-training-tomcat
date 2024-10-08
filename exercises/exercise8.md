# Introduce compensation

## Goal

In this lab, we will include compensation to show that a task is compensated after a business error happened.

## Short description

* After the error boundary event, remove the task and morph the end event to a **Compensate End Event**
* Attach a boundary event to the **deduct amount from credit** service task and morph it to a **Compensate Boundary Event**
* Add a compensation task by clicking the task icon in the context of the **Compensate Boundary Event** and name it **Refund credit**

## Detailed steps

1. Currently, you should have an **Error Boundary Event** on your **Charge credit card** task. After this charging error, let a sequence flow point to an **End Event**.
2. Morph this **End Event** to be a **Compensate End Event**.
3. Now, add a **Boundary Event** to the **Deduct credit** task.
4. Morph the **Boundary Event** to a **Compensate Boundary Event**.
5. In the context of this **Compensate Boundary Event**, create a Task. This task will not be connected with a sequence flow, but with an **Association**. Name this task **Refund credit amount**.
6. Implement a new `JavaDelegate` that has a simple log output for the refund.