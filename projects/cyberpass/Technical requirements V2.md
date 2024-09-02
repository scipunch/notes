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
alt Tablet
	System -> Manager : QR-code for the payment
	Manager -> Customer : Show payment QR-code
else PC
	System -> Manager : Show QR-code on the special page
end
Customer -> System : Get cart info
alt empty customer email
	System -> Customer : Request email for the bill
	Customer -> System : Update email
	System -> Database : Update customer's email
end
Customer -> PaymentSystem : Payment
PaymentSystem -> Customer : Redirect to the payed url
Customer -> System : Payment successful
alt PC
	System -> Manager : Hide QR-code for the payment
end
System -> Database : Update customer's balance & save transaction
System -> Manager : Publish pdated balance
```
1. What if the customer will cancel a redirect to the `success payment` URL?
2. QR-code design