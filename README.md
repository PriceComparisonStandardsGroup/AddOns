# AddOns

## XML Schema
[AddOns.xsd](Schema/AddOns.xsd)

[CreditAgreements.xsd](Schema/CreditAgreements.xsd)

## AddOn Categories List

[AddOn Categories](AddOnCategories.md)

## Finance Plan Definitions
[Finance Plan](Finance/FinancePlan.md)
[Standard Plan](Finance/StandardPlan.md)
[Level Plan](Finance/LevelPlan.md)

## Guidance on Click Through
[GuidanceOnClickThrough.md](GuidanceOnClickThrough.md)

## Contributor Guidelines
[ContributorGuidlines.md](ContributorGuidlines.md)

## Introduction
This is a specification for a universal standard for data exchange between financial services price comparison websites (PCW) and their product provider partners. This proposed standard is in response to the UK legislation ‘PS15/22: Banning opt-out selling across financial services and supporting informed decision making for add-on buyers’ and defines a suggested format for the point of quote (<i>Insurer Response</i>) and the point of click through (<i>Customer Selection</i>) information exchange.  

## Design considerations
-	XML has been chosen to represent Insurer Response data because it is supported by all of the insurance partners for motor and home data transfer and is a common format between all established parties in the market.
-	XSD has been used to formally describe these data as it is the approved method indorsed by the World Wide Web Consortium, which is the main international standards organisation for the World Wide Web.
-	Where possible, naming conventions and entity design choices and naming conventions have been made to support a wide range of open and closed source software vendors.
-	HTTP(S) POST Query String has been used to denote Customer Selection data because it is supported by most of our partners and the precedence has already been set.   
-	Any designs are to address any information exchange formats required to meet the requirements of the FCA Working definition of what is a General Insurance Add-on.  
-	Any designs aims to formally describe these data and minimise misinterpretation or mistakes by consumer applications. 
-	Any designs aims to ensure that we can relay information so our services can be clear, fair and not misleading to a customer.
-	The design aims to minimise effort for PCWs and our Partners.

## Insurer Response
It is unreasonable and impractical to expect our insurers to re-write their entire response schema, however we have proposed a standard for a non-breaking change to add 2 optional collections of “AddOns” and “CreditAgreements” that may exists as a child entities (elements) under their existing policy information they return.  Please refer to supporting documentation of AddOns.xsd and CreditAgreements.xsd for a formal definition on this draft design.
The “AddOns” collection will consist of a collection of products and services that may be free, part of an unbreakable bundle, or incur additional costs and can alter any credit agreement offered by the insure if selected by the customer.

The add-on categories will need to be clearly identified by a standard list that we will agree, but the add-on ‘sub type’, which will denote any specialisation of features, will need to be defined by the insurer. 
For example you may get Breakdown Cover offered by two insures, but the details of what exactly is covered may differ between the partners and as such cannot be standardised.

If an add-on incurs a cost then we identify if the add on can be included in any monthly repayment schedule via a credit agreement (i.e. it is financeable) or if it can only be offered at a one off cost, or a cost at a differing frequency interval. 

For example a user may select breakdown cover. They may then have to pay up front, pay in isolation of the primary policy repayment or allowed to add the cost to the primary policy which will affect their credit agreement and monthly repayment options.

To support this used case we now need a “CreditAgreements” element will allow us to dynamically calculate deposit and monthly repayment amounts based on Customer Selection. This will be achieved by providing tiers of repayment options based on the total cost of financeable add-on and the primary policy. 

For example a customer may not want breakdown cover which means his/her repayment plan will incur a 15% deposit, if they select breakdown cover it may push the total financeable cost into a higher tier that incurs a 25% deposit. 

The tiers will be identified by the min and max price brackets for the total cost of the primary policy and financeable add-ons. Each tier will have a plan type which will denote how the deposit and repayment amounts are calculated.  

