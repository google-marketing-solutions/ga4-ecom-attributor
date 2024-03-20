# GA4 Ecom Attributor for sGTM

This is not an officially supported Google product.

This repository contains a JSON file that can be imported in server-side Google Tag Manager container to enable Item List and Promotion attribution in GA4. This does not modify any existing tags, triggers, or variables in the given Tag Manager container. 
JSON file contains tags and custom templates that needs to be imported and configured in existing workspace.


**This solutions works only:**
* In server-side GTM container (solution needs to be configured in server container)
* If you have ecommerce/promotion measurement implemented on website and data is sent to sGTM endpoint
* If you use Google Cloud Platform (solution uses Firestore as a storage place for List and Promotion data)
* If you are planning to use this solution for Item List attribution:
	*  Item ID needs to be available on every ecommerce event


# How to set up the solution

Follow these steps to properly set up the solution:
* Join [this group](https://groups.google.com/g/ga4-ecom-attributor) to get access to JSON file (any potential updates will be published in the group)
* [Download the JSON file](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/ecom-attributor-sGTM.json)
* Follow implementation guide (explained below) or download [PDF file with step by step instructions](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/ecom-attributor-sGTM-implementation-guide.pdf)




## Create Firestore Database

Go to Google Cloud Platform, to the Firestore page. If you already have database created, make sure you have Database with name **(default)**, in **Native** mode.

If you don't have, follow this steps:
* click on Create database button
* In first step, Select Native mode
* In second step, keep Database ID as it is: **(default)**
* For location type, select what you prefer
* Once you are finished, click on Create database button

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/firestore-database-created.png" width="670" height="487"></p>


## Create Time-to-live (TTL) policy

In Firestore, click on **(default)** database, and then on left-side navigation, click on Time-to-live (TTL). 
Click on Create Policy.
As Collection group enter: **ecommerce_attributor**\
As Timestamp field, enter: **expiration**

Click on Save button. It can take some time until policy is created.

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/ttl-policy.png" width="775" height="927"></p>



## Provide sGTM access to Firestore project

**In case if your sGTM container and Firestore database are located in the same Google Cloud Platform project, you can skip this step.**

**This step is necessary only for users who have two separate GCP projects for sGTM container and for Firestore database.**


Navigate to your GCP project where you have server-side GTM container deployed. Go to **IAM & Admin** and in left-side navigation, click on **Service Accounts**. Copy your service account email address.

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/copy-service-account.png" width="889" height="373"></p>

Now, navigate to your GCP project which you will use to store data in Firestore (GCP project where you created Database and TTL policy in Firestore in previous step). Go to **IAM & Admin** and in left-side navigation,  click on IAM. Click on **Grant Access**.
Paste your service account in **New Principals** field. As a Role, select **Cloud Datastore User**. Click Save button.

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/grant-access-service-account.png" width="617" height="587"></p>


We have configured all necessary setup in Google Cloud Platform. Now we can go to server-side GTM container, import and configure the solution.



## Import JSON file in sGTM container

Before you import JSON file with solution, make sure you have in your sGTM container:
 * Built-in Variable **Event Name** enabled  
 * GA4 event tag that forwards data to GA4

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/required-tag-variable.png" width="731" height="576"></p>

If you already have that:
* download [JSON file](https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/ecom-attributor-sGTM.json)
* Go to Google Tag Manager  
* Open your server-side GTM container where you want to implement solution  
* Click Admin → Import Container
* Select downloaded JSON file to import
* Select in which workspace you want to import the solution
* Select Merge and then Rename conflicting tags, triggers and variables
* Click on Confirm button

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/import-JSON-file.png" width="766" height="773"></p>

Now we can configure the solution.


## Configure Constant - GCP Project ID variable

Open variable called **Constant - GCP Project ID**

Delete everything from the input field, and provide **your Google Cloud Project ID** and save the changes.

**NOTE:** This should be your GCP Project ID where you configured Firestore database in previous steps.

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/gcp-project-id-variable.png" width="876" height="459"></p>



## Configure Ecom Attributor - Write to Firestore tag

Open tag called **Ecom Attributor - Write to Firestore**. In the tag, you don’t have to configure any GCP data or Identifier variable (this is optional, it will be explained in later steps). 

You need to configure setting in following sections:
  
### Scroll down to the section "Which information do you want to collect and store in Firestore" 
  
Mark checkboxes for which Attribution type you want to use this solution:
* Item List Attribution
* Promotion Attribution

### Next, go to section "Attribution time" 
  
Configure how long you want to keep data in Firestore. By default it is 7 days. This means if user interacts with Item inside the List or with Promotion, List and Promotion data will be kept in Firestore for 7 days after user’s last ecommerce interaction. In case if user inside the 7 days window makes a purchase of item which he had interaction with, List and Promotion data from Firestore will be sent to GA4 and Revenue data will be properly attributed.

**NOTE:** Decreasing the attribution time (e.g. to only 1 day) can increase Firestore  costs due to more frequent TTL policy deletes.
  
### Next, go to section "Delete List and Promotion data on purchase event"

What this option will do? When user makes a purchase, if user purchased itemA and itemB, and if there is Promotion and List data stored in Firestore for these two items, this information will be deleted from Firestore. It is recommended to enable this option.

**NOTE:** In case if user has List data available for items which he didn’t purchase currently, this information will stay in Firestore.



After you configure all settings, this is how Ecom Attributor - Write to Firestore tag should look like.  
Depending on which settings you selected, your setup will be of course slightly different compared to this example.


<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/ecom-attributor-tag-setup.png" width="652" height="805"></p>



## Add triggers to Ecom Attributor - Write to Firestore tag


Next, we need to provide triggers for Ecom Attributor - Write to Firestore tag.

### Add trigger(s) if you selected "Item List Attribution" option
If you selected option **Item List Attribution**, you need to know on which event you have List information available in Event Data model. On that specific event, tag needs to fire in order to collect and store List information in Firestore. 
Solution supports collection of List data on following events:
* `select_item`
* `view_item`
* `add_to_cart`
    
NOTE:
* If you have cases on website where List information is available on all three mentioned ecommerce events, solution supports this as well. Just add all three events as a trigger to the tag.
* If List data is available outside of items array, solution supports this as well and it will collect List information.
* If Promotion data is available with Item, this will be collected and stored in Firestore as well.


### Add trigger if you selected "Promotion Attribution" option

If you selected Promotion Attribution option, you have to provide **select_promotion** trigger to the tag.

NOTE:
* Solution supports collection of Promotion data on select_promotion event only.
* Solution will collect Promotion data if it is available inside items array, but it also supports case if you are sending it outside of array.
* In case if you only have Promotion data on `select_promotion` event (no item data), sollution supports this as well. Promotion data will be collected, and sent on event-level with any subsequent ecommerce events. On Purchase event, whole Item revenue data will be attributed to the last clicked Promotion

### Add trigger if you selected "Remove List and Promo data for purchased items" option

If you selected option **Remove List and Promo data for purchased items**, provide **purchase** event as a trigger to the tag.



### Summary
Depending on which setting you selected, you should have:
* If you selected **Item List Attribution**, you should have at least 1 trigger (`select_item`, `view_cart` or `add_to_cart`)  
* In case if you selected **Promotion Attribution**, you should have `select_promotion` event as trigger
* If you selected to **Remove List and Promo data after purchase**, you should have `purchase` event as trigger

**IMPORTANT:** If you have advanced Consent Mode implemented, it is worth to note that this solution won’t work for unconsented pings. We recommend to not trigger Write to Firestore tag on unconsented pings.


<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/ecom-attributor-tag-triggers.png" width="600" height="750"></p>



## Optional #1: Configure "Identifier - hashed" variable

**This step is optional.** 
  
By default, solution is using hashed `client_id` to store data in Firestore (SHA-256, encoded in hex).

In case if you want to use some other device/user identifier,  open variable called **Identifier - hashed**.  
By default, as an input client_id variable is provided. Remove it, and provide variable with identifier you would like to use.

**IMPORTANT:** if you want to use some other device/user identifier (e.g. user_id), this identifier has to be available on every ecommerce event, otherwise solution won’t work.


<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/identifier-hashed-variable.png" width="970" height="669"></p>



## Optional #2: Remove Promotion Transformation & Variables

**This step is optional and should only be done if you don’t have Promotions measurement implemented on your website.**

In case if you don’t have promotion measurement implemented on your website and you are not storing any Promotion information in Firestore, you can delete following things from your sGTM container:
* `Ecom Attributor - Augment - Promotion data`
* `FS Lookup - promotion_name`
* `FS Lookup - promotion_id`
* `FS Lookup - creative_slot`
* `FS Lookup - creative_name`

<p align="center"><img src="https://github.com/google/ga4-ecom-attributor/blob/main/ecom-attributor-sGTM/images/promotion-data.png" width="758" height="641"></p>



# Final checklist

Let's quickly recap what settings you needed to configure:
1. **Create Firestore database**\
In case if you don't have it already, create Firestore database named **(default)**, in Native mode

2. **Create Firestore TTL policy**\
Create TTL policy for **expiration** field, for Collection called **ecommerce_attributor**

4. **Provide sGTM access to the Firestore project**\
This is necessary only for users who have their sGTM container and Firestore database in separate GCP projects

5. **Import JSON file in sGTM container**
6. **Configure Constant - GCP Project ID variable**\
Provide your GCP Project ID inside variable
8. **Configure settings in Ecom Attributor - Write to Firestore tag**\
Configure settings which information you want to collect, how long you want to keep data in Firestore and if you want to delete data after purchase event
10. **Add triggers to the Ecom Attributor - Write to Firestore tag**


In case if everything is configured, you can proceed with testing and deploying the solution.


## FAQ

**Q: Does solution support custom ecommerce implementation?**\
**A:** Solution supports all types of ecommerce measurement  implementation (gtag.js, web GTM, third party tag management system, custom implementation). Solution will works as long as ecommerce data received in sGTM respects GA4 ecommerce event data model (event and parameters naming).


**Q: Will this solution produce additional GCP cost?**\
**A:** It depends on how many ecommerce events and users your website have. Additional costs could occur due to Firestore write and TTL policy deletes.


**Q: Does solution work with advanced Consent Mode?**\
**A:** Solution doesn’t work with unconsented pings. List and Promotion data is stored in Firestore based on hashed `client_id`, therefore it won’t be possible to stitch List and Promotion data from unconsented pings with events after user gave the consent.


**Q: Which information is stored in Firestore if I select option “Item List Attribution”?**\
**A:** Following parameters will be stored in Firestore: `item_list_id`, `item_list_name`, `index`, `location_id`. In case if you have promotion data inside the items array, following parameters will also be collected: `promotion_id`, `promotion_name`, `creative_name`, `creative_slot`. If certain parameters are not set, it simply won’t be collected and stored in Firestore.


**Q: Which information is stored in Firestore if I select option “Promotion Attribution”?**\
**A:** Following parameters will be stored in Firestore: `promotion_id`, `promotion_name`, `creative_name`, `creative_slot`. If certain parameters are not set, it simply won’t be collected and stored in Firestore. In case if there is item list information sent with `select_promotion` event, this information will be collected as well.


**Q: Will List/Promotion information be collected if it is not located inside items array, if it is actually located inside ecommerce object?**\
**A:** Yes, solution supports this, information will still be collected. Following logic is applied: solution will always first look into the items array and pick if any information is available. In case if certain parameter is not available inside items array (e.g. item_list_name), solution will check inside ecommerce object and pick information if it is available.


**Q: Why I need to have Item ID on every ecommerce event that is fired on my website?**\
**A:** Item ID is required on every ecommerce event (except promotions events) because this is the key how List information is stored in Firestore for every item. If you don't have Item ID on one of your ecommerce events (e.g. item ID missing on begin_checkout event), then List information won't be retrieved from Firestore and sent to GA4.


**Q: On which events solution supports collection of List data?**\
**A:** Solution will support collection of List data (and Promotion data if case if it is available) on following events: `select_item`, `view_item` and `add_to_cart` event. Depending on which event you have List information, those events needs to be provided as triggers to the **Ecom Attributor - Write to Firestore** tag.


**Q: On which events solution supports collection of Promotion data?**\
**A:** Solution will support collection of Promotion data (and List data if case if it is available) only on `select_promotion` event. This event needs to be provided as trigger to the **Ecom Attributor - Write to Firestore** tag.


**Q: What if I have only Promotion data on select_promotion event (no item data)?**\
**A:** Solution supports this type of implementation as well. It will collect only Promotion data on `select_promotion` event, and Promotion data will be sent on event-level with any subsequent ecommerce events, which means on purchase event, whole Items revenue will be attributed to the last clicked Promotion.



## Feedback

If you have feedback or notice bugs, please feel free to raise an issue in GitHub or post in the [group](https://groups.google.com/g/ga4-ecom-attributor). Please note that this is not an officially supported Google product, so support is not guaranteed.
