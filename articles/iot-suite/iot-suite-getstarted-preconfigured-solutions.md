<properties
    pageTitle="Komma igång med förkonfigurerade lösningar | Microsoft Azure"
    description="I den här självstudiekursen lär du dig hur du distribuerar en förkonfigurerad Azure IoT Suite-lösning."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>


# Självstudiekurs: Komma igång med förkonfigurerade lösningar

## Introduktion

Azure IoT Suites [förkonfigurerade lösningar][lnk-preconfigured-solutions] kombinerar flera Azure IoT-tjänster för att leverera lösningar från slutpunkt till slutpunkt som implementerar vanliga IoT-företagsscenarier. Med den förkonfigurerade *fjärrövervakningslösningen* kan du ansluta till och övervaka dina enheter. Du kan använda lösningen för att analysera dataströmmen från dina enheter och för att förbättra affärsresultat genom att konfigurera processer så att de svarar automatiskt på dataströmmen.

I den här självstudiekursen lär du dig hur du etablerar den förkonfigurerade lösningen för fjärrövervakning. Du lär dig också om de grundläggande funktionerna i fjärrövervakningslösningen. Du kan komma åt många av dessa funktioner via instrumentpanelen för lösningen som distribueras tillsammans med den förkonfigurerade lösningen:

![Instrumentpanel för den förkonfigurerade fjärrövervakningslösningen][img-dashboard]

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

> [AZURE.NOTE]  Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## Visa instrumentpanelen för lösningen

På instrumentpanelen för lösningen kan du hantera den distribuerade lösningen. Du kan till exempel visa telemetri, lägga till enheter och konfigurera regler.

1.  När etableringen har slutförts och panelen för din förkonfigurerade lösning visar statusen **Klar** klickar du på **Starta** för att öppna portalen för fjärrövervakningslösningen på en ny flik.

    ![Starta den förkonfigurerade lösningen][img-launch-solution]

2.  Som standard visar lösningsportalen *instrumentpanelen för lösningen*. Du kan välja andra vyer från menyn till vänster.

    ![Instrumentpanel för den förkonfigurerade fjärrövervakningslösningen][img-dashboard]

Följande information visas på instrumentpanelen:

- Kartan visar platsen för alla enheter som är kopplade till lösningen. Första gången du kör lösningen finns det fyra simulerade enheter. De simulerade enheterna implementeras som Azure WebJobs och lösningen använder Bing Maps-API:et för att rita information på kartan.
- Panelen **Telemetrihistorik** ritar upp fuktighets- och temperaturtelemetri från en vald enhet nästan i realtid och visar aggregerade data, till exempel högsta, lägsta och genomsnittlig fuktighet.
- Panelen **Larmhistorik** visar de senaste larmhändelserna när ett telemetrivärde har överskridit ett tröskelvärde. Du kan definiera dina egna larm förutom exemplen som skapas med den förkonfigurerade lösningen.

## Visa enhetslistan

Enhetslistan visar alla registrerade enheter i lösningen. Du visar och redigerar enhetsmetadata, lägger till eller tar bort enheter och skickar kommandon till enheter.

1.  Klicka på **Enheter** på den vänstra menyn för att visa *enhetslistan* för den här lösningen.

    ![Enhetslista på instrumentpanelen][img-devicelist]

2.  Enhetslistan visar att det finns fyra simulerade enheter som skapats av etableringsprocessen.

3.  Klicka på en enhet i enhetslistan för att visa information om enheten.

    ![Enhetsinformation på instrumentpanelen][img-devicedetails]

Panelen **Enhetsinformation** innehåller tre delar:

- Avsnittet **Åtgärder** visar de åtgärder som du kan utföra på enheten. Om du inaktiverar enheten kan den inte längre skicka telemetri eller ta emot kommandon. Om du inaktiverar en enhet kan du aktivera den igen. Du kan lägga till en regel som är kopplad till enheten och som utlöser ett larm när ett telemetrivärde överskrider ett tröskelvärde. Du kan också skicka ett kommando till en enhet. Första gången en enhet ansluter meddelar den lösningen vilka kommandon som den kan svara på.
- Avsnittet **Egenskaper** innehåller metadata för enheten. Vissa av dessa metadata kommer från själva enheten (till exempel tillverkaren) och vissa genereras av lösningen (till exempel tiden då den skapades). Du kan redigera enhetens metadata här.
- Avsnittet **Autentiseringsnycklar** innehåller de nycklar som enheten kan använda för att autentisera med lösningen.

