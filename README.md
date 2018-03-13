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
  
### Step 4

  ```html
  <aura:component controller="PropertiesByBroker" implements="flexipage:availableForRecordHome,force:hasRecordId" access="global" >
      <aura:attribute name="recordId" type="Id" />
      <aura:attribute name="brokerRecord" type="Broker__c" />
      <aura:attribute name="propertyRecords" type="Object[]"  />
      <aura:attribute name="columns" type="List" />
      <aura:attribute name="selectedRow" type="String" />
      <aura:attribute name="sortedBy" type="String" />
      <aura:attribute name="sortedDirection" type="String" />
      <aura:attribute name="showDialog" type="String" />

      <aura:handler name="init" value="{!this}" action="{!c.doInit}" />
      <aura:handler event="c:recordUpdated" action="{!c.doInit}" /> 

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
            <c:PropertiesByBrokerEdit recordId="{!v.selectedRow}" showDialog="{!v.showDialog}" />
      </lightning:card>
  </aura:component>
  ```
 
### Step 5

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
            fieldName: "url", //there is no url field on the record, we are using this for the purpose of navigation
            type: "url",
            sortable: "true",
            typeAttributes: { label: { fieldName: "Name" } }
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
      },
      handleRowAction: function (cmp, event, helper) {
          var action = event.getParam('action');
          var row = event.getParam('row');
          switch (action.name) {
              case 'edit_details':
                  cmp.set("v.showDialog", "true");
                  cmp.set("v.selectedRow", row.Id);
                  break;
              case 'delete':
                  var rows = cmp.get('v.propertyRecords');
                  var rowIndex = rows.indexOf(row);
                  rows.splice(rowIndex, 1);
                  cmp.set('v.propertyRecords', rows);
                  break;
          }
      },
    doSelect: function(component, event, helper) {
      var selectedRows = event.getParam('selectedRows');
      for (var i = 0; i < selectedRows.length; i++) {
        alert("You selected: " + selectedRows[i].Name);
      }
    },
    updateColumnSorting: function(component, event, helper) {
      var fieldName = event.getParam('fieldName');
      var sortDirection = event.getParam('sortDirection');
          if (fieldName == "url") {
              fieldName = "Name";
          }
      component.set("v.sortedBy", fieldName);
      component.set("v.sortedDirection", sortDirection);
      helper.sortData(component, fieldName, sortDirection);
    }
  })  
  ```
