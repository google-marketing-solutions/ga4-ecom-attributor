# GA4 Ecom Attributor

This is not an officially supported Google product.

This repository contains a JSON file that can be imported in Google Tag Manager to enable Item List and Promotion attribution in GA4. This does not modify any existing tags, triggers, or variables in the given Tag Manager container.
JSON file contains tags and custom templates that needs to be imported and configured in existing workspace.

**This solutions works only:**
* In web GTM container (solution needs to be configured in web container, but data still can be forwarded to sGTM endpoint in case if you use server-side tagging)
* If you have ecommerce/promotion measurement implemented via GTM and Data Layer (it doesn't work with gtag.js)
* It is compatible both with Universal Analytics and GA4 Data Layer
* If you are planning to use this solution for Item List attribution:
	*  Item ID needs to be available on every ecommerce event you fire to Data Layer (excluding Promotion tracking)
	*  List information needs to be available on Product List Click, Product Detail View or Add2Cart event

**What solution is doing:**
* **Promotion attribution** - user clicks on Promotion, it takes Promotion data, stores them in the cookie called **ga4_promo**. It will store only last clicked promotion data in the cookie. If user makes a purchase, promotion data will be sent to GA4. Data is sent on a purchase level, which means revenue data will be attributed to the last clicked Promotion.

* **Product List attribution** - user clicks on product, it takes List data, stores them in the cookie called **ga4_list_{{item ID}}**. For every item user clicks, it creates new cookie. As user progresses thourgh ecommerce funnel (adds product to the cart, goes to checkout, purchases product), List information will be sent to GA4. When user purchases the product, Item Revenue will be attributed to the appropriate List.

## How to set up the solution

Follow these steps to properly set up the solution:
* Join [this group](https://groups.google.com/g/ga4-ecom-attributor) to get access to JSON file (any potential updates will be published in the group)
* [Download the JSON file](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-web-GTM/ecom-attributor-web-GTM.json)
* Follow implementation guide [on this page](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-web-GTM/README.md)

Once you download the JSON file, you are ready to import it in web GTM container.

## Feedback

If you have feedback or notice bugs, please feel free to raise an issue in GitHub or post in the [group](https://groups.google.com/g/ga4-ecom-attributor). Please note that this is not an officially supported Google product, so support is not guaranteed.