## Sample Response Elements - AddOns
```xml
<?xml version="1.0" encoding="UTF-8"?>
<p:AddOns xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	  xmlns:p="urn:PriceComparisonStandardsGroup/20160505/AddOns">

    <!--Scenario A: a bundled add-on, as a bundled item cannot be removed from the primary cover, as such does not have a price. Also we have permitted an optional link to a web page URL that contains further information about the add-on-->

	<p:AddOn xsi:type="p:Bundled" Category="Legal" SubType="lg22568"/>

    <!--Scenario B: a free add-on is has no price, but it can be removed by customer selection-->

	<p:AddOn xsi:type="p:Free" Category="PNCB" SubType="pncB" />

	 <!--Scenario C: an add-on that incurs extra cost, in this case the add-on on can be added to the credit agreement so will result in tier selection-->

	<p:AddOn xsi:type="p:Incremental" Category="Breakdown" SubType="bgPlus" Financeable="true">
		<p:Price Frequency="Annually">25.01</p:Price>
	</p:AddOn>

    <!--Scenario D: an add-on that incurs extra cost, in this case the add-on cannot be added to the credit agreement, must be payed up front so does not affect tier selection-->

	<p:AddOn xsi:type="p:Incremental" Category="Gadget" SubType="gdPlus" Financeable="false">
		<p:Price Frequency="Annually">10</p:Price>
	</p:AddOn>
</p:AddOns>
```
## Sample Response Elements - CreditAgreements
```xml
<?xml version="1.0" encoding="UTF-8"?>
<c:CreditAgreements xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		    xmlns:c="urn:PriceComparisonStandardsGroup/20160505/CreditAgreements">

    <!--Scenario A: Standard plan for amounts for £0 to 999.99, percentage deposit with minimum of £100, fixed setup fee, no min Interest with 10 instalments-->

    <c:Tier Min="0" Plan="Standard" Max="999.99">
        <c:Finance Term="12" Instalments="10"/>
        <c:Deposit Min="100" AmountType="Percentage">10</c:Deposit>
        <c:Interest Percentage="3.79" minimumAmount="0.00"/>
        <c:Charges>
            <c:Charge AddToLoan="true" Name="SetupFee">
                <c:Amount AmountType="Fixed">10.99</c:Amount>
            </c:Charge>
        </c:Charges>
    </c:Tier>

    <!--Scenario B: Standard plan for amounts for £1000 to 1500.50, fixed deposit with fixed setup fee, no min Interest with 10 instalments-->

	<c:Tier Min="1000" Plan="Standard" Max="1500.50">
        <c:Finance Term="12" Instalments="10"/>
		<c:Deposit AmountType="Fixed">500.50</c:Deposit>
		<c:Interest Percentage="3.79" minimumAmount="0.00"/>
		<c:Charges>
			<c:Charge AddToLoan="true" Name="SetupFee">
				<c:Amount AmountType="Fixed">10.99</c:Amount>
			</c:Charge>
		</c:Charges>
	</c:Tier>

	 <!--Scenario C: Standard plan for amounts for £1500.51 to £5000, percentage deposit with no fees, no min Interest with 10 instalments with a loan cap of £1200-->

	<c:Tier Min="1500.50" Plan="Standard" Max="5000">
		<c:Finance Term="12" Instalments="10" Cap="1200"/>
		<c:Deposit AmountType="Percentage">25</c:Deposit>
		<c:Interest Percentage="3.79" minimumAmount="0.00"/>
	</c:Tier>

	<!--Scenario D: Level plan for amounts for £5000.01 to £20000, no deposit with but % setup fee, min interest of £50.99 with interest calculated at 15% with 12 instalments-->

	<c:Tier Min="5000.01" Plan="Level" Max="200000">
		<c:Finance Term="12" Instalments="12" />
		<c:Interest Percentage="15" minimumAmount="50.99"/>
		<c:Charges>
			<c:Charge AddToLoan="false" Name="SetupFee">
				<c:Amount AmountType="Percentage">1.5</c:Amount>
			</c:Charge>
		</c:Charges>
	</c:Tier>
</c:CreditAgreements>
```

