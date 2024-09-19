# Add a Decision Table About Discount

## Goal

Add a Business Rule Task to the order process and calculate a discount for the order based on the order total.

## Detailed steps
### Decision Modeling
1. Open the Modeler and create a new DMN diagram.
2. Name the decision table **Order discount** and set the Id of the decision table to **orderDiscount**.
3. Open the decision table and name the input column **Order amount**. Set the input expression to **orderTotal**. Select **double** as type to match your order total variable type.
4. Label the output column as **Discount percentage** and name the output variable **discount**. Set the type to **integer**.
5. Add some rules to discount orders. An example is shown below. Use **Unique** for the hit policy to allow a single, unique result.
![image](https://user-images.githubusercontent.com/5269168/195629261-549a3e16-dc5e-4555-b444-5177ad432a30.png)
7. Save the decision table to the `src/main/resources` folder of your process application.

### Process Modeling
9. Open the order process in the modeler.
10. Add a task to the order process to get the discount for the order before the payment invocation. Name the task **Get discount**. Change the task type to **Business Rule Task**.
11. In the implementation section of the property panel select **DMN** as type. Add the id of the decision table (**orderDiscount**) as the decision reference. Enter **discount** as the result variable name. Select **singleEntry** as for the decision result mapping.
12. Add a task between the **Get discount** and **Invoke payment** tasks to apply the discount to the order amount. Name the task **Apply discount**.
13. Change the task type to **Script Task**. Open the script section in the properties panel. Enter **javascript** as the format. Select **Inline script** as the type. Include the following script:
```javascript
orderTotal - (orderTotal * discount / 100)
```
14. Name the result variable **discountedAmount**.
15. To pay only the discounted amount, map the order total that is passed to the payment process with an input mapping for the **Invoke payment** task. In the send task implementation (**SendPaymentRequestDelegate**), the local variable is preferred over the global one.
16. Select the **Invoke payment** task. Open the input section. Click on the **+** to add an input mapping. Enter **orderTotal** as the local variable name. Select **String or Expression** as the assignment type. Enter **${discountedAmount}** as the value to map the result from the decision evaluation.

### Acceptance Test
17. Start a process instance.
18. Open Cockpit and check the history of the order and the payment process. Is the discount applied correctly?