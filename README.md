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
* **Promotion attribution** - user clicks on Promotion, it takes Promotion data, stores them in the cookie called **ga4_promo**. It will store only last clicked promotion data in the cookie. If user makes a purchase, promotion data will be sent to GA4. Data is sent on a purchase level, which means Order revenue data will be attributed to the last clicked Promotion.

* **Product List attribution** - user clicks on product, it takes List data, stores them in the cookie called **ga4_list_{{item ID}}**. For every item user clicks, creates new cookie. As user progresses thourgh ecommerce funnel (adds product to the cart, goes to checkout, purchases product), List information will be sent to GA4. When user purchases the product, Product Revenue will be attributed to the appropriate List.

## How to set up the solution

Follow these steps to properly set up the solution:
* Join [this group](https://groups.google.com/g/ga4-ecom-attributor) to get access to JSON file
* Download the JSON file

Once you download the JSON file, you are ready to import it in web GTM container.

## Import JSON file

To import JSON file, go to [Google Tag Manager](https://tagmanager.google.com/#/home) and open your web container where you want to set up the solution. Navigate to Admin -> Import container:
* Under "Select file to import" select downloaded JSON file
* For workspace, select "Existing" and then select one of your existing workspaces where you want to import the tags
* For import option, select "Merge" -> "Rename conflicting tags, triggers, and variables"

Click on "Confirm" button once you are ready to import the file.

## Configure Cookie creator tag

After importing JSON file, go to Tags section and find tag called **[GA4] Cookie creator for Item list & Promotion attribution**

Cookie creator tag will be responsible for creating cookies and storing List or Promotion information in the cookie and deleting the cookies after the purchase.

In the tag, you need to configure following settings:

##### 1. Data Layer type
In dropdown, select which type of Data Layer you have on your website: [Google Analytics 4](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce?client_type=gtm) or [Universal Analytics](https://developers.google.com/analytics/devguides/collection/ua/gtm/enhanced-ecommerce)


##### 2. Which information you want to collect?

In this section, you need to select which information you want to collect:

* Promotion attribution
  * By selecting this option, you need to provide exact event name you use to push Promotion Click information into Data Layer. For example, if you push Promotion Click data to Data Layer with event called "promotionClick", this is what you need to provide in the input field
  * Same event name that you provide in this input field needs to be added as a trigger for Cookie creator tag

* Product List attribution
  * By selecting this option, you need to provide exact event name you use to push Item List information into Data Layer. For example, if you push List information to Data Layer with event called "productClick", this is what you need to provide in the input field
  * In case if you selected Universal Analytics as Data Layer type, you will have to select one of the radio button options to provide explanation on which ecommerce action you are pushing List information to Data Layer (Product List Click, Detail View or Add to Cart)
  * Same event name that you provide in this input field needs to be added as a trigger for Cookie creator tag

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
* If you selected to track Promotion attribution, and in input field you specified event name "promotionClick", this event needs to be added as trigger as well
* If you also selected to track Product list attribution, and in input field you specified event name "productClick", this event also needs to be added as trigger
* Event you specified as a Purchase event needs to be added as trigger

So, depending on your tag configuration, in total you should have 2 or 3 triggers on Cookie creator tag.

#### How tag works and what is it's purpose?
Based on options you selected, tag will do the following:
* If you selected to collect Promotion attribution - When user clicks on Promotion, Cookie creator tag will fire (Promotion trigger you added to Cookie creator tag). It will check Data Layer, take Promotion data and store it in the cookie called "ga4_promo". If you click on multiple promotions, data is collected only for last clicked promotion.

* If you selected to collect Product List data - when user clicks on product which contains List information, Cookie creator tag will fire (trigger you added to Cookie creator tag). It will check Data Layer, take List information and store it in the cookies called "ga4_list_{{item ID}}". For every product, new cookie will be created. In case if purchased product had "ga4_list_{{item ID}}" cookie, cookie will be deleted on a purchased event. 

* On purchase event, it will delete "ga4_promo" cookie if it exists in the browser. It will check all purchased products, and if any of purchased products had "ga4_list_{{item ID}}" cookie in the browser, it will delete the cookie. 

## Configure [GA4] Promotion events tag

NOTE: If you don’t have Promotion measurement implemented on your website, you can delete this tag.

Open **[GA4] Promotion events** tag you imported. Provide triggers to fire this tag on Promotion Impression (`view_promotion`) and Promotion Click (`select_promotion`).

This GA4 tag will be used to send promotion events to GA4, in case if you have Promotion measurement implemented on your website.

NOTE: In case if you already have implemented GA4 tags for sending promotion events to GA4, you can either pause them to avoid measuring promotion events twice or you can keep your tags and delete this tag.

## Configure [GA4] Ecommerce events tag

Next, find and open tag called **[GA4] Ecommerce events**. 

In case if you already have implemented GA4 tags for sending one of the mentioned ecommerce events, you can pause them to avoid measuring ecommerce events twice.

This tag can send following events to GA4:
*  `view_item_list`
*  `select_item`
*  `view_item`
*  `add_to_cart`
*  `remove_from_cart`
*  `add_to_wishlist`
*  `view_cart`
*  `begin_checkout`
*  `add_payment_info`
*  `add_shipping_info`
*  `purchase`

##### 1. Configuration 
First, provide one of the following things:
* Select your Configuration tag in dropdown menu or
* Put manually your GA4 Measurement ID

##### 2. Configure event parameters

If some parameters are not necessary, feel free to remove them.
For example, if you do not collect `affiliation` and `coupon` on a purchase event, you can remove these two parameters.

If you have additional event parameters or user properties you want to send with GA4 ecommerce events, you need to add them manually.

NOTE: Last 4 parameters (`promotion_id`, `promotion_name`, `creative_name`, `creative_slot`) are only needed if you set up to measure Promotion attribution in Cookie Creator tag and if you have Promotion measurement implemented on website. So, if you didn't select in Cookie creator tag to track Promotion attribution, feel free to remove these parameters.

##### 3. Tag firing priority
Please don't remove Tag firing priority setting. This tag needs to have higher firing priority compared to the Cookie creator tag to ensure data is successfully sent to GA4 before cookies are deleted.

##### 4. Set up triggers

Last step is to add triggers on Ecommerce tag. For triggers, put all you ecommerce events you measure on your website.

## Configure variable [Lookup] GA4 ecommerce event names

Find and open variable called **[Lookup] GA4 ecommerce event names**.

In this variable, you should map your Data Layer events with [recommended GA4 ecommerce events](https://support.google.com/analytics/answer/9267735?hl=en#:~:text=completes%20a%20tutorial-,For%20online%20sales,-We%20recommend%20these).

In case if you do not measure some of the ecommerce interactions (e.g. `add_to_wishlist` or promotion events), remove the value from lookup table.

NOTE: In case if you have GA4 Data Layer type implemented on website with recommended ecommerce event names, this variable is not required, it will take event name directly from Data Layer.

## Final checklist

If you followed instructions above, you should have solution ready. Let's quickly recap what settings you needed to configure:
1. **Cookie creator tag**
    *  In dropdown, you specified which Data Layer you use on your website
    *  If you selected Promotion attribution option, you provided exact event name you use to push Promotion Click information in Data Layer. Same event should be added as trigger to Cookie creator tag.
    *  If you selected Product List attirbution option, you provided exact event name you use to push List information in Data Layer. Same event should be added as trigger to Cookie creator tag.
    *  You provided Purchase event. Same event should be added as trigger to Cookie creator tag.
    *  Triggers for Cookie creator tag are added based on previous options you selected.

2. **[GA4] Promotion events tag**
    *  Triggers are added - add triggers for Promotion Impression and Promotion Click events

3. **[GA4] Ecommerce events tag**
    * You provided Configuration tag or added manually your Measurement ID
    * Any unnecessary event parameters are removed
    * Triggers are addded - any ecommerce events you track on your website should be added as trigger

4. **Variable [Lookup] GA4 ecommerce event names**
    *  Your event names you use on website are mapped to recommended GA4 ecommerce events 

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
