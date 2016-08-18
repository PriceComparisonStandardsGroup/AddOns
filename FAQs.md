
FAQs
====
 
1.	Why do the approaches of the different PCWs differ?
-------------------------------------------------------

In essence they don’t – we all agree on the technical solution required to solve this challenge, there are some important points to make though:

a.	Most commonly selected add ons – the FCA have not stated what the most commonly selected add ons are, so each PCW may have a   different interpretation of what they are

b.	Dynamic .xml or static table driven solutions – the PCW working group has proposed the new .xml schema as a preferred way to deal with the FCA guidance. Some businesses however, may not be able to accommodate this by the required date and so businesses can use a static table driven solution. It is important to note however, that the PCW working group believes that businesses that implement the dynamic .xml solution will be at an advantage over those that don’t

2.	How will the PCWs cope with providers different levels of cover for add ons?
--------------------------------------------------------------------------------

The schema allows for the category to be defined, and a sub category populated which can contain this information. Please refer to the technical specification to see how this has been proposed. Please read ’Guidance on Click Through’ section on https://github.com/PriceComparisonStandardsGroup

3.	How will PCWs and Insurance providers deal with mis matched premiums on click through from the PCW to the provider landing page?
------------------------------------------------------------------------------------------------------------------------------------

Mismatches will be governed by:

a.	The technical solution has attempted to standardise calculations of premiums – please refer to the technical specification. Please read ’Guidance on Click Through’ section on https://github.com/PriceComparisonStandardsGroup

b.	PCWs will send through a premium value as a checksum for the provider to match against. This is not meant to be a validation sum, rather a means to identify mismatches and message customers appropriately. Based on the assumptions that constituent premium components will be sent by the provider, and the calculations standardised, it is not anticipated that mismatches will be commonplace. The working group expects that prices returned to the PCWs and selected by customers as per contractual arrangements will need to be honoured by the providers.

4.	How will the customer selection be incorporated into the Top X feed?
------------------------------------------------------------------------

The working group has proposed that customer selection of add ons will not be incorporated into the various Top x feeds. The rationale being that for an offline journey, it is commonplace for agents to re-establish the customers’ requirements during the duration of a call

5.	“As a provider, I’m not comfortable with the PCWs effectively holding information on my finance charging structure”
-----------------------------------------------------------------------------------------------------------------------
 
The only alternative to this approach is for the PCW to re-quote each time a customer selects or deselects an add on. This is not acceptable for two reasons, firstly providers are unlikely to be able to cope with the additional quote capacity required (as customers are likely to perform these actions regularly and frequently) and secondly, this is not an acceptable customer journey. Each re-quote would have an associated wait time, so if customers select and deselect a number of times as we anticipate they will, it could mean the overall time spent on the customer journey would be unacceptably long; potentially leading to drop off and poor sales conversion.
 
 **Important note:**
 The technical specification contains the majority of information required to answer your questions. If you have a specific question, not answered by the existing documentation or this FAQ, please log an issue on githib, so the PCW has visibility and can respond as a working group.
 
 


