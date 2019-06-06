---
title: Data i realtid visualisering av sensordata från Azure IoT hub i en webbapp | Microsoft Docs
description: Använd ett webbprogram för att visualisera temperatur- och data som samlas in från en sensor och skickas till din Iot-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475998"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualisera sensordata i realtid från Azure IoT hub i ett webbprogram

![Slutpunkt till slutpunkt-diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

I den här självstudien får lära du att visualisera sensordata i realtid som din IoT-hubb tar emot med en node.js-webbapp som körs på den lokala datorn. När du kör webbappen lokalt, kan du också följa stegen för att vara värd för webbappen i Azure App Service. Om du vill försöka att visualisera data i IoT-hubben med hjälp av Power BI, se [Använd Power BI för att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Vad du gör

* Lägga till en konsumentgrupp i din IoT-hubb som webbprogrammet ska använda för att läsa sensordata
* Ladda ned koden för webbappen från GitHub
* Granska koden för webbappen
* Konfigurera miljövariabler för att lagra IoT-hubben artefakter som krävs av din webbapp
* Kör webbappen på utvecklingsdatorn
* Öppna en webbsida om du vill se temperatur och fuktighet realtidsdata från IoT hub
* (Valfritt) Använd Azure CLI för att vara värd för din webbapp i Azure App Service

## <a name="what-you-need"></a>Vad du behöver

* Slutför den [Raspberry Pi onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md) självstudien eller någon av enheten självstudiekurser; exempelvis [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa omfattar följande krav:

  * En aktiv Azure-prenumeration
  * En Iot-hubb i din prenumeration
  * Ett klientprogram som skickar meddelanden till din Iot-hubb

* [Hämta Git](https://www.git-scm.com/downloads)

* I den här artikeln förutsätter vi en Windows-utvecklingsdator; Du kan dock enkelt utföra dessa steg på ett Linux-system i ditt önskade gränssnitt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS) för vissa kommandon i Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägg till en konsumentgrupp till din IoT hub

[Konsumentgrupper](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) ger oberoende vyer i händelseströmmen som gör att appar och Azure-tjänster oberoende använda data från samma Event Hub-slutpunkt. I det här avsnittet ska du lägga till en konsumentgrupp din IoT-hubb inbyggd slutpunkt som webbappen använder för att läsa data från.

Kör följande kommando för att lägga till en konsumentgrupp i den inbyggda slutpunkten för din IoT-hubb:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Obs ned det namn som du väljer du behöver det senare i den här självstudien.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Hämta en tjänstanslutningssträngen för din IoT-hubb

IoT-hubbar skapas med flera standard-åtkomstprinciper. En sådan princip är den **service** policy, som ger tillräcklig behörighet för en tjänst för att läsa och skriva den IoT hub-slutpunkter. Kör följande kommando för att få en anslutningssträng för IoT-hubben som följer principen service:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Anslutningssträngen ska se ut ungefär så här:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Anteckna tjänstanslutningssträngen, du behöver det senare i den här självstudien.

## <a name="download-the-web-app-from-github"></a>Ladda ned webbappen från GitHub

Öppna ett kommandofönster och ange följande kommandon för att ladda ned exemplet från GitHub och ändra till katalogen exemplet:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Granska koden för webbappen

Öppna appen i din favoritredigerare från katalogen web-apps-node-iot-hub-data-visualization. Nedan visas filstruktur som visas i VS Code:

![Filstruktur för Web app](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Ta en stund att granska följande filer:

* **Server.js** är ett tjänstsidan-skript som initierar web socket och adapterklass Event Hub. Det ger en motringning till Event Hub-adapterklass som klassen används för att sända inkommande meddelanden till webb-socket.

* **Händelse-hub-reader.js** är ett skript för tjänsten på klientsidan som ansluter till IoT-hubbens inbyggda slutpunkten med hjälp av den angivna anslutning sträng- och konsumentprogram gruppen. Den extraherar DeviceId och EnqueuedTimeUtc från metadata på inkommande meddelanden och vidarebefordrar meddelandet med motringningsmetoden som registrerats av server.js.

* **Diagram-enhet-data.js** är ett skript för klientsidan som lyssnar på web-socket håller reda på varje DeviceId och lagrar de senaste 50 punkterna för inkommande data för varje enhet. Valda enhetsdata Binder sedan till diagramobjektet.

* **Index.HTML** hanterar UI-layouten för sidan, refererar till de nödvändiga skript för klientsidan logik.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurera miljövariabler för webbappen

För att läsa data från IoT hub behöver webbappen anslutningssträngen för din IoT-hubb och namnet på konsumentgrupp som ska läsas. De här strängarna hämtar från process-miljön i följande rader i server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Ange miljövariabler i kommandofönstret med följande kommandon. Ersätt platshållarvärdena med tjänstanslutningssträngen för din IoT-hubb och namnet på konsumentgrupp som du skapade tidigare. Inte citera strängarna.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Kör webbappen

1. Se till att din enhet körs och skickar data.

2. Kör följande rader för att ladda ned och installera refererade paket och starta webbplatsen i kommandofönstret:

   ```cmd
   npm install
   npm start
   ```

3. Du bör se utdata i konsolen som anger att webbappen har lyckats ansluta till din IoT-hubb och lyssnar på port 3000:

   ![Webbapp som är igång på konsolen](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Öppna en webbsida för att se data från IoT hub

Öppna en webbläsare till `http://localhost:3000`.

I den **väljer du en enhet** väljer du enheten för att se en rityta som körs på de senaste 50 temperatur och fuktighet datapunkter som skickas av enheten till IoT hub.

![Web app-sidan som visar i realtid temperatur och fuktighet](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Du bör också se utdata i konsolen som visar meddelandena som din webbapp är aktivt till webbläsarklienten:  

![Web app broadcast utdata på konsolen](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Var värd för webbapp i App Service

Den [Web Apps-funktionen i Azure App Service](https://docs.microsoft.com/azure/app-service/overview) tillhandahåller en plattform som en tjänst (PAAS) som värd för webbprogram. Webbprogram i Azure App Service kan dra nytta av kraftfulla Azure-funktioner som ytterligare säkerhet, belastningsutjämning, och skalbarhet som samt Azure och partnern DevOps-lösningar som kontinuerlig distribution, pakethantering, och så vidare. Azure App Service stöder webbprogram som har utvecklats i många populära språk och distribueras på Windows eller Linux-infrastruktur.

I det här avsnittet ska du etablera en webbapp i App Service och distribuera din kod till den med hjälp av Azure CLI-kommandon. Du hittar information om de kommandon som används i den [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) dokumentation. Innan du startar, kontrollera att du har slutfört stegen för att [lägga till en resursgrupp i din IoT-hubb](#add-a-consumer-group-to-your-iot-hub), [hämta en tjänstanslutningssträngen för din IoT-hubb](#get-a-service-connection-string-for-your-iot-hub), och [ladda ned webbappen från GitHub](#download-the-web-app-from-github).

1. En [App Service-plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definierar en uppsättning beräkningsresurser för en app som finns i App Service ska köras. I den här självstudien använder vi utvecklare/kostnadsfria nivån som värd för webbappen. Med den kostnadsfria nivån web Apps som körs på delade Windows-resurser med andra App Service-appar, inklusive appar från andra kunder. Azure även erbjudanden som App Service-planer att distribuera web apps på Linux beräkningsresurser. Du kan hoppa över det här steget om du redan har en App Service-plan som du vill använda.

   Kör följande kommando för att skapa en App Service-plan med hjälp av den kostnadsfria nivån av Windows. Använd samma resursgrupp som din IoT hub finns i. Service-planens namn kan innehålla övre och gemena bokstäver, siffror och bindestreck.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Nu etablera en webbapp i App Service-planen. Den `--deployment-local-git` parametern kan koden för webbappen överförs till och distribueras från en Git-lagringsplats på din lokala dator. Webbappens namn måste vara globalt unikt och får innehålla övre och gemena bokstäver, siffror och bindestreck.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Lägg nu till programinställningar för de miljövariabler som som anger anslutningssträngen för IoT hub och konsumentgrupp för Event hub. Individuella inställningar är blankstegsavgränsad i den `-settings` parametern. Använd tjänstanslutningssträngen för din IoT-hubb och konsumentgrupp som du skapade tidigare i den här självstudien. Inte citera värdena.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Aktivera Web Sockets-protokollet för webbappen och ange webbappen att ta emot HTTPS-begäranden endast (HTTP-begäranden ska omdirigeras till HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. För att distribuera koden till App Service, ska du använda din [användarnivå distributionsbehörigheterna](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Dina autentiseringsuppgifter för distribution på användarnivå skiljer sig från dina Azure-autentiseringsuppgifter och används för lokal Git och FTP-distributioner till en webbapp. När är de giltig för alla dina App Service-appar i alla prenumerationer i ditt Azure-konto. Om du tidigare har autentiseringsuppgifter för distribution på användarnivå, kan du använda dem.

   Om du inte tidigare har autentiseringsuppgifter för distribution på användarnivå eller om du inte kommer ihåg ditt lösenord, kör du följande kommando. Ditt användarnamn i distributionen måste vara unikt i Azure och det får inte innehålla den ' @' symbolen för lokal Git push-meddelanden. När du uppmanas ange och bekräfta det nya lösenordet. Lösenordet måste vara minst åtta tecken långt, med två av följande tre element: bokstäver, siffror och symboler.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Hämta Git-URL: en du använder för att skicka koden upp till App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Lägg till en fjärransluten i din klon som refererar till Git-lagringsplats för webbappen i App Service. För \<URL för Git-klonen\>, använda URL: en som returneras i föregående steg. Kör följande kommando i kommandofönstret.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. För att distribuera koden till App Service, anger du följande kommando i kommandofönstret. Om du tillfrågas om autentiseringsuppgifter, anger du autentiseringsuppgifterna för användarnivå distribution som du skapade i steg 5. Kontrollera att du har överfört till huvudgrenen för App Service-fjärranslutna.

    ```cmd
    git push webapp master:master
    ```

9. Förloppet för distributionen uppdateras i kommandofönstret. En lyckad distribution avslutas med rader som liknar följande utdata:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Kör följande kommando för att fråga efter tillståndet för webbappen och se till att den körs:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Gå till `https://<your web app name>.azurewebsites.net` i en webbläsare. En webbsida som liknar den du såg när du körde webbappen lokalt visar. Förutsatt att enheten kör och skickar data, bör du se en pågående rityta av de 50 senaste temperatur och fuktighet avläsningar som skickats av enheten.

## <a name="troubleshooting"></a>Felsökning

Om du stöter på problem med det här exemplet kan du testa stegen i följande avsnitt. Om du fortfarande har problem kan du skicka oss feedback längst ned i det här avsnittet.

### <a name="client-issues"></a>Klientproblem

* Om en enhet inte visas i listan, eller inget diagram ritas, kontrollera att enheten koden körs på din enhet.

* I webbläsaren, öppna utvecklarverktyg (i många webbläsare på F12 nyckel öppnas det), och hitta konsolen. Leta efter eventuella varningar och fel som är tryckt.

* Du kan felsöka klientskript i /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problem med lokal webbplats

* Titta på resultatet i fönstret där du startade nod för konsolens utdata.

* Felsök serverkoden, särskilt server.js och /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service-problem

* Gå till din webbapp i Azure-portalen. Under **övervakning** i den vänstra rutan väljer **Apptjänst loggar**. Aktivera **Programinloggning (filsystem)** , ange **nivå** till fel och sedan väljer **spara**. Öppna sedan **loggström** (under **övervakning**).

* Från din webbapp i Azure-portalen under **utvecklingsverktyg** Välj **konsolen** och validera node- och npm-versioner med `node -v` och `npm -v`.

* Om du ser ett fel om inte att hitta ett paket kan har du kört stegen i fel ordning. När platsen har distribuerats (med `git push`) app service körs `npm install`, som körs baserat på den aktuella versionen av noden som den har konfigurerats. Om som ändras i konfigurationen senare, måste du göra en meningslöst ändring i koden och skicka igen.

## <a name="next-steps"></a>Nästa steg

Du har använt din webbapp för att visualisera sensordata i realtid från IoT hub.

Ett annat sätt att visualisera data från Azure IoT Hub finns [Använd Power BI för att visualisera sensordata i realtid från IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
