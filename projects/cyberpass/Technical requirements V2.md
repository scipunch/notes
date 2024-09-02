```plantuml
actor Customer
actor Manager
participant System
database Database

Customer -> System : Scan QR-code
Customer -> Manager : Choose minutes package
Manager -> System : Setup choosed minutes packages
System -> Manager : QR-code for the payment
Manager -> Customer : Show payment QR-code
Customer -> System : Get cart info
```
