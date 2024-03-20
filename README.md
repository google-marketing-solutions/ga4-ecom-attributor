# GA4 Ecom Attributor

This is not an officially supported Google product.

This repository contains a JSON files that can be imported in Google Tag Manager to enable Item List and Promotion attribution in GA4. This does not modify any existing tags, triggers, or variables in the given Tag Manager container.
JSON file contains tags and custom templates that needs to be imported and configured in existing workspace.



## What is GA4 Ecom Attributor?


Advertisers often in their ecommerce implementation have List information only available when user clicks on the item inside the Item List. Similar is for Promotion - information is usually available only when user clicks on specific Promotion.
In order to get full data in GA4 Item List and Promotion reports, Item List and Promotion data needs to be sent with all ecommerce events.

Often, due to the complexity of a website, it is hard for advertisers to provide this information with every single ecommerce event, which creates a challenge how to make these reports actionable.

This solution is designed to pass List and Promotion data with every ecommerce event. Each time when user interacts with item or promotion (which contains List or Promotion information), List and Promotion information will be stored. Depending on which version of solution you are using, information will be stored either in 1st party cookie (Ecom Attributor for web GTM) or in Firestore (Ecom Attributor for sGTM). This data is then passed along with all subsequent ecommerce events to GA4.

As a result, Item List and Promotion attribution will fully work in GA4, making Item List and Promotion reports fully populated with ecommerce data and ready to be used for analysis.










**GA4 Ecom Attributor has two versions of solution:**
* **Ecom Attributor for web GTM:** this solution needs to be implemented in web GTM container and it relies on First party cookies as a storage solution for List and Promotion data.
* **Ecom Attributor for sGTM:** this solution needs to be implemented in server-side GTM container and it relies on Firestore as a storage solution for List and Promotion data.




### Comparison between two versions of solution:

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/sgtm-vs-webgtm-comparison.png" width="990" height="456"></p>


## How to set up the solution

Follow these steps to properly set up the solution:

**1.)** Join [this group](https://groups.google.com/g/ga4-ecom-attributor) to get access to JSON file (any potential updates will be published in the group)\
**2.)** Depending on which version of solution you want to use, click on one of the following links:

* **Ecom Attributor for web GTM:**
	* [Github page](https://github.com/google/ga4-ecom-attributor/tree/main/ecom-attributor-web-GTM)
	* [JSON file](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-web-GTM/ecom-attributor-web-GTM.json)
	* [PDF with implementation instructions](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-web-GTM/ecom-attributor-web-GTM-implementation-guide.pdf)	

* **Ecom Attributor for sGTM:**
	* [Github page](https://github.com/google/ga4-ecom-attributor/tree/main/ecom-attributor-sGTM)
	* [JSON file](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/ecom-attributor-sGTM.json)
	* [PDF with implementation instructions](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/ecom-attributor-sGTM-implementation-guide.pdf)	 




## Feedback

If you have feedback or notice bugs, please feel free to raise an issue in GitHub or post in the [group](https://groups.google.com/g/ga4-ecom-attributor). Please note that this is not an officially supported Google product, so support is not guaranteed.