## Skicka ett kommando till en enhet

Rutan med enhetsinformation visar alla kommandon som en specifik enhet stöder och gör att du kan skicka kommandon till en enhet. Första gången en enhet startar skickar den information om de kommandon som den stöder till lösningen.

1.  Klicka på **Kommandon** i rutan med enhetsinformation för den valda enheten.

    ![Enhetskommandon på instrumentpanelen][img-devicecommands]

2.  Välj **PingDevice** i kommandolistan.

3.  Klicka på **Skicka kommando**.

4.  Du kan se statusen för kommandot i kommandohistoriken.

    ![Kommandostatus på instrumentpanelen][img-pingcommand]

Lösningen spårar statusen för varje kommando som skickas. Resultatet är till en början **Väntande**. När enheten rapporterar att den har kört kommandot ändras resultatet till **Lyckades**.

## Lägga till en ny simulerad enhet

När du distribuerar den förkonfigurerade lösningen etablerar du automatiskt fyra exempelenheter som du ser i enhetslistan. Dessa enheter är *simulerade enheter* som körs i ett Azure-webbjobb. Simulerade enheter gör det lätt att experimentera med den förkonfigurerade lösningen utan att behöva distribuera verkliga fysiska enheter. Om du vill ansluta en verklig enhet till lösningen går du självstudiekursen [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm].

Följande steg beskriver hur du lägger till en simulerad enhet i lösningen:

1.  Gå tillbaka till enhetslistan.

2.  Klicka på **+ Lägg till en enhet** i det nedre vänstra hörnet för att lägga till en ny enhet.

    ![Lägga till en enhet i den förkonfigurerade lösningen][img-adddevice]

3.  Klicka på **Lägg till ny** på panelen **Simulerad enhet**.

    ![Ange information om den nya enheten på instrumentpanelen][img-addnew]
    
    Förutom att skapa en ny simulerad enhet kan du också lägga till en fysisk enhet om du väljer att skapa en **anpassad enhet**. Mer information om hur du ansluter fysiska enheter till lösningen finns i [Ansluta enheten till den förkonfigurerade övervakningslösningen i IoT Suite][lnk-connect-rm].

4.  Välj **Låt mig ange mitt eget enhets-ID** och ange ett unikt enhets-ID, t.ex. **mydevice_01**.

5.  Klicka på **Skapa**.

    ![Spara den nya enheten][img-definedevice]

6. I steg 3 i **Lägga till en simulerad enhet** klickar du på **Klar** för att gå tillbaka till enhetslistan.

7. Du kan se att din enhet **körs** i listan över enheter.

    ![Visa den nya enheten i enhetslistan][img-runningnew]

8. Du kan också visa den simulerade telemetrin från den nya enheten på instrumentpanelen:

    ![Visa telemetri från den nya enheten][img-runningnew-2]

## Redigera enhetsmetadata

Första gången en enhet ansluter till lösningen skickar den sina metadata till lösningen. När du redigerar enhetsmetadata via instrumentpanelen för lösningen skickar den de nya metadatavärdena till enheten och lagrar de nya värdena i DocumentDB-databasen för lösningen. Mer information finns i [Enhetsidentitetsregistret och DocumentDB][lnk-devicemetadata].

1.  Gå tillbaka till enhetslistan.

2.  Välj den nya enheten i **enhetslistan** och klicka sedan på **Redigera** för att redigera **enhetsegenskaperna**:

    ![Redigera enhetsmetadata][img-editdevice]

3. Bläddra nedåt och ändra värdena för latitud och longitud. Klicka på **Spara ändringar i enhetsregistret**.

    ![Redigera enhetsmetadata][img-editdevice2]

4. Gå tillbaka till instrumentpanelen. Enhetens plats på kartan har ändrats:

    ![Redigera enhetsmetadata][img-editdevice3]

## Lägga till en regel för den nya enheten

Det finns inga regler för den nya enheten som du har lagt till. I det här avsnittet ska du lägga till en regel som utlöser ett larm när temperaturen som rapporteras av den nya enheten överstiger 47 grader. Notera innan du börjar att telemetrihistoriken för den nya enheten på instrumentpanelen visar att enhetens temperatur aldrig överstiger 45 grader.

