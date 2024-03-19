# GA4 Ecom Attributor for web GTM

This is not an officially supported Google product.

This repository contains a JSON file that can be imported in web Google Tag Manager container to enable Item List and Promotion attribution in GA4. This does not modify any existing tags, triggers, or variables in the given Tag Manager container.
JSON file contains tags and custom templates that needs to be imported and configured in existing workspace.

**This solutions works only:**
* In web GTM container (solution needs to be configured in web container, but data still can be forwarded to sGTM endpoint in case if you use server-side tagging)
* If you have ecommerce/promotion measurement implemented via GTM and Data Layer by following Google documentation (it doesn't work with gtag.js, custom implementation or third-party tag management system)
* It is compatible both with Universal Analytics and GA4 Data Layer
* If you are planning to use this solution for Item List attribution:
	*  Item ID needs to be available on every ecommerce event you fire to Data Layer (excluding Promotion tracking)
	*  List information needs to be available on Product List Click, Product Detail View or Add2Cart event

## How to set up the solution

Follow these steps to properly set up the solution:
* Join [this group](https://groups.google.com/g/ga4-ecom-attributor) to get access to JSON file (any potential updates will be published in the group)
* [Download the JSON file](https://github.com/google/ga4-ecom-attributor/blob/main/ga4-ecom-attributor.json)
* Follow implementation guide (explained below) or use [PDF file](https://github.com/google/ga4-ecom-attributor/blob/main/GA4_ecom_attributor_implementation_guide.pdf)

Once you download the JSON file, you are ready to import it in web GTM container.


## Import JSON file

To import JSON file, go to [Google Tag Manager](https://tagmanager.google.com/#/home) and open your web container where you want to set up the solution. Navigate to Admin -> Import container:
* Under "Select file to import" select downloaded JSON file
* For workspace, select "Existing" and then select one of your existing workspaces where you want to import the tags
* For import option, select "Merge" -> "Rename conflicting tags, triggers, and variables"

Click on "Confirm" button once you are ready to import the file. Once you import JSON file, you should have these tags and variables imported:\
<img src="https://github.com/google/ga4-ecom-attributor/blob/main/images/GTM-imported.png" width="500" height="530">

## Configure Cookie creator tag

After importing JSON file, go to Tags section and find tag called **[GA4] Cookie creator for Item list & Promotion attribution**

Cookie creator tag will be responsible for creating cookies and storing List or Promotion information in the cookie and deleting the cookies after the purchase.

In the tag, you need to configure following settings:

##### 1. Data Layer type
In dropdown, select which type of Data Layer you have on your website: [Google Analytics 4](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce?client_type=gtm) or [Universal Analytics](https://developers.google.com/analytics/devguides/collection/ua/gtm/enhanced-ecommerce)


##### 2. Which information you want to collect?

In this section, you need to select which information you want to collect:

* Promotion attribution
  * By selecting this option, you need to provide exact event name you use to push Promotion Click information into Data Layer. For example, if you push Promotion Click data to Data Layer with event called "select_promotion", this is what you need to provide in the input field
  * Same event name that you provide in this input field needs to be added as a trigger for Cookie creator tag

* Product List attribution
  * By selecting this option, you need to provide exact event names you use to push Item List information into Data Layer. In total, there are three input fields. In some cases, webshops have List information can be available on multiple ecommerce actions (e.g. Product List Click, Detail View and Add to Cart).
If you have List information available on multiple ecommerce actions, please fill out corresponding fields.
  * In case if you have List information available only on one ecommerce action (e.g. Product List Click), provide event name only in that field. Rest of the fields leave empty.
  * Same event name that you provide in this input field needs to be added as a trigger for Cookie creator tag

**IMPORTANT: In order for solution to work, you must specify at least one ecommerce event which contains list information in Data Layer.**

##### 3. Purchase event 

In this input field, provide exact event name you use to push purchase information to Data Layer. For example, if you use event called "purchase" to push data to Data Layer, this is what you need to provide in the input field.\
Again, event name that you provide in this input field needs to be also added as a trigger for Cookie creator tag.

##### 4. Modify cookie expiration
By default, all cookies this tag creates will be browser-session. That means cookies will expire after browser session ends. This is setting we recommend to keep.

However, if you want to extend cookie expiration, select option "Modify cookie expiration setting?" and provide value in seconds.
For example, if you want for cookies to expire after 24 hours, put value 86400 in input field.

##### 5. Tag firing priority
Please don't remove Tag firing priority setting. This tag needs to have lower firing priority compared to [GA4] Ecommerce events tag.\
This will ensure that data is sent to GA4 before cookies are deleted.

##### 6. Triggers for Cookie creator tag

Triggers for Cookie creator tag will depend based on your tag configuration. Events you provided in tag configuration also needs to be added as triggers, otherwise solution will not work!\
\
For example:
* If you selected to track Promotion attribution, and in input field you specified event name "select_promotion", this event needs to be added as trigger as well
* If you also selected to track Product list attribution, and in input field you specified event name "select_item", this event also needs to be added as trigger
* Event you specified as a Purchase event needs to be added as trigger

So, depending on your tag configuration, in total you should have 2-5 triggers on Cookie creator tag:\
<img src="https://github.com/google/ga4-ecom-attributor/blob/main/images/cookie-creator-tag.png" width="500" height="645">


#### How tag works and what is it's purpose?
Based on options you selected, tag will do the following:
* If you selected to collect Promotion attribution - When user clicks on Promotion, Cookie creator tag will fire (Promotion trigger you added to Cookie creator tag). It will check Data Layer, take Promotion data and store it in the cookie called "ga4_promo". If you click on multiple promotions, data is collected only for last clicked promotion.

* If you selected to collect Product List data - when user clicks on product which contains List information, Cookie creator tag will fire (trigger you added to Cookie creator tag). It will check Data Layer, take List information and store it in the cookies called "ga4_list_{{item ID}}". For every product, new cookie will be created. In case if purchased product had "ga4_list_{{item ID}}" cookie, cookie will be deleted on a purchased event. 

* On purchase event, it will delete "ga4_promo" cookie if it exists in the browser. It will check all purchased products, and if any of purchased products had "ga4_list_{{item ID}}" cookie in the browser, it will delete the cookie. 


## Modify your GA4 ecommerce event tags

Your ecommerce implementation can vary. For example, you can use only 1 tag to send all ecommerce events to GA4 or you can use separate tag for each ecommerce event. 

The changes that you need to make are exactly the same, it only depends on how many tags you need to make them:

* Disable "Enable ecommerce data" option in every ecommerce tag - since we need to modify items array before we send data to GA4, we need to disable option to take data automatically from dataLayer
* add `items` event parameter and as value provide `[custom] GA4 Items array` variable you imported in GTM - We need to provide items array and our custom variable which will build items array with all List attribution data

NOTE: These changes only needs to be done on ecommerce event tags! You don't have to do these changes on tags that you use to send promotion data to GA4 (Promotion Impression and Promotion Click events).

To summarize, you should made these changes to all ecommerce event tags in GTM (except promotion tags):\
<img src="https://github.com/google/ga4-ecom-attributor/blob/main/images/adjusting-ecommerce-tags.png" width="500" height="535">


* And you have to adjust purchase event tag - for purchase event tag, since it will not take data automatically from data layer anymore, you need to manually specify all event level purchase-related parameters that you have in dataLayer:
  *  `transaction_id`
  *  `value`
  *  `currency`
  *  `tax`
  *  `shipping`
  *  `coupon`
   
For example, if on purchase event tag you have transaction_id, currency, value and shipping info, provide those parameters in purchase tag in GTM.
Example how you should add event-level purchase related parameters. As value, create data layer variable to capture corresponding information from dataLayer:\
<img src="https://github.com/google/ga4-ecom-attributor/blob/main/images/adding-event-level-purchase-parameters.png" width="500" height="505">



## Optional - Send promotion data to GA4

You need to do these modifications only if you:\
a) have Promotion data tracking on your website (promo Impression and promo Click) **AND**\
b) if you set up in Cookie creator tag to collect Promotion information in cookie

If this is the case for you, you need to provide additional 4 parameters to each ecommerce even tag in GTM (not necessary to do changes on Promotion Impression and Promotion Click tags):
* promotion_id
* promotion_name
* creative_name
* creative_slot


So, for each ecommerce event tag, you should add these 4 parameters, and as value provide variables you imported with JSON file:\
<img src="https://github.com/google/ga4-ecom-attributor/blob/main/images/promo-data.png" width="500" height="430">

NOTE: it is not necessary to do add these parameters in Promotion tags (view_promotion and select_promotion event tags)!



## Final checklist

If you followed instructions above, you should have solution ready. Let's quickly recap what settings you needed to configure:
1. **Cookie creator tag**
    *  In dropdown, you specified which Data Layer you use on your website
    *  If you selected Promotion attribution option, you provided exact event name you use to push Promotion Click information in Data Layer. Same event should be added as trigger to Cookie creator tag.
    *  If you selected Product List attirbution option, you provided exact event name you use to push List information in Data Layer. Same event should be added as trigger to Cookie creator tag.
    *  You provided Purchase event. Same event should be added as trigger to Cookie creator tag.
    *  Triggers for Cookie creator tag are added based on previous options you selected.

2. **Modify all your ecommerce event tags in GTM (not needed to do these changes on Promotion event tags)**
    *  Disable option "Send Ecommerce data"
    *  add `items` event parameter, and as value provide variable that you imported: `[custom] GA4 Items array`
    *  In purchase event tag, add necessary information (depending which info you have in dataLayer): `transaction_id`, `value`, `currency`, `shipping`, `tax`, `coupon`

3. **Optional - Add promotion parameters to send promotion data to GA4**\
NOTE: this needs to be done only if you have Promotion data tracking on your website (view_promotion and select_promotion events) **AND** if you set up in Cookie creator tag to collect Promotion information in cookie.
    * If this is true, then in every ecommerce event tag, add 4 event parameters: `promotion_id`, `promotion_name`, `creative_name`, `creative_slot` and as value assign corresponding variables that you imported from JSON file
    * This change needs to be done only on ecommerce event tags, not necessary to do it on Promotion tags.

In case if everything is configured, you can proceed with testing and deploying the solution.

## FAQ

**Q: Which List information will be collected?**\
**A:** If you have Universal Analytics Data Layer, it will collect List name (`list` parameter) and Item Position in the list (`position` parameter).\
If you have GA4 Data Layer, it will collect List Name (`item_list_name`), List ID (`item_list_id`) and Item position (`index`).

**Q: Which Promotion information will be collected?**\
**A:** If you have Universal Analytics Data Layer, it will collect Promotion `id`, `name`, `creative` and `position` parameters.\
In GA4 Data Layer, it will collect `promotion_id`, `promotion_name`, `creative_name` and `creative_slot` parameters.

**Q: What if I do not have one of the collected parameters in Data Layer (e.g. I don't have `position` parameter for my products)?**\
**A:** Missing parameter simply won't be collected and information won't be sent to GA4.

**Q: I have product-level custom dimensions/metrics. Will they still be sent to GA4?**\
**A:** Yes, any product-level custom dimensions/metrics will still be sent to GA4.

**Q: Does List information needs to be available inside Items array or can it be also provided inside ecommerce object (for UA, inside ActionField object)?**\
**A:** Solution is developed to work in both cases. First, it will check inside Items/Products array. If List information is not availble, it will check inside ecommerce object (for UA actionField object).

**Q: Why I need to have Item ID on every ecommerce event that is fired on my website?**\
**A:** Item ID is required on every ecommerce event (except promotions events) because this is the key how List information is stored in the cookie and taken from the cookie. If you don't have Item ID on one of your ecommerce events (e.g. item ID missing on `checkout` event), then List information won't be retrieved from cookie and sent to GA4.

## Feedback

If you have feedback or notice bugs, please feel free to raise an issue in GitHub or post in the [group](https://groups.google.com/g/ga4-ecom-attributor). Please note that this is not an officially supported Google product, so support is not guaranteed.
