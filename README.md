ngRemoteTK
==========

An Angular service wrapper for RemoteTK

Dependencies
------------
This wrapper greatly depends on the RemoteTK project. You will need to install [RemoteTK](https://github.com/developerforce/Force.com-JavaScript-REST-Toolkit) in your org for this wrapper to work.

Installation
------------
1. Add the ngRemoteTK as a new component in your salesforce org.
2. Include the component in your visualforce page(s).
3. Write your custom code using this new Angular service!

Example of usage
----------------
```visualforce
<apex:page docType="html-5.0"
  sidebar="false"
  showheader="false"
  standardStyleSheets="false"
  applyHtmlTag="false"
  applyBodyTag="false">
  
  <html ng-app="myApp">
    <head>
      <script src="//code.jquery.com/jquery-1.11.0.min.js"></script>
      <script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.21/angular.min.js"></script>
      <!-- RemoteTK -->
      <c:RemoteTK />

      <!-- ngRemoteTK -->
      <c:ngRemoteTK appNameSpace="myApp" />

      <script type="text/javascript">
        //RemoteTK Client
        var rtkClient = new remotetk.Client();

        //Angular App
        var app = angular.module('myApp', ['myApp.services']);

        //Angular Controller
        app.controller('myAppCtrl', function ($scope, $q, ngRemoteTK){

          var acctId = 'some sfdc account id';


          /* ngRemoteTK magic! */

          // Retrieve a record example
          ngRemoteTK.retrieve('account',acctId,'Id, Name, Industry').then(function(response){
            //We don't want the attributes. We only want the actual fields and values for the object
            if(response.hasOwnProperty('attributes')){
              delete response.attributes;
            }
            $scope.account = response;
            console.log('Account: ' + JSON.stringify($scope.account));
          });

          // Upsert a record example
          $scope.submit = function(){ 
            console.log('Account about to be submitted: ' + JSON.stringify($scope.account));
            ngRemoteTK.upsert('account', 'Id', acctId, $scope.account);
          };

        });
      </script>
    </head>
    <body ng-controller="myAppCtrl">
      <button type="button" ng-click="submit()">Upsert Test</button>
    </body>
  </html>
  
</apex:page>
```

Special Note
------------
There is a bug in the Salesfoce RemoteTK.component at line 129 under the method "remotetk.Client.prototype.upsert". I've submitted a fix/pull request, but while we are waiting for that to get merged you can simply make this quick change yourself:

Change line 129 from:
'$RemoteAction.RemoteTKController.upser'

To:
'{!$RemoteAction.RemoteTKController.upser}'