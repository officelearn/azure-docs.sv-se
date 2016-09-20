<properties
    pageTitle="Komma igång med enhetshantering i IoT Hub | Microsoft Azure"
    description="Självstudiekurs om hur du kommer igång med Azure IoT Hub för enhetshantering med C#. Använd Azure IoT Hub och C# med Microsoft Azure IoT-SDK:er för att implementera enhetshantering."
    services="iot-hub"
    documentationCenter=".net"
    authors="juanjperez"
    manager="timlt"
    editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="juanpere"/>

# Komma igång med Azure IoT Hub-enhetshantering med node.js (förhandsversion)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introduktion
Innan du kan börja använda enhetshantering med Azure IoT Hub måste du skapa en Azure IoT-hubb, etablera enheter i IoT Hub, starta flera simulerade enheter och visa dessa enheter i exempelanvändargränssnittet för enhetshantering. Den här kursen vägleder dig genom dessa steg.

> [AZURE.NOTE]  Du måste skapa en ny IoT-hubb för att aktivera enhetshanteringsfunktioner även om du har en befintlig IoT-hubb eftersom befintliga IoT-hubbar inte har dessa funktioner än. När enhetshantering är offentligt tillgängligt kommer alla befintliga IoT-hubbar att uppgraderas med enhetshanteringsfunktionerna.

## Krav

I den här självstudiekursen förutsätter vi att du använder en Linux-utvecklingsdator med Ubuntu.

Följande programvara måste vara installerad för att du ska kunna utföra stegen:

- Git

- gcc (version 4.9 eller senare). Du kan kontrollera vilken versionen som är installerad i din miljö genom att köra kommandot `gcc --version`. Information om hur du uppgraderar din version av gcc i Ubuntu 14.04 finns här: <http://askubuntu.com/questions/466651/how-do-i-use-the-latest-gcc-4-9-on-ubuntu-14-04>.

- [CMake](https://cmake.org/download/) (version 2.8 eller senare). Du kan kontrollera vilken versionen som är installerad i din miljö genom att köra kommandot `cmake --version`.

- Node.js 6.1.0 eller senare.  Installera Node.js för din plattform från <https://nodejs.org/>.

- En aktiv Azure-prenumeration. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure][lnk-free-trial].

## Skapa en enhetshanteringsaktiverad IoT-hubb

Du måste skapa en enhetshanteringsaktiverad IoT-hubb som dina simulerade enheter kan ansluta till. Följande steg visar hur du kan slutföra den här åtgärden med hjälp av Azure-portalen.

1.  Logga in på [Azure-portalen].
2.  I snabbåtkomstfältet klickar du på **Nytt**, sedan på **Sakernas Internet** och sedan på **Azure IoT Hub**.

    ![][img-new-hub]

3.  Välj konfigurationen för din IoT-hubb på bladet **IoT Hub**.

    ![][img-configure-hub]

  -   I rutan **Namn** anger du ett namn för din IoT-hubb. Om **namnet** är giltigt och tillgängligt visas en grön kryssmarkering i **namnrutan**.
  -   Välj en **pris- och skalningsnivå**. Den här guiden kräver inte en specifik nivå.
  -   I **Resursgrupp** skapar du en ny resursgrupp eller väljer en befintlig. Mer information finns i [Hantera Azure-resurser med hjälp av resursgrupper].
  -   Markera kryssrutan för att **aktivera enhetshantering**. Exemplen fungerar inte om du inte markerar kryssrutan **Aktivera enhetshantering**. Genom att markera **Aktivera enhetshantering** skapar du en förhandsgranskning av IoT Hub som endast stöds i östra USA, norra Europa och Östasien och som inte är avsedd för produktionsscenarier. Du kan inte migrera enheter till och från hubbar som har aktiverats för enhetshantering.
  -   I **Plats** väljer du platsen som ska vara värd för din IoT-hubb. Enhetshantering i IoT Hub är bara tillgängligt i östra USA, Nordeuropa och Östasien under den offentliga förhandsgranskningen. I framtiden kommer funktionerna att vara tillgängliga i alla regioner.

