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
      <aura:attribute name="brokerRecord" type="Broker__c" />
      <aura:attribute name="propertyRecords" type="Object[]"  />
      <aura:attribute name="columns" type="List" />

      <aura:handler name="init" value="{!this}" action="{!c.doInit}" />

      <force:recordData aura:id="brokerLookup" 
                        recordId="{!v.recordId}"
                        targetFields="{!v.brokerRecord}" 
                        fields="Picture__c" 
                        layoutType="FULL"  />

      <lightning:card iconName="custom:custom85" title="{! 'Properties for ' + v.brokerRecord.Name }">
          <lightning:datatable aura:id="datatable" 
                               data="{!v.propertyRecords}" 
                               columns="{!v.columns}" 
                               keyField="id" 
                               resizeColumnDisabled="true"
                               onrowselection="{!c.doSelect}"
                               onrowaction="{! c.handleRowAction }"
                               onsort="{!c.updateColumnSorting}" />
              </lightning:card>
  </aura:component>
  ```
  
### Step 3

  ```js
  ({
    doInit: function(component, event, helper) {
      var rowLevelActions = [
        { label: 'Edit Details', name: 'edit_details' },
        { label: 'Delete', name: 'delete' }
          ];
      component.set("v.columns", [
        {
          label: "Property",
          fieldName: "url", //there is no url field, we are using it for the purpose of navigation
          type: "url",
          sortable: "true", // we won't actually finish this in the demo
          typeAttributes: { label: { 
              fieldName: "Name" // this is what will be displayed in the column
              } 
          }
        },
        {
          label: "Status",
          fieldName: "Status__c"
        },
        {
          type: 'action',
          typeAttributes: { rowActions: rowLevelActions }
        }
          ]);
      var action = component.get("c.getPropertiesByBroker");
      action.setParams({
        BrokerId: component.get("v.recordId")
      });
      action.setCallback(this, function(response) {
        var propertyList = response.getReturnValue();
        propertyList.forEach(function(element){
                  // url is a temporary field that we will use for the purpose of navigation
          element.url = "/one/one.app#/sObject/" + element.Id;
        });
        component.set("v.propertyRecords", propertyList);
      })
      $A.enqueueAction(action);
    }
  })
  ```
