# Collinson

Architecture Overview

The architecture chosen is a distributed micro-service design utilising discrete domain services. This should allow for the following benefits:
- Scalability and Flexibility
- Parallel development utilising small teams for each service
- Easier testing as each service can be tested in isolation
- Better maintainability, as changes to a service can be confined to that service
- Simplicity - small services are easier to document and understand for new staff

Where possible we will use third parties to handle areas that are not the core focus of the PriorityPass business, such as taking payments. This implies a need for integrations utilising API's. This, in turn, suggests the use of a commercial API Gateway product to ensure security (e.g. authentication and DDOS protection), robustness and scalability.

Technical choices:
- Leave the payment process with the Tax platform. Simplifies our design, reduces our PCI/DSS exposure
- Use OAuth2.0 authentication flow to integrate the two accounts. This allows a smoother flow for the customer for subsequent bookings. Allows the customer to see all their taxi journeys under their account in the Taxi app whether booked through PriorityPass or not.
- Microservice design - for reasons detailed above.
- Separate database for each service (where needed). This achieves clean separation of the data confining it to the service responsible for that data domain. This also allows different DB technology to be chosen, if desired.
- Orchestrated journey flow - better able to handle complex flows including error flows and compensating calls when a service fails. The alternative of choreographed services would introduce more complex interdependance between the services. For instance, if the Taxi app cannot fulfil a booking request (maybe they don't have any availability) the orchestrator can rewind the process and try for a different time slot.
This also separates the workflow logic into one service instead of this being spread amongst several services.


PCI Compliance

The solution will rely on the Taxi Partner to take the payments by charging the journey to the customer's 'Taxi' account. This relieves PriorityPass from the need to handle payments and therefore frees us from any PCI/DSS concerns.
This relies on the assumption that the customer will have an account with the Taxi partner.
If this assumption is incorrect, and the Taxi journeys need to be paid for by the customer through the PriorityPass app then we will need to use a payment partner, such as Stripe. Stripe is certified as a PCI Level 1 Service Provider and we can use them to handle the payment process. We will then have to pass the revenue on to the Taxi app platform in some back-office reconciliation process, perhaps running at end-of-day. Stripe provides technical approaches for taking payments without the need for the PriorityPass platform to touch card-holder data through the use of hosted payment fields. We will only store the tokenised card number and last four digits of the card to allow the customer to identify which card was used. We would only have to perform a Self Assessment Questionnaire annually to confirm our PCI/DSS approach.


Omissions & Trade-offs:

Performance Aspects - depending on the load and peak rates this solution may well need further technical features to cope, for instance by using caching and de-coupling components using messaging queues. This would be something that should be considered when the load profile is known and after testing the architecture to see how it performs. The distributed nature of the design should allow services to be scaled easily, but the amount of scaling and how the load is distributed would need some work.

UI design could be better. There is probably a lot more needed around choosing addresses and pick up times. Probably needs an expert in UX to get it smoother.