4.  När du har valt dina konfigurationsalternativ för IoT-hubben klickar du på **Skapa**. Det kan ta några minuter för Azure att skapa din IoT-hubb. Du kan kontrollera statusen genom att övervaka förloppet på **Startsidan** eller på panelen **Meddelanden**.

    ![][img-monitor]

5.  När IoT-hubben har skapats öppnar du bladet för den nya IoT-hubben, skriver ner **värdnamnet** och klickar sedan på **Policyer för delad åtkomst**.

    ![][img-keys]

6.  Klicka på **iothubowner**-principen och kopiera och notera anslutningssträngen på **iothubowner**-bladet. Kopiera den till en plats som du kan komma åt senare eftersom du behöver den för att slutföra resten av den här kursen.

    > [AZURE.NOTE] I produktionsscenarier bör du inte använda **iothubowner**-autentiseringsuppgifterna.

    ![][img-connection]

Nu har du skapat en enhetshanteringsaktiverad IoT-hubb. Du behöver anslutningssträngen för att slutföra resten av den här självstudiekursen.

## Skapa exemplen och etablera enheter i din IoT-hubb

I det här avsnittet ska du köra ett skript som skapar den simulerade enheten och exemplen och som etablerar en uppsättning nya enhetsidentiteter i enhetsregistret för IoT Hub. En enhet kan inte ansluta till IoT Hub om den inte har en post i enhetsregistret.

Du skapar exemplen och etablerar enheter i IoT Hub genom att följa dessa steg:

1.  Öppna ett gränssnitt.

2.  Klona github-databasen. **Klona i en katalog som inte har några tomma blanksteg.**

      ```
      git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
      ```

3.  Från rotmappen där du klonade **azure-iot-sdks**-databasen navigerar du till katalogen **azure-iot-sdks/c/build_all/linux** och kör följande kommando för att installera de nödvändiga paketen och beroende bibliotek:

      ```
      ./setup.sh
      ```


4.  Från rotmappen där du klonade **azure-iot-sdks**-databasen navigerar du till katalogen **azure-iot-sdks/node/service/samples**, kör följande kommando och ersätter platshållarvärdet med anslutningssträngen från föregående avsnitt:

      ```
      ./setup.sh <IoT Hub Connection String>
      ```

Skriptet gör följande:

1.  Kör **cmake** för att skapa de nödvändiga filerna som krävs för att bygga den simulerade enheten. Den körbara filen finns i **azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample**. Observera att källfilerna finns i mappen **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample**.

2.  Skapar den simulerade enhetens körbara fil **iotdm\_simple\_sample**.

3.  Kör `npm install` för att installera de nödvändiga paketen.

4.  Kör `node generate_devices.js` för att etablera enhetsidentiteter i IoT Hub. Enheterna beskrivs i **sampledevices.json**. När enheterna har etablerats lagras autentiseringsuppgifterna i filen **devicecreds.txt** (som finns i mappen **azure-iot-sdks/node/service/samples**).

## Starta dina simulerade enheter

Nu när enheterna har lagts till i enhetsregistret kan du starta de simulerade hanterade enheterna. Du måste starta en simulerad enhet för varje enhetsidentitet som etableras i Azure IoT Hub.

Använd ett gränssnitt, navigera till katalogen **azure-iot-sdks/node/service/samples** och kör:

  ```
  ./simulate.sh
  ```

Det här skriptet returnerar de kommandon som du måste köra för att starta **iotdm\_simple\_sample** för varje enhet som anges i filen **devicecreds.txt**. Kör kommandona separat från ett separat terminalfönster för varje simulerad enhet. Den simulerade enheten fortsätter att köras tills du stänger kommandofönstret.

Programmet **iotdm\_simple\_sample** har skapats med klientbiblioteket för C för enhetshantering i Azure IoT Hub, vilket gör att vi kan skapa IoT-enheter som kan hanteras av Azure IoT Hub. Enhetstillverkare kan använda det här biblioteket för att rapportera egenskaper och implementera de körningsåtgärder som krävs av enhetsjobb. Det här biblioteket är en komponent som levereras som en del av Azure IoT Hub-SDK:erna med öppen källkod.

