---
title: Självstudier – Skapa en MEAN-stack på en virtuell Linux-dator i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du skapar en stack för MongoDB, Express, AngularJS och Node.js (MEAN) på en virtuell Linux-dator i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a23dd71b9453a11298192fc909c5a3c9708f11b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962308"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Självstudier: Skapa en stack för MongoDB, Express, AngularJS och Node.js (MEAN) på en virtuell Linux-dator i Azure

Den här självstudiekursen beskriver hur du implementerar en stack för MongoDB, Express, AngularJS och Node.js (MEAN) på en virtuell Linux-dator i Azure. MEAN-stacken som du skapar gör det möjligt att lägga till, ta bort och lista böcker i en databas. Lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell Linux-dator
> * Installera Node.js
> * Installera MongoDB och konfigurera servern
> * Installera Express och konfigurera vägar till servern
> * Få åtkomst till vägar med AngularJS
> * Köra programmet

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Skapa en virtuell Linux-dator

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) och skapa en virtuell Linux-dator med kommandot [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel används Azure CLI för att skapa en resursgrupp med namnet *myResourceGroupMEAN* på platsen *eastus*. En virtuell dator som heter *myVM*, skapas med SSH-nycklar om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet --ssh-key-value.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Se till att använda rätt offentlig IP-adress. I vårt exempel ovan var vår IP-adress 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Installera Node.js

[Node.js](https://nodejs.org/en/) är en JavaScript-körning som bygger på Chromes V8 JavaScript-motor. Node.js används i den här självstudiekursen för att ställa in Express-vägar och AngularJS-styrenheter.

Installera Node.js på den virtuella datorn med hjälp av det bash-gränssnitt som du öppnade med SSH.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Installera MongoDB och konfigurera servern
[MongoDB](http://www.mongodb.com) lagrar data i flexibla JSON-liknande dokument. Fält i en databas kan variera från dokument till dokument, och datastrukturen kan ändras med tiden. För vår exempelprogram lägger vi till bokposter till MongoDB som innehåller titel, ISBN-nummer, författare och antal sidor. 

1. Konfigurera MongoDB-nyckeln på den virtuella datorn med hjälp av det bash-gränssnitt som du öppnade med SSH.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Uppdatera pakethanteraren med nyckeln.
  
    ```bash
    sudo apt-get update
    ```

3. Installera MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Starta servern.

    ```bash
    sudo service mongodb start
    ```

5. Vi behöver även installera [body-parsern](https://www.npmjs.com/package/body-parser-json)-paketet som ska hjälpa oss att bearbeta JSON som skickats i förfrågningar till servern.

    Installera npm-pakethanteraren.

    ```bash
    sudo apt-get install npm
    ```

    Installera body-parser-paketet.
    
    ```bash
    sudo npm install body-parser
    ```

6. Skapa en mapp med namnet *Böcker* och lägg till en fil i den som heter *server.js* och innehåller webbserverns konfiguration.

    ```node.js
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Installera Express och konfigurera vägar till servern

[Express](https://expressjs.com) är ett minimalt och flexibelt Node.js-programramverk som innehåller funktioner för webb- och mobilprogram. Express används i den här självstudiekursen för att skicka bokinformation till och från vår MongoDB-databas. [Mongoose](http://mongoosejs.com) tillhandahåller en enkel schemabaserad lösning för modellering av dina programdata. Mongoose används i den här självstudiekursen för att tillhandahålla en bokschema för databasen.

1. Installera Express och Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. Skapa en mapp med namnet *appar* i mappen *Böcker* och lägg till en fil med namnet *routes.js* med de definierade expressvägarna.

    ```node.js
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. Skapa en mapp med namnet *modeller* i mappen *appar* och lägg till en fil med namnet *book.js* med den definierade bokmodellskonfigurationen.  

    ```node.js
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Få åtkomst till vägar med AngularJS

[AngularJS](https://angularjs.org) tillhandahåller ett webbramverk med vilket du kan skapa dynamiska vyer i dina webbprogram. I den här självstudiekursen använder vi AngularJS för att ansluta vår webbsida med Express och utföra åtgärder på vår bokdatabas.

1. Ändra katalogen tillbaka till *Böcker* (`cd ../..`), och skapa sedan en mapp med namnet *offentligt* och lägg till en fil med namnet *script.js* med den definierade styrenhetskonfigurationen.

    ```node.js
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. Skapa en fil med namnet *index.html* i mappen *offentligt* med den definierade webbsidan.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Köra programmet

1. Ändra katalogen tillbaka till *Böcker* (`cd ..`) och starta servern genom att köra följande kommando:

    ```bash
    nodejs server.js
    ```

2. Öppna en webbläsare på den adress som du registrerade för den virtuella datorn. Till exempel *http://13.72.77.9:3300*. Du bör se något som liknar följande sida:

    ![Bokpost](media/tutorial-mean/meanstack-init.png)

3. Fyll i data i textrutorna och klicka på **Lägg till**. Exempel:

    ![Lägg till en bokpost](media/tutorial-mean/meanstack-add.png)

4. När du har uppdaterat sidan bör du se något som liknar följande sida:

    ![Lista bokposter](media/tutorial-mean/meanstack-list.png)

5. Du kan klicka på **ta bort** och ta bort bokposten från databasen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du skapat ett webbprogram som håller koll på bokposter med hjälp av en MEAN-stack på en virtuell Linux-dator. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell Linux-dator
> * Installera Node.js
> * Installera MongoDB och konfigurera servern
> * Installera Express och konfigurera vägar till servern
> * Få åtkomst till vägar med AngularJS
> * Köra programmet

Gå vidare till nästa självstudiekurs där du får lära dig att skydda webbservrar med SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker webbserver med SSL](tutorial-secure-web-server.md)
