---
title: Node.js-program med Socket.io - Azure
description: Lär dig hur du använder socket.io i ett node.js-program som finns på Azure.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 0b515c630d8a3539cdab1df64b1925e9fcaf206e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360777"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Skapa ett node.js-chattprogram med Socket.IO på en Azure Cloud Service

Socket.IO ger realtidskommunikation mellan nod.js-servern och klienterna. Den här självstudien går igenom värd för ett uttag. IO-baserat chattprogram på Azure. Mer information om Socket.IO finns [i socket.io](https://socket.io).

En skärmdump av den färdiga ansökan är nedan:

![Ett webbläsarfönster som visar tjänsten som finns på Azure][completed-app]  

## <a name="prerequisites"></a>Krav
Kontrollera att följande produkter och versioner är installerade för att kunna fylla i exemplet i den här artikeln:

* Installera [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Installera [nod.js](https://nodejs.org/download/)
* Installera [Python version 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Skapa ett molntjänstprojekt
Följande steg skapar molntjänstprojektet som ska vara värd för Socket.IO-programmet.

1. Sök efter **Windows PowerShell**på **Start-menyn** eller **Startskärmen**. Högerklicka slutligen på **Windows PowerShell** och välj **Kör som administratör**.
   
    ![Ikon för Azure PowerShell][powershell-menu]
2. Skapa en katalog som heter **\\c: nod**. 
   
        PS C:\> md node
3. Ändra kataloger till **katalogen\\c: nod**
   
        PS C:\> cd node
4. Ange följande kommandon för att skapa en ny lösning med namnet **chatapp** och en arbetarroll med namnet **WorkerRole1:**
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Följande svar visas:
   
    ![Utdata från de nya azureservice och add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Ladda ner chattexemplet
För det här projektet använder vi chattexemplet från [Socket.IO GitHub-databasen]. Gör följande för att hämta exemplet och lägga till det i projektet som du tidigare skapade.

1. Skapa en lokal kopia av databasen med knappen **Klona.** Du kan också använda **ZIP-knappen** för att ladda ner projektet.
   
   ![En webbläsarfönstervisning https://github.com/LearnBoost/socket.io/tree/master/examples/chat, med zip-hämtningsikonen markerad](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navigera i katalogstrukturen för den lokala databasen tills du kommer till **\\exempelchattkatalogen.** Kopiera innehållet i den här katalogen till **katalogen C:\\nod\\chatapp\\WorkerRole1** som skapats tidigare.
   
   ![Explorer, visar innehållet i\\exemplen chattkatalog extraheras från arkivet][chat-contents]
   
   De markerade objekten i skärmbilden ovan är de filer som kopieras från **\\exemplen chattkatalog**
3. I **katalogen\\C:\\\\nod chatapp WorkerRole1** tar du bort **filen server.js** och byter sedan namn på **filen app.js** till **server.js**. Detta tar bort standardfilen **server.js** som skapats tidigare av cmdleten **Add-AzureNodeWorkerRole** och ersätter den med programfilen från chattexemplet.

### <a name="modify-serverjs-and-install-modules"></a>Ändra server.js och installationsmoduler
Innan du testar programmet i Azure-emulatorn måste vi göra några mindre ändringar. Utför följande steg i filen server.js:

1. Öppna **filen server.js** i Visual Studio eller textredigerare.
2. Leta reda på avsnittet **Modulberoenden** i början av server.js och ändra raden som innehåller **sio = require('.. //.. lib//socket.io')** till **sio = require('socket.io')** enligt nedan:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. För att säkerställa att programmet lyssnar på rätt port, öppna server.js i Anteckningar eller din favoritredigerare och ändra sedan följande rad genom att ersätta **3000** med **process.env.port** som visas nedan:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

När du har sparat ändringarna på **server.js**använder du följande steg för att installera nödvändiga moduler och testar sedan programmet i Azure-emulatorn:

1. Med **Azure PowerShell**ändrar du kataloger till **katalogen\\C: nod\\chatapp\\WorkerRole1** och använder följande kommando för att installera de moduler som krävs av det här programmet:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Detta kommer att installera modulerna som anges i package.json-filen. När kommandot är klart bör du se utdata som liknar följande:
   
   ![Utdata från kommandot npm install][The-output-of-the-npm-install-command]
2. Eftersom det här exemplet ursprungligen var en del av Socket.IO GitHub-databasen och direkt refererade till Socket.IO-biblioteket efter relativ sökväg, refererades Socket.IO inte i filen package.json, så vi måste installera det genom att utfärda följande kommando:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testa och distribuera
1. Starta emulatorn genom att utfärda följande kommando:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Om du stöter på problem med att starta emulator, t.ex.: Start-AzureEmulator: Ett oväntat fel uppstod.  Information: Påträffade ett oväntat fel Kommunikationsobjektet System.ServiceModel.Channels.ServiceChannel kan inte användas för kommunikation eftersom det är i feltillstånd.
   > 
   > Installera om AzureAuthoringTools v 2.7.1 och AzureComputeEmulator v 2.7 – se till att versionen matchar.

2. Öppna en webbläsare **http://127.0.0.1**och navigera till .
3. När webbläsarfönstret öppnas anger du ett smeknamn och trycker sedan enter.
   På så sätt kan du skicka meddelanden som ett specifikt smeknamn. Om du vill testa fleranvändarfunktioner öppnar du ytterligare webbläsarfönster med samma WEBBADRESS och anger olika smeknamn.
   
   ![Två webbläsarfönster som visar chattmeddelanden från Användare1 och Användare2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. När du har testat programmet stoppar du emulatorn genom att utfärda följande kommando:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Om du vill distribuera programmet till Azure använder du cmdleten **Publish-AzureServiceProject.** Ett exempel:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Var noga med att använda ett unikt namn, annars misslyckas publiceringsprocessen. När distributionen har slutförts öppnas webbläsaren och navigerar till den distribuerade tjänsten.
   > 
   > Om du får ett felmeddelande om att det angivna prenumerationsnamnet inte finns i den importerade publiceringsprofilen måste du hämta och importera publiceringsprofilen för din prenumeration innan du distribuerar till Azure. Se **avsnittet Distribuera programmet till Azure** i Build och distribuera ett [Node.js-program till en Azure Cloud Service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Ett webbläsarfönster som visar tjänsten som finns på Azure][completed-app]
   
   > [!NOTE]
   > Om du får ett felmeddelande om att det angivna prenumerationsnamnet inte finns i den importerade publiceringsprofilen måste du hämta och importera publiceringsprofilen för din prenumeration innan du distribuerar till Azure. Se **avsnittet Distribuera programmet till Azure** i Build och distribuera ett [Node.js-program till en Azure Cloud Service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Ditt program körs nu på Azure och kan vidarebefordra chattmeddelanden mellan olika klienter med hjälp av Socket.IO.

> [!NOTE]
> För enkelhetens skull är det här exemplet begränsat till att chatta mellan användare som är anslutna till samma instans. Det innebär att om molntjänsten skapar två arbetsrollinstanser kan användarna bara chatta med andra som är anslutna till samma arbetsrollinstans. Om du vill skala programmet så att det fungerar med flera rollinstanser kan du använda en teknik som Service Bus för att dela Socket.IO butikstillstånd över instanser. Exempel på exempel finns i användningsexexemplen för servicebussköer och ämnen i [Azure SDK för Node.js GitHub-databasen](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig hur du skapar ett grundläggande chattprogram som finns i en Azure Cloud Service. Mer information om hur du är värd för det här programmet på en Azure-webbplats finns i [Skapa ett nod.js-chattprogram med Socket.IO på en Azure-webbplats][chatwebsite].

Mer information finns i [Node.js Developer Center](https://docs.microsoft.com/azure/javascript/).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-databas]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png





