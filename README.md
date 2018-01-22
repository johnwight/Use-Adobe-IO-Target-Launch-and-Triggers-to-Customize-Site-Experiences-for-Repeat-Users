# Use Adobe I/O, Target, Launch, and Triggers to Customize Site Experiences for Repeat Users

These instructions describe how to use Adobe I/O Events and Runtime with Launch, Target, and Triggers to customize website experiences for anonymous users who have previously abandoned their carts.

1. [Introduction](#Introduction)

1. [Set Up Products](#Set-Up-Products)

1. [Watch the Solution Work](#Watch-It-Work)

## <a name="Introduction">Introduction</a>

### Solution Use Case

This solution applies to the following use case:

An anonymous user visits a retail website. The user visits the product page, selects a product color and size and then adds the product to the cart. The user proceeds to checkout, but before completing the transaction, leaves the website and abandons the cart. The next time the user visits the website, the user experiences a custom-tailored view based on past interaction with the website.

### Architecture
The following diagram shows the architecture for this solution:

![runtime flow](https://user-images.githubusercontent.com/29133525/35231736-c32ba766-ff56-11e7-8383-20183fed9364.png)

### What's Needed

To complete this solution, you will need authorization and administrative privileges to use the following services:

*   Adobe Launch
*   Adobe Target
*   Adobe Analytics, including Triggers
*   Adobe Marketing Cloud Activation Core Services

## <a name="Set-Up-Products">Set Up Products</a>

To set up Adobe products for this solution:

1. [Set Up Launch](#Set-Up-Launch)

1. [Set Up Analytics Triggers](#Set-Up-Analytics-Triggers)

1. [Set Up Adobe I/O Runtime](#Adobe-I/O-Runtime)

1. [Set Up Adobe I/O Events](#Adobe-I/O-Events)

1. [Set Up Target](#Set-Up-Target)

### <a name="Set-Up-Launch">Set Up Launch</a>

Adobe Launch is the next-generation Dynamic Tag Management (DTM) tool. It provides a platform-based approach to building DTM extensions and a streamlined distribution system to quickly deploy client-side DTM libraries. With Launch, you can create custom resources and re-use them within DTM to simplify the distribution of client-side web applications.

To set up Launch:

1. On www.launch.adobe.com, click **New Property**.

     ![create new property](https://user-images.githubusercontent.com/29133525/35232042-a62c732e-ff57-11e7-9210-1205d6d9e46c.png)

1. On the **Create Property** box, provide the details for the new property and click **Save.**

     ![create property box details](https://user-images.githubusercontent.com/29133525/35232087-c8a33c3a-ff57-11e7-82ff-8b68c085726a.png)

1. Click the **Extensions** tab and install the following extensions

   * Target
   * Analytics
   * ContextHub
   * Core
   * Experience Cloud ID Service
   
     ![extensions](https://user-images.githubusercontent.com/29133525/35232128-ee66002e-ff57-11e7-9958-5aa36d633d26.png)

1. Click the **Rules** tab and create a **Target** rule with the following specifications:

     ![edit rule](https://user-images.githubusercontent.com/29133525/35232202-1dbb2cb4-ff58-11e7-8d9d-398024a3ae43.png)

1. Similarly, create an **Analytics** rule with the following specifications:

     ![analytics rule](https://user-images.githubusercontent.com/29133525/35232240-39d0808e-ff58-11e7-9ac1-92d763c3cd21.png)

1. For the **Adobe Analytics - Set Variables** action, click the **</> Open Editor** button at the bottom of the page and add the following custom script:

   ```
   var cart=ContextHub.getItem("cart");
   var profile=ContextHub.getItem("profile");

   var price=[];
   var items=[];
   var links=[];
   var thumbs=[];
   var origin=window.location.origin;
   for(i=0;i<cart.entries.length;i++)
   {
        price[i]=cart.entries[i].price;
        items[i]=cart.entries[i].title;
        links[i]=origin+cart.entries[i].page;
        thumbs[i]=origin+cart.entries[i].thumbnail;
   }

   s.eVar3=document.cookie.split("PC#")[1].split("#")[0];
   s.eVar4=price.join("|");
   s.eVar5=items.join("|");
   s.eVar6=links.join("|");
   s.eVar7=thumbs.join("|");

   ```
7. Click the **Environments** tab and create the following environments:

   * Dev
   * Stage
   * Production

     ![create environments](https://user-images.githubusercontent.com/29133525/35232311-65b511b0-ff58-11e7-8abf-b8672c63dbfe.png)

8. Save the rule and then click the **Publishing** tab.

9. Click **Add New Library** button.

     ![add new library](https://user-images.githubusercontent.com/29133525/35232426-a6d1364c-ff58-11e7-88e7-88d02cf29c19.png)
   
10. On the **Create New Library** form, specify a **Name** for the build and then select **Dev (development)** from the **Environment** drop down.

11. Click the **Add All Changed Resources** button. 
   
     ![specify build](https://user-images.githubusercontent.com/29133525/35232486-d2a1fdc4-ff58-11e7-930d-61983c6cc83f.png)
   
12. Under **Development**, select **Build for Development** in the library drop down.
   
     ![build for dev](https://user-images.githubusercontent.com/29133525/35232529-ecd5061e-ff58-11e7-9e33-78a2a336a73a.png)

13. Approve and publish the library by selecting the appropriate option under the drop down arrow for each phase of the build workflow (**Submitted** and **Approved**).

     ![full flow](https://user-images.githubusercontent.com/29133525/35232581-09ba310a-ff59-11e7-8ecf-413d8a1b3b25.png)

14. Repeat this process for the **Stage** and **Production** environments as well.

15. The last step in the workflow is to select **Build and Publish to Production** on the drop down arrow under **Approved**.

     ![build and publish to production](https://user-images.githubusercontent.com/29133525/35232616-2b0ee6de-ff59-11e7-8405-3472c6f1876c.png)


### <a name="Set-Up-Analytics-Triggers">Set Up Analytics Triggers</a>

Triggers is a Marketing Cloud Activation core service that enables marketers to identify, define, and monitor key consumer behaviors, and then generate cross-solution communication to re-engage visitors. You can use triggers in real-time decisions and personalization.

For instructions on setting up Analytics Triggers, see [Specifying a New Trigger](https://github.com/adobeio/analytics-triggers-documentation#Specify-a-New-Trigger).

To set up Triggers for this solution:

1. Create a cart abandonment Triggers rule.

     ![cart abandonment rule](https://user-images.githubusercontent.com/29133525/35232707-671e5056-ff59-11e7-8309-db44bd0d296f.png)

1. Add the following dimensions:

     ![dimensions](https://user-images.githubusercontent.com/29133525/35232766-8e9bfec6-ff59-11e7-888c-d69adb140cf6.png)

### <a name="Set-Up-Target">Set Up Target</a>

Before setting up Target, you must first configure Adobe I/O services, as described in the [following section](#Use-Adobe-I/O). Once the Target profile API is set with Adobe I/O, you can [continue setting up Target](#Set-Up-Target-Continued).

### <a name="Adobe-I/O-Runtime">Set Up Adobe I/O Runtime</a>

Adobe I/O Runtime is a serverless platform that allows you to quickly deploy custom code to respond to events and execute functions in the cloud, with no server set-up.

In this solution, you can deploy the following script to handle Triggers I/O Events and to provide updates to the visitor profile in **Customer Attributes** via Target Profile APIs.

**webhook.js**
```

function main(args) {
    var method = args.__ow_method;
 
    if (method == "get") {
        var res = args.__ow_query.split("challenge=");
        var challenge = res[1];
        if (challenge)
            console.log("got challenge: " + challenge);
        else
            console.log("no challenge");
 
        return {
            body: challenge
        }
    }
 
    if (method == "post") {
        var body = new Buffer(args.__ow_body, 'base64');
        var jSon = JSON.parse(body);
        var mcId = jSon.trigger.mcId;
        var index = jSon.trigger.enrichments.analyticsHitSummary.dimensions.eVar5.data.length - 1;
        var pcId = jSon.trigger.enrichments.analyticsHitSummary.dimensions.eVar3.data[index];
        var trPrices = jSon.trigger.enrichments.analyticsHitSummary.dimensions.eVar4.data[index].split("|")[0];
        var trProducts = jSon.trigger.enrichments.analyticsHitSummary.dimensions.eVar5.data[index].split("|")[0];
        var trLink = jSon.trigger.enrichments.analyticsHitSummary.dimensions.eVar6.data[index].split("|")[0];
        var trThumb = jSon.trigger.enrichments.analyticsHitSummary.dimensions.eVar7.data[index].split("|")[0];
        var trOffer = "5";
        var trCoupon = "ADBETGT5OFF";
 
        //Additional 5% off, if product price is more than $100
        if (trPrices > 100) {
            trOffer = "10";
            trCoupon = "ADBETGT10OFF";
        }
 
        console.log("webhook invoked with: " + body);
 
        var fs = require('fs');
        var request = require('request');
        var file = pcId + ".txt";
        var content = "batch=pcId,mcId,trProducts,trPrices,trLink,trThumb,trOffer,trCoupon\n"
                       + pcId + "," + mcId + "," + trProducts + "," + trPrices + "," + trLink + "," + trThumb + "," + trOffer + "," + trCoupon;
 
        return new Promise(function(resolve, reject) {
            try {
                fs.writeFileSync(file, content);
            } catch (e) {
                console.log("Cannot write file ", e);
            }
 
            try {
                var data = fs.readFileSync(file);
                console.log("Sending:" + data);
            } catch (e) {
                console.log("Cannot read file ", e);
            }
 
            var options = {
                url: 'http://<your_client_id>.tt.omtrdc.net/m2/<your_client_id>/v2/profile/batchUpdate',
                headers: {
                    "content-type": "application/x-www-form-urlencoded",
                    "Authorization": "Bearer <target_authorization_code>"
                }
            }
            fs.createReadStream(file, {encoding: 'utf-8'}).pipe(request.post(options, function(err, message, res) {
                if (err) {
                    reject(err);
                } else {
                    resolve({body: res});
                }
            }));
 
        });
    }
}
```

To deploy the webhook.js and create a web action, enter the following commands:

```
wsk action create runtime webhook.js
```
```
wsk action update runtime --web raw
```

After entering the commands, the web action is accessible at the following location:
```
https://runtime-preview.adobe.io/api/v1/web/<your_openwhisk_namespace>/default/runtime
```

### <a name="Adobe-I/O-Events">Set Up Adobe I/O Events</a>

With Adobe I/O Events, you can code event-driven experiences, applications, and custom workflows that leverage and combine the Adobe Experience Cloud, Creative Cloud, and Document Cloud.

Use Adobe I/O by creating a new integration with the Console. To do this:

1. After signing in to the [Adobe I/O Console](https://adobe.io/console), click **New Integration**.

    ![new integration button](https://user-images.githubusercontent.com/29133525/30292388-2ccdd986-96f3-11e7-93bd-93f74bb4e3a4.png)

2. Select **Receive real-time events** and click **Continue**.

    ![real time events](https://user-images.githubusercontent.com/29133525/30292486-946dc812-96f3-11e7-9bc5-2aa0196f704b.png)


3. Select **Analytics Triggers** as an event provider and click **Continue**.

    ![create io trigger](https://user-images.githubusercontent.com/29133525/30292595-064305ec-96f4-11e7-9e60-3ee811c7949a.png)

4. Click **Continue** to move on to the next page without making any changes.

5. Provide the **Name** and **Description** for your integration.

    ![name integration box](https://user-images.githubusercontent.com/29133525/30292714-6d00a8ac-96f4-11e7-8449-f93bb2fccfcb.png)

6. Generate a public certificate. To do this:

    1. Open a terminal and execute the following command:

        ```
        openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout private.key -out certificate_pub.crt
        ```
    2. Upload the public certificate by clicking the **Select a File** link and then by selecting the certificate from your computer:

        ![public certificate](https://user-images.githubusercontent.com/29133525/32476036-2e563e40-c332-11e7-9fbd-889f1ba8054f.png)

7. On the **Webhook Details** form, add details, including your web action URL for I/O Events integration webhook. This is where Triggers messages will be delivered by I/O Events. Click **Save**.

     ![webhook details](https://user-images.githubusercontent.com/29133525/35233287-ea8b5dca-ff5a-11e7-8a10-186e632ed391.png)

    For more information on creating and registering webhooks, see [Introduction to Webhooks](https://github.com/adobeio/adobeio-events-documentation/blob/master/Webhook_docs_intro.md).


### <a name="Set-Up-Target-Continued">Set Up Target</a>

Adobe Target is a personalization solution that makes it easy to identify your best content through tests that are easy to execute. So you can deliver the right experience to the right customer.

To set up Target:

1. Click **Launch** on the Target product card.

     ![target card launch](https://user-images.githubusercontent.com/29133525/35233319-0bfb3ec6-ff5b-11e7-85ef-db00b66c8c85.png)

1. On the **Audiences** tab, click the **Create Audience** button.

     ![audiences tab redo](https://user-images.githubusercontent.com/29133525/35234347-0ebad54c-ff5e-11e7-94fd-60acb63db18b.png)


1. On the **Information** form, provide a **Name** for the audience. Then click the **Add Rule** button and select **Visitor Profile**.

     ![information form visitor profile](https://user-images.githubusercontent.com/29133525/35234400-3aa423de-ff5e-11e7-8838-afc6985d52cf.png)


1. Click the **Visitor Profile** drop down arrow to see existing custom attributes for creating audiences.

     ![custom attribute](https://user-images.githubusercontent.com/29133525/35234493-8fbb50e0-ff5e-11e7-940d-197171950a4b.png)

1. Create a rule to check if the user has abandoned a cart during earlier visits and then click **Save**.

     ![create rule cart](https://user-images.githubusercontent.com/29133525/35234543-c4df1d4c-ff5e-11e7-994a-ed2e4dbc81ab.png)

1. Click the **Activities** tab and select **Experience Targeting**.

     ![activities tab](https://user-images.githubusercontent.com/29133525/35234602-fb9924fe-ff5e-11e7-8ca9-e127b4108b0b.png)
     
1. On the **Create Experience Targeting Activity** form, provide the Target page URL.

     ![targeting activity](https://user-images.githubusercontent.com/29133525/35234641-1cf82258-ff5f-11e7-8ebc-c8d6f5b18205.png)

1. Open the text editor to add a custom UI element for the user on the site. To do this, select an empty container and click **Edit Text/HTML**.

     ![container](https://user-images.githubusercontent.com/29133525/35234700-3d586ac6-ff5f-11e7-846d-d0a70ba2151b.png)

1. On the text editor, click the **HTML** button, paste the following HTML and click **Save**. 

   ```
   <div style="border: 2px solid grey">
       <strong>
           Hi! It seems you were trying to buy &quot;${profile.trProducts}&quot;<a href="${profile.trLink}" target="_blank"><img src="${profile.trThumb}" /></a>, Here is an exclusive ${profile.trOffer}% discount coupon for you: ${profile.trCoupon}
       </strong>
   </div>
   ```
     ![text editor](https://user-images.githubusercontent.com/29133525/35234773-810ffaf4-ff5f-11e7-9bfd-3badafb445eb.png)

10. Click **Next** and then click **Change Audience**. Select the audience created in Step 4 (Trigger Audience).

     ![change audience](https://user-images.githubusercontent.com/29133525/35234826-addd2fac-ff5f-11e7-9e65-0fa0aff1992d.png)


11. On the **Activity Settings**, specify your **Objective**, **Priority**, **Duration**, and **Primary Goal**.

     ![activity settings](https://user-images.githubusercontent.com/29133525/35234872-cee9deb6-ff5f-11e7-9786-2412edfde98e.png)

12. Click **Save & Close**.
 
## <a name="Watch-It-Work">Watch the Solution Work</a> 

To watch the solution work, you can test the cart abandonment scenario:

1. At http://localhost:4502/content/we-retail/us/en/products/men.html, click on a product you want to test.

1. Select a **Color** and **Size**, then click the **ADD TO CART** button and **Checkout**.

1. Enter details on the **ORDER** form and then click **CONTINUE**.

     ![checkout form](https://user-images.githubusercontent.com/29133525/35234926-f44a35ac-ff5f-11e7-84b7-08bb9c96042d.png)

1. Close the browser tab to simulate the cart abandonment scenario.

1. On the Triggers UI page, watch for your specified trigger event to appear.

     ![triggers page](https://user-images.githubusercontent.com/29133525/35234984-17044344-ff60-11e7-8116-8af484324e89.png)

1. On the Command Line Interface (CLI) enter the following commands:

   ```
   wsk activation list runtime
   ```

     ![runtime commands](https://user-images.githubusercontent.com/29133525/35235023-3a2ac712-ff60-11e7-9f8b-014cca316a4f.png)


7. Select and copy the first activation listed and paste it into the following command:

   ```
   wsk activation get f6f5ae1dcb3d4292991d63f22283fb94
   ```

8. Verify that the API call to update the visitor profile has successfully executed. You can check the batch status by using the URL shown in the response > result > \<batchStatus\> tag: 

   http://mboxedge28.tt.omtrdc.net/m2/adobeiosolutions/profile/batchStatus?batchId=adobeiosolutions-1508968014477-39630647.

     ![trigger response](https://user-images.githubusercontent.com/29133525/35235092-5eecb9ca-ff60-11e7-8291-f8118d03601f.png)


9. View the [**Products** site page](http://localhost:4502/content/we-retail/us/en/products/men.html) to see the custom UI element you created.

     ![response custom ui element on page](https://user-images.githubusercontent.com/29133525/35235241-cc3971da-ff60-11e7-8caf-9bc3fbe4d9db.png)

10. To inspect the element, right-click on the page and select **Inspect**.

     ![inspect element](https://user-images.githubusercontent.com/29133525/35235281-ecedd5d8-ff60-11e7-9b21-e9a35a94fd6f.png)


11. Click the **Network** tab. Reload the page and filter for **target**.

     ![network tab on debugger](https://user-images.githubusercontent.com/29133525/35235317-0ea83696-ff61-11e7-9a1c-b25fad9fe806.png)


12. You can explore the element to verify and debug the target action.

     ![debug target action](https://user-images.githubusercontent.com/29133525/35235363-277f06ae-ff61-11e7-931d-6baf2d549a9c.png)

## Feedback?

Please help make this solution as useful as possible. If you find a problem in the documentation or have a suggestion, click the **Issues** tab on this GiHhub repository and then click the **New issue** button. Provide a title and description for your comment and then click the **Submit new issue** button.

   ![submit new issue](https://user-images.githubusercontent.com/29133525/32515298-f344bd5a-c3bc-11e7-9978-34516f964f9f.png)