1.  Gå tillbaka till enhetslistan.

2.  Välj den nya enheten i **enhetslistan** och klicka sedan på **Lägg till regel** för att lägga till en regel för enheten.

3. Skapa en regel som använder **Temperatur** som datafält och **AlarmTemp** som utdata när temperaturen överstiger 47 grader:

    ![Lägga till en enhetsregel][img-adddevicerule]

4. Spara ändringarna genom att klicka på **Spara och visa regler**.

5.  Klicka på **Kommandon** i rutan med enhetsinformation för den nya enheten.

    ![Lägga till en enhetsregel][img-adddevicerule2]

6.  Välj **ChangeSetPointTemp** från kommandolistan och ange **SetPointTemp** till 45. Klicka på **Skicka kommando**:

    ![Lägga till en enhetsregel][img-adddevicerule3]

7.  Gå tillbaka till instrumentpanelen för lösningen. Efter en kort stund visas en ny post i rutan **Larmhistorik** när temperaturen som rapporteras av den nya enheten överstiger tröskelvärdet på 47 grader:

    ![Lägga till en enhetsregel][img-adddevicerule4]

8. Du kan granska och redigera alla regler på sidan **Regler** på instrumentpanelen:

    ![Visa en lista över enhetsregler][img-rules]

9. Du kan granska och redigera alla åtgärder som kan vidtas som svar på en regel på sidan **Åtgärder** på instrumentpanelen:

    ![Visa en lista över enhetsåtgärder][img-actions]

> [AZURE.NOTE] Du kan definiera åtgärder som kan skicka ett e-postmeddelande eller ett SMS som svar på en regel eller integrera med ett affärssystem via en [logikapp][lnk-logic-apps]. Mer information finns i [Ansluta Logic App till den förkonfigurerade fjärrövervakningslösningen i Azure IoT Suite][lnk-logicapptutorial].

## Andra funktioner

Från lösningsportalen kan du söka efter enheter med specifika egenskaper, till exempel ett modellnummer:

![Söka efter en enhet][img-search]

Du kan inaktivera en enhet och när den har inaktiverats kan du ta bort den:

![Inaktivera och ta bort en enhet][img-disable]

## I bakgrunden

När du distribuerar en förkonfigurerad lösning skapar distributionsprocessen flera resurser i Azure-prenumerationen som du valt. Du kan visa dessa resurser på Azure-[portalen][lnk-portal]. Under distributionsprocessen skapas en **resursgrupp** med ett namn baserat på det namn som du valde för den förkonfigurerade lösningen:

![Förkonfigurerad lösning på Azure-portalen][img-portal]

Du kan visa inställningarna för varje resurs genom att välja resursen i listan över resurser i resursgruppen.

Du kan också visa källkoden för den förkonfigurerade lösningen. Källkoden för den förkonfigurerade fjärrövervakningslösningen finns i [azure-iot-remote-monitoring][lnk-rmgithub]-databasen på GitHub:

- Mappen **DeviceAdministration** innehåller källkoden för instrumentpanelen.
- Mappen **Simulator** innehåller källkoden för den simulerade enheten.
- Mappen **EventProcessor** innehåller källkoden för backend-processen som hanterar den inkommande telemetrin.

När du är klar kan du ta bort den förkonfigurerade lösningen från Azure-prenumeration på webbplatsen [azureiotsuite.com][lnk-azureiotsuite]. På den här webbplatsen kan du enkelt ta bort alla resurser som etablerades när du skapade den förkonfigurerade lösningen.

> [AZURE.NOTE] För att vara säker på att du tar bort allt relaterat till den förkonfigurerade lösningen tar du bort den från [azureiotsuite.com][lnk-azureiotsuite] i stället för att bara ta bort resursgruppen på portalen.

## Nästa steg

Nu när du har distribuerat en fungerande förkonfigurerad lösning kan du fortsätta och lära dig mer om IoT Suite genom att läsa följande artiklar:

- [Genomgång av den förkonfigurerade fjärrövervakningslösningen][lnk-rm-walkthrough]
- [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm]
- [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md



<!--HONumber=Sep16_HO3-->


