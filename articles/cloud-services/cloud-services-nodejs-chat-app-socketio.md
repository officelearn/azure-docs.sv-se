---
title: Node.js-App med Socket.io - Azure
description: Lär dig hur du använder socket.io i ett node.js-program på Azure.
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: 0fae47f248d5662b69a0d1a12c82b7ded33badd6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001991"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Skapa en Node.js-chattprogram med Socket.IO på en Azure-molntjänst

Socket.IO gör i realtid kommunikation mellan din node.js-server och klienter. Den här självstudien vägleder dig genom som är värd för en socket. I/o-baserat chattprogram på Azure. Läs mer på Socket.IO [socket.io](http://socket.io).

En skärmbild av det färdiga programmet understiger:

![Ett webbläsarfönster som visar den tjänst som finns i Azure][completed-app]  

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att följande produkter och versioner är installerade för att slutföra exemplet i den här artikeln:

* Installera [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Installera [Node.js](https://nodejs.org/download/)
* Installera [Python version 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Skapa ett Molntjänstprojekt
Följande steg skapar molntjänstprojektet som är värd för programmet Socket.IO.

1. Från den **Start-menyn** eller **startskärmen**, Sök efter **Windows PowerShell**. Slutligen, högerklicka på **Windows PowerShell** och välj **kör som administratör**.
   
    ![Azure PowerShell-ikonen][powershell-menu]
2. Skapa en katalog med namnet **c:\\noden**. 
   
        PS C:\> md node
3. Ändra sökvägen till den **c:\\noden** directory
   
        PS C:\> cd node
4. Ange följande kommandon för att skapa en ny lösning med namnet **chatapp** och en arbetsroll som heter **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Följande meddelande visas:
   
    ![Utdata från den nya-azureservice och Lägg till azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Ladda ned exemplet chatt
För det här projektet ska vi använda chatt-exempel från den [Socket.IO GitHub-lagringsplats]. Utför följande steg för att ladda ned exemplet och lägga till den i projektet som du skapade tidigare.

1. Skapa en lokal kopia av databasen med hjälp av den **klona** knappen. Du kan också använda den **ZIP** för att ladda ned projektet.
   
   ![En webbläsare fönstret visa https://github.com/LearnBoost/socket.io/tree/master/examples/chat, med ikonen ZIP hämta markerat][chat-example-view]
2. Navigera katalogstrukturen på den lokala lagringsplatsen tills du kommer till den **exempel\\chatt** directory. Kopiera innehållet i den här katalogen till den **C:\\nod\\chatapp\\WorkerRole1** katalog som skapades tidigare.
   
   ![Explorer visar innehållet i exemplen\\chatt directory extraheras från arkivet][chat-contents]
   
   De markerade objekten i skärmbilden ovan är de filer som kopierats från den **exempel\\chatt** directory
3. I den **C:\\nod\\chatapp\\WorkerRole1** directory, ta bort den **server.js** filen och Byt sedan namn på den **app.js** filen till **server.js**. Detta tar bort standard **server.js** filen som skapades tidigare i den **Lägg till AzureNodeWorkerRole** cmdlet och ersätter den med programfilen från exemplet chatt.

### <a name="modify-serverjs-and-install-modules"></a>Ändra Server.js och installera moduler
Vi måste göra några mindre ändringar innan du testar programmet i Azure-emulatorn. Utför följande steg för att server.js-fil:

1. Öppna den **server.js** filen i Visual Studio eller valfri textredigerare.
2. Hitta de **modulen beroenden** avsnittet i början av server.js och ändra den rad som innehåller **sio = require('..//.. lib//socket.IO')** till **sio = require('socket.io')** enligt nedan:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Öppna server.js i anteckningar eller redigeringsprogram du föredrar för att säkerställa att programmet lyssnar på rätt port, och sedan ändra följande rad genom att ersätta **3000** med **process.env.port** enligt nedan:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

När du har sparat ändringarna **server.js**, Använd följande steg för att installera moduler som krävs och testa programmet i Azure-emulatorn:

1. Med hjälp av **Azure PowerShell**, ändrar du katalog till den **C:\\nod\\chatapp\\WorkerRole1** katalogen och Använd sedan följande kommando för att installera modulerna krävs av programmet:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Detta installerar de moduler som anges i package.json-fil. När kommandot har slutförts bör du se utdata som liknar följande:
   
   ![Utdata från npm installationskommando][The-output-of-the-npm-install-command]
2. Eftersom det här exemplet är en del av Socket.IO GitHub-lagringsplatsen och refereras direkt i biblioteket med Socket.IO av relativ sökväg, refererades inte Socket.IO i package.json-fil, så vi måste installera den genom att följande kommando:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testa och distribuera
1. Starta emulatorn genom att följande kommando:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Om du stöter på problem med att starta emulatorn, t.ex.: Start-AzureEmulator: ett oväntat fel uppstod.  Information: Påträffade kan ett oväntat fel Kommunikationsobjekt System.ServiceModel.Channels.ServiceChannel, inte användas för kommunikation eftersom den har statusen Faulted.
   
      installera om AzureAuthoringTools v 2.7.1 och AzureComputeEmulator v 2.7 – Kontrollera att versionen matchar.
   >
   >


2. Öppna en webbläsare och gå till **http://127.0.0.1**.
3. När webbläsarfönstret öppnas, ange ett smeknamn och sedan tryck på RETUR.
   Detta kommer att du kan publicera meddelanden som en specifik smeknamn. Om du vill testa funktionen för flera användare öppna ytterligare webbläsarfönster med samma URL och ange olika smeknamn.
   
   ![Två webbläsarfönster som visar några meddelanden från Användare1 och Användare2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. När du testar programmet kan du stoppa emulatorn genom att utfärda följande kommando:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Om du vill distribuera programmet till Azure, använda den **Publish-AzureServiceProject** cmdlet. Exempel:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Se till att använda ett unikt namn, annars misslyckas publiceringsprocessen. När distributionen är klar öppnas i webbläsaren och navigera till den distribuerade tjänsten.
   > 
   > Om du får ett felmeddelande om att angivna prenumerationens namn inte finns i den importera publikationsprofilen, måste du hämta och importera publiceringsprofilen för din prenumeration innan du distribuerar till Azure. Se den **distribuera programmet till Azure** delen av [skapa och distribuera ett Node.js-program till en Azure-molntjänst](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Ett webbläsarfönster som visar den tjänst som finns i Azure][completed-app]
   
   > [!NOTE]
   > Om du får ett felmeddelande om att angivna prenumerationens namn inte finns i den importera publikationsprofilen, måste du hämta och importera publiceringsprofilen för din prenumeration innan du distribuerar till Azure. Se den **distribuera programmet till Azure** delen av [skapa och distribuera ett Node.js-program till en Azure-molntjänst](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Programmet körs nu på Azure och kan vidarebefordra chattmeddelanden mellan olika klienter med Socket.IO.

> [!NOTE]
> Det här exemplet är begränsad till chatta mellan användare som är anslutna till samma-instans för enkelhetens skull. Det innebär att om Molntjänsten skapar två arbetarrollinstanser, användare kommer bara att kunna chatta med andra anslutna till samma worker-rollinstans. Du kan använda en teknik som Service Bus för att dela arkivtillståndet Socket.IO över instanser för att skala programmet att fungera med flera rollinstanser. Exempel finns exempel Service Bus-köer och ämnen om användning i den [Azure SDK för Node.js GitHub-lagringsplatsen](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att skapa ett grundläggande chattprogram i en Azure-molntjänst. Om du vill lära dig mer om att vara värd för det här programmet i en Azure-webbplats, se [och skapa ett Node.js-chattprogram med Socket.IO på en Azure Web Site][chatwebsite].

Mer information finns också i [Node.js-Utvecklingscenter](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: http://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-lagringsplats]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