När du kör **simulate.sh** visas en dataström i utdatafönstret. Dessa utdata visar inkommande och utgående trafik samt **printf**-instruktioner i de programspecifika återanropsfunktionerna. Dessa utdata gör att du kan se inkommande och utgående trafik samtidigt som du ser hur exempelprogrammet hanterar de avkodade paketen. När enheten ansluter till IoT Hub börjar tjänsten automatiskt att observera resurser på enheten. IoT Hub DM-klientbiblioteket anropar sedan enhetens återanrop för att hämta de senaste värdena från enheten.

Här är utdata från **iotdm\_simple\_sample**-exempelprogrammet. Överst ser du meddelandet **REGISTERED**, som visar att enheten med ID:t **Device11 7ce4a850** ansluter till IoT Hub.

> [AZURE.NOTE]  Om du vill visa mindre utförliga utdata skapar du och kör återförsäljarkonfigurationen.

![][img-output]

Se till att alla simulerade enheter fortfarande körs när du utför stegen i följande avsnitt.

## Köra exempelanvändargränssnittet för enhetshantering

Nu när du har etablerat en IoT-hubb och har flera simulerade enheter som körs och som har registrerats för hantering kan du distribuera exempelgränssnittet för enhetshantering. Exempelanvändargränssnittet för enhetshantering är ett exempel på hur du kan använda API:er för enhetshantering för att skapa en interaktiv gränssnittsupplevelse.  Mer information om exempelanvändargränssnittet för enhetshantering, inklusive [kända problem](https://github.com/Azure/azure-iot-device-management#knownissues), finns i [Azure IoT device management UI][lnk-dm-github]-databasen på GitHub.

Hämta, skapa och kör exempelanvändargränssnittet för enhetshantering genom att följa dessa steg:

1. Öppna ett gränssnitt.

2. Kontrollera att du har installerat Node.js 6.1.0 eller senare enligt anvisningarna i kravavsnittet genom att skriva `node --version`.

3. Klona GitHub-databasen Azure IoT device management UI genom att köra följande kommando i gränssnittet:

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Kör följande kommando för att hämta beroende paket i rotmappen för din klonade kopia av databasen Azure IoT device management UI:

    ```
    npm install
    ```

5. När npm install-kommandot har slutförts kör du följande kommando i gränssnittet för att skapa koden:

    ```
    npm run build
    ```

6. Använd en textredigerare för att öppna filen user-config.json i roten för den klonade mappen. Ersätt texten ”&lt;YOUR CONNECTION STRING HERE&gt;” med IoT Hub-anslutningssträngen från föregående avsnitt och spara filen.

7. Kör följande kommando i gränssnittet för att starta UX-appen för enhetshantering:

    ```
    npm run start
    ```

8. När kommandotolken rapporterar att tjänsterna har startat öppnar du en webbläsare och går till enhetshanteringsappen på följande URL för att visa dina simulerade enheter: <http://127.0.0.1:3003>.

    ![][img-dm-ui]

Se till att de simulerade enheterna och enhetshanteringsappen fortfarande körs när du går vidare till nästa självstudiekurs om enhetshantering.


## Nästa steg

Om du vill fortsätta att lära dig om IoT Hub går du till [Komma igång med Gateway-SDK][lnk-gateway-SDK].

Om du vill lära dig mer om enhetshanteringsfunktionerna i Azure IoT Hub går du självstudiekursen [Utforska Azure IoT Hub-enhetshantering med hjälp av exempelanvändargränssnittet][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started-node/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started-node/image2.png
[img-monitor]: media/iot-hub-device-management-get-started-node/image3.png
[img-keys]: media/iot-hub-device-management-get-started-node/image4.png
[img-connection]: media/iot-hub-device-management-get-started-node/image5.png
[img-output]: media/iot-hub-device-management-get-started-node/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started-node/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure-portalen]: https://portal.azure.com/
[Hantera Azure-resurser med hjälp av resursgrupper]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md


<!--HONumber=sep16_HO1-->


