# Handle an exception

## Goal

In this lab, you will throw an exception and then refactor the process to handle this as an incident. You will also test the behaviour

## Detailed steps

1. Open the *CreditCardService* class.
2. Add a new method to the *CreditCardService* to validate the expiry date:
    ```java
	boolean validateExpiryDate(String expiryDate) {
		if (expiryDate.length() != 5) {
		  return false;
		} 

		try {
		  int month = Integer.valueOf(expiryDate.substring(0, 2));
		  int year = Integer.valueOf(expiryDate.substring(3, 5)) + 2000;
		  LocalDate now = LocalDate.now();

		  if (month < 1 || month > 12 || year < now.getYear()) {
			  return false;
		  }

		  if (year > now.getYear() || (year == now.getYear() && month >= now.getMonthValue())) {
			  return true;
		  } else {
			  return false;
		  }
		} catch (NumberFormatException|IndexOutOfBoundsException e) {
		  return false;
		}
  }
    ```
3. Add the example code below to throw an exception if an expired credit card should be charged. Add this snippet to the execute method between the LOG statements.
```java
if (validateExpiryDate(expiryDate) == false) {
  System.out.println("expiry date " + expiryDate + " is invalid");
  throw new IllegalArgumentException("invalid expiry date");
}
```
4. In the process model, select the service task **Charge credit card** and tick `Asynchronous continuations > Before`.
5. Run the process with a payload that has an invalid expiry date.
6. Check Cockpit for the incident and resolve it.