## Sample Response Elements - How to include the new elements in a pre-existing insurer response 
```xml
<!--This example is used to explain why we need a new standard and how the new standard can be added to an existing insurer response, 
	the example already includes te insurers internal standard for 'ancillaries'-->
<quote xmlns="http://www.exampleinsurer.co.uk" 
		   xmlns:a="urn:PriceComparisonStandardsGroup/20160505/AddOns" 
		   xmlns:c="urn:PriceComparisonStandardsGroup/20160505/CreditAgreements">
	<quoteref>C6F63B-OOGOG</quoteref>
	<price>
		<policyterm>
			<term>12</term>
			<premium>158.00</premium>
		</policyterm>
		<xs>
			<vol>250</vol>
			<comp>0</comp>
			<total>250</total>
			<fire>150</fire>
		</xs>
	</price>
	<!--This is the orginal insurers standard for add-ons, the original format tell us that we have 3 possible add-ons-->
	<ancillaries>
		<!--This 'Hire Car' denotes an add-on but we don't know if it is financeable-->
		<ancillary>
			<name>Hire Car Plus</name>
			<price>28.25</price>
			<included>false</included>
		</ancillary>
		<!--This 'Legal Cover' denotes a free, or a bundled add-on, but we don't know which one-->
		<ancillary>
			<name>Legal Cover</name>
			<price>0</price>
			<included>true</included>
		</ancillary>
		<!--This 'Windscreen' denotes an add-on but we don't know if it is financeable-->
		<ancillary>
			<name>Windscreen</name>
			<price>9.39</price>
			<included>false</included>
		</ancillary>
	</ancillaries>
	<!--This additive section denotes the 'ancillaries' in the AddOns format, we now know that Legal is free and not bundled, CourtesyCar can be selected 
         and the value added to the total amount repayable, but Windscreen cover must be payed up front, The SubTypes allow customer selection of add-ons 
         in a format that is already understood by the insurer-->
	<a:AddOns>
		<a:AddOn xsi:type="p:Free" Category="Legal" SubType="Legal"/>
		<a:AddOn xsi:type="p:Incremental" Category="CourtesyCar" SubType="Hire Car Plus" Financeable="true">
			<a:Price Frequency="Annually">28.25</a:Price>
		</a:AddOn>
		<a:AddOn xsi:type="p:Incremental" Category="Windscreen" SubType="Windscreen" Financeable="false">
			<a:Price Frequency="Annually">9.39</a:Price>
		</a:AddOn>
	</a:AddOns>
	<!--This additive section denotes the possible repayment plans on offer by the insurer, in this scenario two tiers have to be returned because the 
        customer could, post quote, select an add-on that push the total amount repayable to the second tier in the collection which has higher interest 
        rate and higher minimum deposit-->
	<c:CreditAgreements>
		<c:Tier Min="0" Plan="Standard" Max="175">
			<c:Finance Term="12" Instalments="11"/>
			<c:Deposit Min="5" AmountType="Percentage">10</c:Deposit>
			<c:Interest Percentage="5" minimumAmount="0.00"/>
			<c:Charges>
				<c:Charge AddToLoan="true" Name="SetupFee">
					<c:Amount AmountType="Fixed">10</c:Amount>
				</c:Charge>
			</c:Charges>
		</c:Tier>
		<c:Tier Min="175" Plan="Standard" Max="300">
			<c:Finance Term="12" Instalments="11"/>
			<c:Deposit Min="15" AmountType="Percentage">10</c:Deposit>
			<c:Interest Percentage="10" minimumAmount="0.00"/>
			<c:Charges>
				<c:Charge AddToLoan="true" Name="SetupFee">
					<c:Amount AmountType="Fixed">12</c:Amount>
				</c:Charge>
			</c:Charges>
		</c:Tier>
	</c:CreditAgreements>
</quote>
```
