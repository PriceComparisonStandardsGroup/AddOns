# Guidance On Click Though

On Customer Selection, bundles should not be needed to be specified in the click through information as by definition are unbreakable from the primary policy, we only need confirmation from partners that they will honour this scenario. 

It is also debatable if we need to specify if a customers does not want a free add-on as they have not incurred a penalty to be given a free service but this system will permit us to specify the opt out pf a free add-on.   
For Add-ons that incur additional cost we will need to provide opt-in flags on click through and also denote the sub type of add-on that was selected. 

We already have a precedence to do this for items such as protected no claims bonus and legal cover using HTTP Form POST of a query string. We suggest keep to this as it is the lowest common denominator, and we know that the majority of our partners support this.
It will also make it very easy for PCWs to implement this feature in the presentation layer, however it is prudent for a PCW to log customer interaction for compliance and customer support reasons. 

When we do get a finalised add-on category list, we should use our definition names as the ‘name’ of a query string parameter, and the ‘value’ to denote the sub type the customer has selected.

The details of the expected repayment plan, this is to allow insurers to identify a mismatch between the insurer and PCW repayment plan algorithm and report the issue and remedy this situation. This error condition should only occur if a new type of algorithm is invented that is not defined in the standards.      

To define a standard, this could be:
- HTTPS transport only, to protect our customers from transaction sniffing.
- POST only, not only to avoid the message size and encoding restrictions on GETS, also to secure any transactions.
- Query string parameter names must be case sensitive to the standard Product/Services list we will finally agree on, with a Boolean value, true to denote opt-in.
- Omitted parameters we will be an implicit opt-out, but can be defined explicitly. This will allow add-on variety growth without breaking the standard and accidentally opting-in customers for new products.  
- We need to send paramiters that denote the <i>expected</i> annual and monthly repayment plans as means to allow for the insurer to detect significant repayment calculation mismatch.

Sample Http Requests (Click-through Requests)
For example, say we have the following product/services list (note that the URL is an example and does not indicate a standard):
```
CourtesyCar (which is a free add-on, it subtype has been defined a ‘basic’ by insurer)
ProtectedNoClaimsBonus
Breakdown (with a partner subtype of bdownplus they use to define their top level)
LegalExpenses
```

In this scenario the customer only wants Breakdown Cover, but allowed the free courtesy car cover to remain selected.
```
POST https://www.insurealpha.com/car/buyonline.asp HTTP/1.1
Host: w3schools.com
quoteref=19f313b4&BreakdownCover=bdownplus&CourtesyCar=basic&Deposit=12.55&Installments=9&InstallmentAmount=12.55&TotalRepayable=125.50&Total=120 
```

In this scenario the customer only wants Breakdown Cover, but the PCW has explicitly removed Courtesy Car Cover 
```
POST https://www.insurebeta.com/motor/homepage.php HTTP/1.1
Host: w3schools.com
quoteref=19f313b4&BreakdownCover=bdownplus&Deposit=12.55&Installments=9&InstallmentAmount=12.55&TotalRepayable=125.50&Total=120  
```
