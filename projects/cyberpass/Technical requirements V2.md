```plantuml
actor Customer
actor Manager
participant System
database Database
actor PaymentSystem

autonumber
Customer -> System : Scan QR-code
Customer -> Manager : Choose minutes package
Manager -> System : Setup choosed minutes packages
System -> Manager : QR-code for the payment
Manager -> Customer : Show payment QR-code
Customer -> System : Get cart info
alt empty customer email
	System -> Customer : Request email for the bill
	Customer -> System : Update email
	System -> Database : Update customer's email
end
Customer -> PaymentSystem : Payment
PaymentSystem -> Customer : Redirect to the payed url
Customer -> System : Payment successful
System -> Database : Update customer's balance & save transaction
System -> Manager : Publish pdated balance
```
1. What if the customer will cancel a redirect to the `success payment` URL?