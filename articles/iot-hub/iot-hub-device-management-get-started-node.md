<properties
    pageTitle="Komma igång med enhetshantering i IoT Hub | Microsoft Azure"
    description="Självstudiekurs om hur du kommer igång med Azure IoT Hub för enhetshantering med C#. Använd Azure IoT Hub och C# med Microsoft Azure IoT-SDK:er för att implementera enhetshantering."
    services="iot-hub"
    documentationCenter=".net"
    authors="ellenfosborne"
    manager="timlt"
    editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Komma igång med Azure IoT Hub-enhetshantering med node.js (förhandsversion)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introduktion
Innan du kan börja arbeta med Azure IoT Hub-enhetshantering måste du skapa en Azure IoT-hubb, etablera enheter i IoT-hubben och starta flera simulerad enheter. Den här kursen vägleder dig genom dessa steg.

> [AZURE.NOTE]  Du måste skapa en ny IoT-hubb för att aktivera enhetshanteringsfunktioner även om du har en befintlig IoT-hubb eftersom befintliga IoT-hubbar inte har enhetshanteringsfunktioner än. När enhetshantering är offentligt tillgängligt kommer alla befintliga IoT-hubbar att uppgraderas med enhetshanteringsfunktionerna.

## Krav

Följande måste vara installerat för att du ska kunna slutföra stegen:

- Git
- node
- npm
- CMake (version 2.8 eller senare). Installera CMake från <https://cmake.org/download/>. Markera kryssrutan för att lägga till CMake i den aktuella användar-PATH-variabeln.
- En aktiv Azure-prenumeration.

    Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure][lnk-free-trial].

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
  -   Markera kryssrutan för att **aktivera enhetshantering**.
  -   I **Plats** väljer du platsen som ska vara värd för din IoT-hubb. Enhetshantering i IoT Hub är bara tillgängligt i östra USA, Nordeuropa och Östasien under den offentliga förhandsgranskningen. I framtiden kommer funktionerna att vara tillgängliga i alla regioner.

  > [AZURE.NOTE]  Exemplen fungerar inte om du inte markerar kryssrutan **Aktivera enhetshantering**.

4.  När du har valt dina konfigurationsalternativ för IoT-hubben klickar du på **Skapa**. Det kan ta några minuter för Azure att skapa din IoT-hubb. Du kan kontrollera statusen genom att övervaka förloppet på **Startsidan** eller på panelen **Meddelanden**.

    ![][img-monitor]

5.  När IoT-hubben har skapats öppnar du bladet för den nya IoT-hubben, skriver ner **värdnamnet** och klickar sedan på ikonen **Nycklar**.

    ![][img-keys]

6.  Klicka på **iothubowner**-principen och kopiera och notera anslutningssträngen på **iothubowner**-bladet. Kopiera den till en plats som du kan komma åt senare eftersom du behöver den för att slutföra resten av den här kursen.

    > [AZURE.NOTE] I produktionsscenarier bör du inte använda **iothubowner**-autentiseringsuppgifterna.

    ![][img-connection]

Nu har du skapat en enhetshanteringsaktiverad IoT-hubb. Du behöver anslutningssträngen för att slutföra resten av den här självstudiekursen.

## Skapa exemplen och etablera enheter i din IoT-hubb

I det här avsnittet ska du köra ett skript som skapar den simulerade enheten och exemplen och som etablerar en uppsättning nya enhetsidentiteter i IoT-hubbens enhetsregister. En enhet kan inte ansluta till IoT Hub om den inte har en post i enhetsregistret.

Du skapar exemplen och etablerar enheter i IoT Hub genom att följa stegen nedan:

1.  Öppna terminalen.

2.  Klona github-databasen. **Klona i en katalog som inte har några tomma blanksteg.**

      ```
      git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
      ```

3.  Från rotmappen där du klonade **azure-iot-sdks**-databasen navigerar du till katalogen **azure-iot-sdks/node/service/samples**, ersätter platshållarvärdet med anslutningssträngen från föregående avsnitt och kör:

      ```
      setup.bat <IoT Hub Connection String>
      ```

Skriptet gör följande:

1.  Kör **cmake** för att skapa de nödvändiga filerna som krävs för att bygga den simulerade enheten. Den körbara filen finns i **azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample**. Observera att källfilerna finns i mappen **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample**.

2.  Skapar den simulerade enhetens körbara fil **iotdm\_simple\_sample**.

3.  Kör ``` npm install ``` för att installera de nödvändiga paketen.

4.  Kör ```node generate_devices.js``` för att etablera enhetsidentiteter i IoT Hub. Enheterna beskrivs i **sampledevices.json**. När enheterna har etablerats lagras autentiseringsuppgifterna i filen **devicecreds.txt** (som finns i mappen **azure-iot-sdks/node/service/samples**).

## Starta dina simulerade enheter

Nu när enheterna har lagts till i enhetsregistret kan du starta de simulerade hanterade enheterna. En simulerad enhet måste startas för varje enhetsidentitet som etableras i Azure IoT Hub.

Använda terminalen och kör följande i katalogen **azure-iot-sdks/node/service/samples**:

  ```
  simulate.sh
  ```

Det här skriptet returnerar de kommandon som du måste köra för att starta **iotdm\_simple\_sample** för varje enhet som anges i filen **devicecreds.txt**. Kör kommandon individuellt från ett separat terminalfönster för varje simulerad enhet. Den simulerade enheten fortsätter att köras tills du stänger kommandofönstret.

Programmet **iotdm\_simple\_sample** har skapats med klientbiblioteket för C för enhetshantering i Azure IoT Hub, vilket gör att vi kan skapa IoT-enheter som kan hanteras av Azure IoT Hub. Enhetstillverkare kan använda det här biblioteket för att rapportera egenskaper och implementera de körningsåtgärder som krävs av enhetsjobb. Det här biblioteket är en komponent som levereras som en del av Azure IoT Hub-SDK:erna med öppen källkod.

När du kör **simulate.sh** visas en dataström i utdatafönstret. Dessa utdata visar inkommande och utgående trafik samt **printf**-instruktioner i de programspecifika återanropsfunktionerna. Detta gör att du kan se inkommande och utgående trafik samtidigt som du ser hur exempelprogrammet hanterar de avkodade paketen. När enheten ansluter till IoT Hub börjar tjänsten automatiskt att observera resurser på enheten. IoT Hub DM-klientbiblioteket anropar sedan enhetens återanrop för att hämta de senaste värdena från enheten.

Nedan är utdata från exempelprogrammet **iotdm\_simple\_sample**. Överst ser du meddelandet **REGISTERED**, som visar att enheten med ID:t **Device11 7ce4a850** ansluter till IoT Hub.

> [AZURE.NOTE]  Om du vill visa mindre utförliga utdata skapar du och kör återförsäljarkonfigurationen.

![][img-output]

Låt de simulerade enheterna fortsätta köra medan du slutför självstudiekurserna i ”Nästa steg”.

## Nästa steg

Om du vill lära dig mer om enhetshanteringsfunktionerna i Azure IoT Hub går du igenom självstudiekurserna:

- [Använda enhetstvillingen][lnk-tutorial-twin]

- [Hitta enhetstvillingen med hjälp av frågor][lnk-tutorial-queries]

- [Uppdatera enhetens inbyggda programvara med hjälp av enhetsjobb][lnk-tutorial-jobs]

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure-portalen]: https://portal.azure.com/
[Hantera Azure-resurser med hjälp av resursgrupper]: ../azure-portal/resource-group-portal.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md



<!--HONumber=jun16_HO2-->


