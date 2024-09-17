# Add Data Handling to Delegates

## Goal

In this lab, we will add data handling to the delegate code. Then, we will adjust the JUnit test.

## Short description

* Add data handling to the delegates
* Adjust conditional expressions on sequence flows
* Adjust the Junit test to use the new variable handling

## Detailed steps

1. Add the provided services to your project.
2. Adjust your credit deduction delegate to use the `CustomerService` by calling its `getCustomerCredit(String customerId)` method. Fetch the needed process variable and supply it to the service method.\
The method returns the customer's credit by taking the last two digits of the `customerId`. Save it in a new process variable called `customerCredit` using the `setVariable()` method of the `DelegateExecution`.

3. Next, use the `deductCredit(String customerId, Double amount)` method of the `CustomerService` to deduct the order amount from the customer's credit.\
The method will return the open amount to pay by credit card. Save it in a process variable called `openAmount`. 
5. Now, we can adjust the expressions on the sequence flows to use the data provided by the services. For the `Yes`-Path, enter `${openAmount <= 0}`. For the `No`-Path, enter `${openAmount > 0}`.
6. Adjust your credit card delegate to use the `CreditCardService` by calling its `chargeAmount(String cardNumber, String cvc, String expiryDate, Double amount)` method. Fetch the needed process variables and supply them to the service method.\
The method for now will simply print a log output.
6. Restart your process application and run the process by starting it via the Desktop Modeler. Supply the following payload:

```json
{
  "orderTotal": { "value": 30 },
  "customerId": { "value": "cust20" },
  "cardNumber": { "value": "1234 5678" },
  "cvc": { "value": "789" },
  "expiryDate": { "value": "09/24" },
}
```

Then, inspect the history of the process instance.
6. Finally, we will have to adjust the JUnit test. Add more variables to the map before starting the process and remove the line containing the `customerCredit`:
   ```java
   variables.put("customerId", "cust20");
   variables.put("cardNumber", "1234 5678");
   variables.put("cvc", "789");
   variables.put("expiryDate", "09/24");
   ```
7. Run your unit test and inspect the log output.