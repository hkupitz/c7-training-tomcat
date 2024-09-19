# Simulate an External Task Worker

## Goal

Change your payment process model to use an external task worker for the credit deduction. Then simulate its execution by using the REST API.

## Detailed steps
### Process Modeling
1. In the payment process, select the task `Deduct credit`.
2. Change the implementation type to **External**.
3. Enter the topic `creditDeduction`.

### Simulation of External Task Worker
4. Take a look at the [documentation](https://docs.camunda.org/rest/camunda-bpm-platform/7.21/#tag/Process-Definition) to find out how to start a process instance via the Camunda 7 REST API. 
5. Start a process instance of the order process via the REST API. You can use a REST client of your liking, such as Postman, [Bruno](https://github.com/usebruno/bruno/releases/download/v1.29.1/bruno_1.29.1_x64_win.zip) or even `curl`. If you want you can import the OpenAPI specification in Bruno or Postman from this URL: https://start.camunda.com/openapi.json
4. Check the documentation of the `External Task` endpoint: https://docs.camunda.org/rest/camunda-bpm-platform/7.21/#tag/External-Task
5. *Fetch & lock* and then *Complete* the external task in your payment process instance using the respective REST requests. Make sure that the complete process chain (Order + Payment) runs through successfully end-to-end.