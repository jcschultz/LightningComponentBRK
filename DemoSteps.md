# Lightning Components BRK

 Contact: **Greg Rewis, Principal Developer Evangelist, [grewis@salesforce.com](mailto:grewis@salesforce.com), +1-602-451-4858**


## **Change Log**

6/7/2017	Greg Rewis	[grewis@salesforce.com](mailto:grewis@salesforce.com)	Notes

## Objectives

* Introduce Lightning Components
* Understand <lightning> namespaced components
* Create a simple Lightning Component

## Preparation

* Create a Summer '17 Developer org
* Enable and deploy **My Domain** to all users
* Install the LightningComponentsBRK package from: [https://login.salesforce.com/packaging/installPackage.apexp?p0=04t6A0000016uhv](https://th-project-dev-ed.my.salesforce.com/one/one.app#/setup/alohaRedirecthttps://login.salesforce.com/packaging/installPackage.apexp?p0=04t6A0000016sUm&isdtp=p1)
* Once installed, open the Dreamhouse Lightning app and click on the **Data Import** tab.
    * Click the Initialize Sample Data button
* Open the following in another browser tab to allow you to copy/paste code: https://github.com/garazi/LightningComponentBRK

## Devices

None


## Monitor Setup

 Nothing of note


## Demo Script

 Actions and scripts for the Demo. 


* Recording TBD
* At the completion of each demo:
    * In App Builder, remove the BrokerProperties that you create from the page.
    * In the Developer Console, open the BrokerProperties component and delete it.

### Talk track

| Do   |      Say      |
|----------|-------------|
|Click on the Properties tab and choose a property.	| The Dreamhouse app is an app for a ficticious real estate company to help them manage their inventory of houses, brokers, prospects, etc. The default Property Record page has been modified with several Lightning Components using Lightning App Builder.
|Point out the Lightning Components in the righthand column.	| Each of the items in the righthand column is a Lightning Component. In other words, they are each a piece of standalone functionality. But just because the individual components are independant of one another on the page, they can still communicate with each other, as well as pull data from the record itself.|
|Point to the **Days on the Market** component at the top of the column.	|For example, this component is calculating the number of days on the market using the **Date Listed** field in the record.
|Point to the **Property Map** component.	| The Property Map is picking up the address from the record and using it to place a pin on the map in the proper location.
|Point to the **Similar Properties** component.	| In addition to grabbing fields from a record when the page loads, Lightning Components can also "listen" for events to occur. For example, this component has found similar properties based upon the current price of the record. In this case, it is looking for properties that are plus or minus $100000.
|Edit the **Price** of the main record, lowering it by $75000-100000. Save the change.	| Notice if we lower the price of this property and save, the component updates with a new selection of similar properties.
|Click **Setup > Edit Page**.	| As a developer, we can expose parameters of our Lightning Components so that an Admin can modify the behavior of the component on the page.
|Drag another copy of the **Similar Properties** component onto the page below the current instance of the component. Highlight the parameters in the righthand column.	| When a component contains parameters, we can see them here in App Builder.
|Change the Search Criteria to **Bedrooms**. |	For example, we can ask for similar properties by the number of bedrooms.
|Click **Save** and then click the **Back** link.	| So now we have a single component performing two different tasks.
|Click the Edit (pencil) icon for one of the properties in the Similar Properties component that is searching by **price**. Choose a different **Status** and click **Save.**	 |Using the magic of something called Lightning Data Services, this component can even update a record that we aren't even looking at. Lightning Data Service is also what allowed the component to react to the price change. 
|Clicking this Edit icon is pulling the actual data from the record. | Let's change the **Status** on this component. And we can see it has, in fact, updated back in the component. And, by the way, there was no Apex Class involved in updating the record. Instead, again, Lightning Data Service has updated the record.
|Point to the **Neighborhood Explorer** component.	| Lightning Components are not limited to talking to Salesforce. In the Neighborhood Explorer component, we are again capturing the address for the Property Record like we did in the Property Map component. But this time, we are passing it off to a 3rd party app running on Heroku which is proxying the Yelp! API to gather information about schools, shopping and restaurants around this property.
|Click on the **Mortgage Calculator** in the Utility Bar. | Lightning Components can be used almost everywhere in the Lightning Experience. In this case, we're using a Lightning Component in the Utility Bar so that it's available throughout the Dreamhouse app. Lightning Components can also be used as Quick Actions, and new in Summer '17, they can be used as Standard Button Overrides.
|Click on the Brokers tab and choose a broker.	| Now that we've looked at several components in action, let's talk about how you actually build a Lightning Component. We're going to create a component for the Broker Record page to show us a list of the properties assigned to an individual broker.
| Open the **Developer Console** from the **Setup** menu. |	We're going to use the Developer Console to help us build the component.
|Choose **New > Lightning Component**. | When you create a Lightning Component, in addition to giving the component a name, we also need to specify where the component is going to be used. 
|Give the component a name of **BrokerProperties** and check the **Lightning Record Page** checkbox. Then click **Submit**. |	The five checkboxes allow us to add the necessary interfaces to the component. **Lightning Tab** specifies that the component can be used in Salesforce1. **Lightning Page** specifies that the component can be used on any Lightning Page that supports customization. **Lightning Record Page** specifies that the component can be used on an object Record page, which is what we will use. **Lightning Communities Page** obviously makes the component available for Communities pages. And **Lightning Quick Action** means the component can be used as a quick action.
|Point out the **implements=""** attribute. |	Because we checked the **Lightning Record Page** checkbox, we can see the interfaces **flexipage:AvailableForRecordHome** and **force:hasRecordId** have been added to the component.
|Paste the code from **Step 1** on the github page into the component, then choose **Save**. |	In the interest of time, I'm just going to copy a bit of code into the component to get us started. The Lightning Framework uses several key concepts that we can see here.
|Highlight the two **<aura:attribute>** tags. |	The first is the concept of **aura:attributes**. These are simply value providers. In other words, they are a place where we store data that can be displayed or manipulated by the component.
| Highlight the **<force:recordData>** tag. |	I mentioned something called Lightning Data Services earlier. The <force:recordData> tag is how we access Lightning Data Service within a Lightning Component. When the component loads, it uses the force:hasRecordId interface to retrieve the Id of the current record page and assigns it to the recordId attribute of force:recordData. This, in turn, tells Lightning Data Service to retrieve the rest of the fields for the designated record, based upon the layoutType - in this case the full record. Once the fields have been retrieved, they are stored in the attribute defined by the targetRecord attribute; here that is the attribute with the name broker.
|Highlight the **<lightning:card>** tag. |	In order to help us build the markup faster, the Lightning Framework uses UI elements called Base Lightning Components. These all start with **lightning:** as the first part of the tag name. The lightning:card creates a gray box with an icon and title.
|Switch back to the Broker page and choose **Setup > Edit Page**. |	In order to see our component, we need to actually put it on a page, and to that, we will use Lightning App Builder. 
|Locate the **BrokerProperties** component in the list of Custom components. |	Lightning App Builder gives us access to Standard Components, as well as the components which we have created. 
|Drag the component into the righthand column of the page layout. |	Let's place the component in the righthand column of the page.
|Click Save, and then click the Back link after the page is saved. |	We'll save the page, and we can see that it is, in fact, appearing on the page. And you can see that the component has actually grabbed the name of the broker using Lightning Data Service.
|Switch back to the Developer Console.	| But, of course, we need more data, and a place to put it in the markup.
|**REPLACE** the code in the Developer Console with the code from **Step 2** on the github page, then **Save** the file. |	We'll add a little bit more code to the component.
|Highlight **controller="PropertiesByBroker"** in the **<aura:component>** tag.	| Just like in Visualforce, we need to tell the Lightning Component which Apex Class we want to use as the controller for the component. This Apex Class simply makes a query for all of the properties that have the selected broker assigned to them.
|Highlight the **<aura:handler>** tag. |	However, unlike in Visualforce, a Lightning Component needs to instruct the server when to execute the methods in an Apex Class. For this component, we will do it when the component is initialized on the page. This event is called, literally, **init**. So, this handler is listening for the component to hit the page, and when it does, it will tell the **doInit** function to fire.
|Click the **Controller** button on the righthand side of the Developer Console. |	So, we obviously need to create the **doInit** function, which we'll do by first adding a controller file to the bundle.
|**REPLACE** all of the code in the Controller with the code from **Step 3** on the github page, and then **Save** the file. |	The first thing that you can see is that we are declaring a variable called **action**. This is pointing at the method in the Apex Controller that we want to execute. We're also sending a single parameter of **BrokerId** to the class by retrieving the Id of the current Broker record. Finally, when the Apex Controller sends its response back, the Callback for the action assigns the list of properties to the aura attribute named **properties**.
|Switch back to the **BrokerProperties.cmp** tab. |	Once the data is received from the Apex Controller, let's see how it's added to the component markup.
|Highlight the **<aura:iteration>** tag. |	Inside of the lightning:card, we have an unordered list `<ul>` that is going to display the list of properties. To do this, we use an aura iteration to loop over the contents of the aura attribute named properties, which is where we put the data when it came back from the server.
|Scroll to the end of the two **`<li>`** tags. |	For each item in the properties aura attribute, we're displaying the Name and Status.
|Switch back to the Broker page and reload to see the changes. |	Let's take a look at the updated component.
|Click the Brokers tab and choose a different broker.	| As you can see when we go to a different broker, we obviously get a different list of properties.


If you have additional time and/or questions, feel free to open any of the components in the Developer Console to show their code.


## Call to Action

 What would you like the developer to do after the demo?


* **REQUIRED**: Earn the Lightning Components badge: https://trailhead.salesforce.com/modules/lex_dev_lc_basics
* Download the Lightning Components Developer guide: https://developer.salesforce.com/docs/atlas.en-us.208.0.lightning.meta/lightning/intro_framework.htm


