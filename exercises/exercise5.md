# Message Events

## Goal

You create a new process model to handle orders. The payment gets started by receiving a message from the order process. The order process continues once the payment is completed.

## Detailed steps

### Process modeling

1. Open the Modeler and create a new BPMN diagram. Model an order process with the following elements:
    * Start event **Order received**
    * Send task **Invoke payment**
    * Receive event **Payment completed**
    * End event **Order completed**

2. Fill in the technical attributes:
    1. Process Id: **OrderProcess**
    2. Process name: **Order process**
    4. Send Task implementation: Type **Java Class**, Java class **org.camunda.training.SendPaymentRequestDelegate**
    5. Message Intermediate Catch Event: Open the Message section in the property panel and add a new Global message reference. Enter **paymentCompletedMessage** as Name.
3. Save the process model in the src/main/resources folder of your project. Name it **order.bpmn**.

### Message sending

4. Create a new class `SendPaymentRequestDelegate`. It should implement the `JavaDelegate` interface.
    ```java
   package org.camunda.training;

   import org.camunda.bpm.engine.delegate.DelegateExecution;
   import org.camunda.bpm.engine.delegate.JavaDelegate;
   
   public class SendPaymentRequestDelegate implements JavaDelegate {  
   
     @Override
     public void execute(DelegateExecution execution) throws Exception {

       // Here goes the message sending logic     
     }
   }
    ```

5. To send the **paymentRequestMessage** to the payment process and pass all variables as well as the business key from the order process, add the following implementation in the `execute` method of your delegate. The result of the correlation returns the process instance ID of the payment process. It will be saved as a new process variable when the Send Task gets completed.
    ```java
    ProcessInstance processInstance = execution
        .getProcessEngineServices()
        .getRuntimeService()
        .createMessageCorrelation("paymentRequestMessage")
        .setVariables(execution.getVariables())
        .processInstanceBusinessKey(execution.getProcessBusinessKey())
        .correlateStartMessage();
    execution.setVariable("paymentProcessInstanceId", processInstance.getId());
    ```

### Message receiving

6. Open the Modeler and open the Payment process. Change the start event to a Message Start Event. Open the Message section in the property panel and add a new Global message reference. Enter **paymentRequestMessage** as Name.
7. Change the end event to a Message End Event. Set the Implementation to the type `Java Class`: `SendPaymentCompletionDelegate`.
8. Create another delegate that sends a message back to the order process. The correlation happens via the businessKey in this implementation.
   ```java
   package org.camunda.training;

   import org.camunda.bpm.engine.delegate.DelegateExecution;
   import org.camunda.bpm.engine.delegate.JavaDelegate;

   public class SendPaymentCompletionDelegate implements JavaDelegate {
   
     @Override
     public void execute(DelegateExecution execution) throws Exception {
       execution
       .getProcessEngineServices()
       .getRuntimeService()
       .createMessageCorrelation("paymentCompletedMessage")
       .processInstanceBusinessKey(execution.getBusinessKey())
       .correlate();
     }
   }
   ```

### Acceptance testing

11. Start a process instance from the Modeler using this payload:
    ```json
    {
      "variables": {
        "orderTotal": {
          "value": 49.99,
          "type": "Double"
        },
        "customerId": {
          "value": "cust25",
          "type": "String"
        },
        "cardNumber": {
          "value": "1234 5678"
        },
        "CVC": {
          "value": "123"
        }, 
        "expiryDate": {
          "value": "09/24"
        }
      },
      "businessKey": "Order test 1"
    }    
    ```

    What happens?
12. We will deal with the problem in the next exercise.