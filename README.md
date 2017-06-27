# Lightning Component BRK

The following code snippets are for use with the Lightning Components BRK.

### Install Package

[https://login.salesforce.com/packaging/installPackage.apexp?p0=04t6A0000016uhv](https://login.salesforce.com/packaging/installPackage.apexp?p0=04t6A0000016uhv)

### Step 1

  ```html
  <aura:attribute name="recordId" type="Id" />
  <aura:attribute name="broker" type="Broker__c" />
  <force:recordData aura:id="propertyService" 
                    recordId="{!v.recordId}" 
                    targetFields="{!v.broker}"
                    layoutType="FULL" />
  <lightning:card iconName="custom:custom85" title="{! 'Properties for ' + v.broker.Name}">

  </lightning:card>
  ```
  
### Step 2

  ```html
  <aura:component controller="PropertiesByBroker" implements="flexipage:availableForRecordHome,force:hasRecordId" access="global" >
      <aura:attribute name="recordId" type="Id" />
      <aura:attribute name="broker" type="Broker__c" />
      <aura:attribute name="properties" type="Object[]" />
      <aura:handler name="init" value="{!this}" action="{!c.doInit}" />
      <force:recordData aura:id="propertyService" 
                        recordId="{!v.recordId}" 
                        targetFields="{!v.broker}"
                        layoutType="FULL" />
      <lightning:card iconName="custom:custom85" title="{! 'Properties for ' + v.broker.Name}">
          <ul class="slds-list--vertical slds-has-dividers--top-space slds-m-around_medium">
              <aura:iteration items="{!v.properties}" var="item">
                  <ul class="slds-list__item">
                      <li class="slds-list__item"><span class="slds-text-color--weak slds-m-right--small">Property:</span> {!item.Name}</li>
                      <li class="slds-list__item"><span class="slds-text-color--weak slds-m-right--small">Status:</span> {!item.Status__c}</li>
                  </ul>
              </aura:iteration>           
          </ul>
      </lightning:card>
  </aura:component>
  ```
  
### Step 3

  ```js
  ({
    doInit : function(component, event, helper) {
      var action = component.get("c.getPropertiesByBroker");
          action.setParams({
              BrokerId: component.get("v.recordId")
          })
          action.setCallback(this, function(response) {
              var list = response.getReturnValue();
              component.set("v.properties", list);
          })
          $A.enqueueAction(action);
    }
  })
  ```
