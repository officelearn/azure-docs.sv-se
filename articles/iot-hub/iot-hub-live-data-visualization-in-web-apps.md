---
title: Datavisualisering i realtid av dina IoT-hubbdata i en webbapp
description: Använd ett webbprogram för att visualisera temperatur- och fuktighetsdata som samlas in från en sensor och skickas till din Iot-hubb.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675324"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualisera sensordata i realtid från din Azure IoT-hubb i ett webbprogram

![Diagram från på sluten tid](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

I den här självstudien får du lära dig hur du visualiserar sensordata i realtid som din IoT-hubb tar emot med en node.js-webbapp som körs på din lokala dator. När du har kört webbappen lokalt kan du också följa stegen för att vara värd för webbappen i Azure App Service. Om du vill försöka visualisera data i din IoT-hubb med hjälp av Power BI läser du [Använda Power BI för att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Vad du gör

* Lägg till en konsumentgrupp i din IoT-hubb som webbprogrammet använder för att läsa sensordata
* Ladda ned webbappkoden från GitHub
* Undersök webbappkoden
* Konfigurera miljövariabler för att hålla de IoT Hub-artefakter som behövs av webbappen
* Kör webbappen på utvecklingsmaskinen
* Öppna en webbsida för att se temperatur- och fuktighetsdata i realtid från din IoT-hubb
* (Valfritt) Använda Azure CLI för att vara värd för din webbapp i Azure App Service

## <a name="what-you-need"></a>Vad du behöver

* Slutför [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) handledning eller en av enheten tutorials; till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa omfattar följande krav:

  * En aktiv Azure-prenumeration
  * Ett Iot-nav under din prenumeration
  * Ett klientprogram som skickar meddelanden till din Iot-hubb

* [Ladda ner Git](https://www.git-scm.com/downloads)

* Stegen i den här artikeln förutsätter en Windows-utvecklingsdator. Du kan dock enkelt utföra dessa steg på ett Linux-system i ditt önskade skal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS) till Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägga till en konsumentgrupp i din IoT-hubb

[Konsumentgrupper](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) tillhandahåller oberoende vyer i händelseströmmen som gör det möjligt för appar och Azure-tjänster att självständigt använda data från samma Event Hub-slutpunkt. I det här avsnittet lägger du till en konsumentgrupp i IoT-hubbens inbyggda slutpunkt som webbappen ska använda för att läsa data från.

Kör följande kommando för att lägga till en konsumentgrupp i den inbyggda slutpunkten för IoT-hubben:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Notera ner det namn du väljer, behöver du det senare i den här guiden.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Skaffa en tjänstanslutningssträng för din IoT-hubb

IoT-hubbar skapas med flera standardåtkomstprinciper. En sådan princip **service** är tjänstprincipen, som ger tillräcklig behörighet för en tjänst för att läsa och skriva IoT-hubbens slutpunkter. Kör följande kommando för att hämta en anslutningssträng för IoT-hubben som följer serviceprincipen:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Anslutningssträngen ska se ut ungefär så här:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Notera ner tjänsten anslutningssträngen, behöver du det senare i den här guiden.

## <a name="download-the-web-app-from-github"></a>Ladda ned webbappen från GitHub

Öppna ett kommandofönster och ange följande kommandon för att hämta exemplet från GitHub och ändra till exempelkatalogen:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Undersök webbappkoden

Öppna webbappen i din favoritredigerare i katalogen webb-apps-node-iot-hub-data-visualisering. Följande visar filstrukturen som visas i VS-kod:

![Filstruktur för webbapp](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Ta en stund att undersöka följande filer:

* **Server.js** är ett skript på tjänstsidan som initierar webbuttaget och klassen Event Hub wrapper. Det ger en motringning till klassen Event Hub wrapper som klassen använder för att sända inkommande meddelanden till webbuttaget.

* **Event-hub-reader.js** är ett tjänstskript som ansluter till IoT-hubbens inbyggda slutpunkt med den angivna anslutningssträngen och konsumentgruppen. Den extraherar DeviceId och EnqueuedTimeUtc från metadata på inkommande meddelanden och vidarebefordrar sedan meddelandet med hjälp av den motringningsmetod som registrerats av server.js.

* **Chart-device-data.js** är ett klientskript som lyssnar på webbuttaget, håller reda på varje DeviceId och lagrar de senaste 50 punkterna med inkommande data för varje enhet. Den binder sedan de markerade enhetsdata till diagramobjektet.

* **Index.html** hanterar ui-layouten för webbsidan och refererar till nödvändiga skript för logik på klientsidan.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurera miljövariabler för webbappen

För att läsa data från din IoT-hubb behöver webbappen din IoT-hubbs anslutningssträng och namnet på den konsumentgrupp som den ska läsa igenom. Dessa strängar hämtas från processmiljön på följande rader i server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Ange miljövariabler i kommandofönstret med följande kommandon. Ersätt platshållarvärdena med serviceanslutningssträngen för IoT-hubben och namnet på den konsumentgrupp som du skapade tidigare. Citera inte strängarna.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Kör webbappen

1. Kontrollera att enheten körs och skickar data.

2. I kommandofönstret kör du följande rader för att hämta och installera refererade paket och starta webbplatsen:

   ```cmd
   npm install
   npm start
   ```

3. Du bör se utdata i konsolen som anger att webbappen har anslutit till din IoT-hubb och lyssnar på port 3000:

   ![Webbapp som startats på konsolen](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Öppna en webbsida för att se data från din IoT-hubb

Öppna en `http://localhost:3000`webbläsare till .

I listan **Välj en enhet** väljer du enheten för att se ett löpdiagram med de senaste 50 temperatur- och fuktighetsdatapunkterna som enheten har skickat till IoT-hubben.

![Webbappsida som visar temperatur och luftfuktighet i realtid](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Du bör också se utdata i konsolen som visar de meddelanden som webbappen sänder till webbläsarklienten:  

![Webbapp sändning utdata på konsolen](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Vara värd för webbappen i App Service

[Funktionen Webbappar i Azure App Service](https://docs.microsoft.com/azure/app-service/overview) tillhandahåller en plattform som en tjänst (PAAS) för att vara värd för webbprogram. Webbprogram som finns i Azure App Service kan dra nytta av kraftfulla Azure-funktioner som ytterligare säkerhet, belastningsutjämning och skalbarhet samt Azure- och partner-DevOps-lösningar som kontinuerlig distribution, pakethantering och så vidare. Azure App Service stöder webbprogram som utvecklats på många populära språk och distribueras på Windows- eller Linux-infrastruktur.

I det här avsnittet etablerar du en webbapp i App Service och distribuerar din kod till den med hjälp av Azure CLI-kommandon. Du hittar information om de kommandon som används i dokumentationen till [az webapp.](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) Innan du börjar kontrollerar du att du har slutfört stegen för att lägga till [en resursgrupp i IoT-hubben,](#add-a-consumer-group-to-your-iot-hub) [hämta en tjänstanslutningssträng för IoT-hubben](#get-a-service-connection-string-for-your-iot-hub)och [hämta webbappen från GitHub](#download-the-web-app-from-github).

1. En [App Service-plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) definierar en uppsättning beräkningsresurser för en app som finns i App Service som ska köras. I den här självstudien använder vi nivån Utvecklare/kostnadsfria för att vara värd för webbappen. Med den kostnadsfria nivån körs din webbapp på delade Windows-resurser med andra App Service-appar, inklusive appar för andra kunder. Azure erbjuder även App Service-planer för att distribuera webbappar på Linux-beräkningsresurser. Du kan hoppa över det här steget om du redan har en App Service-plan som du vill använda.

   Om du vill skapa en apptjänstplan med den kostnadsfria Windows-nivån kör du följande kommando. Använd samma resursgrupp som IoT-hubben finns i. Namnet på serviceplanen kan innehålla versaler och gemener, siffror och bindestreck.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Etablera nu en webbapp i apptjänstplanen. Parametern `--deployment-local-git` gör att webbappkoden kan laddas upp och distribueras från en Git-databas på den lokala datorn. Webbappnamnet måste vara globalt unikt och innehålla versaler och gemener, siffror och bindestreck. Var noga med att ange Nod version 10.6 eller senare för parametern, `--runtime` beroende på vilken version av Nod.js runtime du använder. Du kan `az webapp list-runtimes` använda kommandot för att få en lista över körningar som stöds.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Lägg nu till programinställningar för de miljövariabler som anger anslutningssträngen för IoT-hubb och konsumentgruppen Event hub. Enskilda inställningar är utrymme avgränsade `-settings` i parametern. Använd tjänstanslutningssträngen för IoT-hubben och konsumentgruppen som du skapade tidigare i den här självstudien. Citera inte värdena.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Aktivera webb socketprotokollet för webbappen och ange att webbappen endast ska ta emot HTTPS-begäranden (HTTP-begäranden omdirigeras till HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Om du vill distribuera koden till App Service använder du [distributionsuppgifterna på användarnivå](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Distributionsuppgifterna på användarnivå skiljer sig från dina Azure-autentiseringsuppgifter och används för Git-lokala distributioner och FTP-distributioner till en webbapp. När de har angetts är de giltiga i alla apptjänstappar i alla prenumerationer i ditt Azure-konto. Om du tidigare har angett distributionsautentiseringsuppgifter på användarnivå kan du använda dem.

   Om du inte tidigare har angett distributionsuppgifter på användarnivå eller om du inte kommer ihåg ditt lösenord kör du följande kommando. Ditt distributionsanvändarnamn måste vara unikt i Azure och det får inte innehålla @-symbolen för lokala Git-pushes. När du uppmanas att ange och bekräfta ditt nya lösenord. Lösenordet måste vara minst åtta tecken långt, med två av följande tre element: bokstäver, siffror och symboler.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Få Git-URL:en att använda för att skicka koden till App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Lägg till en fjärrkontroll till klonen som refererar till Git-databasen för webbappen i App Service. För \<Git-klon-URL\>använder du url:en som returnerades i föregående steg. Kör följande kommando i kommandofönstret.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Om du vill distribuera koden till App Service anger du följande kommando i kommandofönstret. Om du uppmanas att ange autentiseringsuppgifter för distribution på användarnivå som du skapade i steg 5. Se till att du trycker till huvudgrenen på App Service-fjärrkontrollen.

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

10. Kör följande kommando för att fråga om webbappens tillstånd och kontrollera att den körs:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Gå till `https://<your web app name>.azurewebsites.net` i en webbläsare. En webbsida som liknar den du såg när du körde webbappen lokalt visas. Förutsatt att enheten körs och skickar data bör du se en löpande tomt med de 50 senaste temperatur- och fuktighetsavläsningarna som skickas av enheten.

## <a name="troubleshooting"></a>Felsökning

Om du stöter på några problem med det här exemplet provar du stegen i följande avsnitt. Om du fortfarande har problem, skicka oss feedback längst ner i detta ämne.

### <a name="client-issues"></a>Problem med klienten

* Om en enhet inte visas i listan eller om inget diagram ritas kontrollerar du att enhetskoden körs på enheten.

* Öppna utvecklarverktygen (i många webbläsare öppnas den med F12-tangenten) och hittar konsolen. Leta efter varningar eller fel som skrivs ut där.

* Du kan felsöka skript på klientsidan i /js/chat-device-data.js.

### <a name="local-website-issues"></a>Lokala webbplatsfrågor

* Titta på utdata i fönstret där du lanserade noden för konsolutgång.

* Felsöka serverkoden, särskilt server.js och /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problem med Azure App Service

* Gå till din webbapp i Azure-portalen. Under **Övervakning** i den vänstra rutan väljer du **Apptjänstloggar**. Aktivera **Programloggning (Filsystem),** ange **Nivå** till Fel och välj sedan **Spara**. Öppna sedan **Loggström** (under **Övervakning).**

* Från webbappen i Azure-portalen väljer du **Konsol** `node -v` under **Utvecklingsverktyg** och validerar nod- och npm-versioner med och `npm -v`.

* Om du ser ett fel om att inte hitta ett paket kan du ha kört stegen i fel ordning. När platsen distribueras `git push`(med) körs `npm install`apptjänsten , som körs baserat på den aktuella versionen av noden som den har konfigurerat. Om det ändras i konfigurationen senare måste du göra en meningslös ändring av koden och skicka igen.

## <a name="next-steps"></a>Nästa steg

Du har använt webbappen för att visualisera sensordata i realtid från din IoT-hubb.

Ett annat sätt att visualisera data från Azure IoT Hub finns i [Använda Power BI för att visualisera sensordata i realtid från din IoT-hubb](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
