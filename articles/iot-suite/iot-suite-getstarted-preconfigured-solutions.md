---
title: "Komma igång med förkonfigurerade lösningar | Microsoft Docs"
description: "I den här självstudiekursen lär du dig hur du distribuerar en förkonfigurerad Azure IoT Suite-lösning."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: e68815c2dafc596c3560ad3fcb2a7bf96d29182b
ms.lasthandoff: 04/18/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Självstudiekurs: Komma igång med förkonfigurerade lösningar
## <a name="introduction"></a>Introduktion
Azure IoT Suites [förkonfigurerade lösningar][lnk-preconfigured-solutions] kombinerar flera Azure IoT-tjänster för att leverera lösningar från slutpunkt till slutpunkt som implementerar vanliga IoT-företagsscenarier. Med den förkonfigurerade *fjärrövervakningslösningen* kan du ansluta till och övervaka dina enheter. Du kan använda lösningen för att analysera dataströmmen från dina enheter och för att förbättra affärsresultat genom att konfigurera processer så att de svarar automatiskt på dataströmmen.

I den här självstudiekursen lär du dig hur du etablerar den förkonfigurerade lösningen för fjärrövervakning. Vi går också igenom de grundläggande funktionerna i den förkonfigurerade lösningen. Du kan komma åt många av dessa funktioner från *instrumentpanelen* för lösningen som distribueras som en del av den förkonfigurerade lösningen:

![Instrumentpanel för den förkonfigurerade fjärrövervakningslösningen][img-dashboard]

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

> [!NOTE]
> Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Scenarioöversikt

När du distribuerar den fjärranslutna förinställda övervakningslösningen innehåller den redan resurser som hjälper dig att gå igenom ett vanligt scenario för fjärransluten övervakning. I det här scenariot rapporterar flera enheter som är anslutna till lösningen oväntade temperaturvärden. I avsnitten nedan får du information om följande:

* Identifiera de enheter som skickar oväntade temperaturvärden.
* Konfigurera dessa enheter för att få fram mer detaljerad telemetri.
* Åtgärda problemet genom att uppdatera den inbyggda programvaran på dessa enheter.
* Kontrollera att åtgärden har löst problemet.

En viktig egenskap i detta scenario är att du kan utföra alla dessa åtgärder via fjärranslutning från instrumentpanelen med lösningar. Du behöver inte fysisk åtkomst till enheterna.

## <a name="view-the-solution-dashboard"></a>Visa instrumentpanelen för lösningen

På instrumentpanelen för lösningen kan du hantera den distribuerade lösningen. Du kan till exempel visa telemetri, lägga till enheter och konfigurera regler.

1. När etableringen har slutförts och panelen för din förkonfigurerade lösning visar statusen **Klar** klickar du på **Starta** så öppnas portalen för fjärrövervakningslösningen på en ny flik.

    ![Starta den förkonfigurerade lösningen][img-launch-solution]

1. Som standard visar lösningsportalen *instrumentpanelen*. Du kan navigera till andra delar av lösningsportalen via menyn till vänster på sidan.

    ![Instrumentpanel för den förkonfigurerade fjärrövervakningslösningen][img-menu]

Följande information visas på instrumentpanelen:

* En karta visar platsen för alla enheter som är kopplade till lösningen. Första gången du kör lösningen finns det 25 simulerade enheter. De simulerade enheterna implementeras som Azure WebJobs och lösningen använder Bing Maps-API:et för att rita information på kartan. Se [vanliga frågor och svar][lnk-faq] för information om hur du gör kartan dynamisk.
* Panelen **Telemetrihistorik** ritar upp fuktighets- och temperaturtelemetri från en vald enhet nästan i realtid och visar aggregerade data, till exempel högsta, lägsta och genomsnittlig fuktighet.
* Panelen **Larmhistorik** visar de senaste larmhändelserna när ett telemetrivärde har överskridit ett tröskelvärde. Du kan definiera dina egna larm förutom exemplen som skapas med den förkonfigurerade lösningen.
* På panelen **Jobb** visas information om schemalagda jobb. Du kan schemalägga egna jobb på sidan **Hanteringsjobb**.

## <a name="view-alarms"></a>Visa larm

Panelen med larmhistorik visar att fem enheter rapporterar högre telemetrivärden än förväntat.

![TODO-larmhistorik på instrumentpanelen för lösningen][img-alarms]

> [!NOTE]
> Dessa larm genereras av en regel som ingår i den förinställda lösningen. Den här regeln genererar en avisering när temperaturvärdet som skickas av en enhet överstiger 60. Du kan definiera egna regler och åtgärder genom att välja [Regler](#add-a-rule) och [Åtgärder](#add-an-action) i den vänstra menyn.

## <a name="view-devices"></a>Visa enheter

I listan *Enheter* visas alla registrerade enheter i lösningen. Från enhetslistan kan du se och redigera enhetsmetadata, lägga till eller ta bort enheter och anropa metoder på enheter. Du kan filtrera och sortera listan över enheter i enhetslistan. Du kan också anpassa de kolumner som visas i enhetslistan.

1. Välj **Enheter** om du vill visa listan över enheter för den här lösningen.

   ![Visa enhetslistan i lösningsportalen][img-devicelist]

1. I enhetslistan visas inledningsvis 25 simulerade enheter som har skapats vid etableringsprocessen. Du kan lägga till ytterligare simulerade och fysiska enheter till lösningen.

1. Välj en enhet i enhetslistan om du vill visa information om enheten.

   ![Visa information om enheten i lösningsportalen][img-devicedetails]

Panelen **Enhetsinformation** innehåller sex delar:

* En uppsättning länkar som gör att du kan anpassa på enhetsikonen, inaktivera enheten, lägga till en regel, anropa en metod eller skicka ett kommando. En jämförelse av kommandon (meddelanden från enheten till molnet) och metoder (direkta metoder) finns i [Cloud-to-device communications guidance][lnk-c2d-guidance] (Vägledning för kommunikation från moln till enhet).
* Avsnittet **Enhetstvilling – taggar** gör det möjligt att redigera taggvärden för enheten. Du kan visa taggvärden i enhetslistan och använda taggvärden för att filtrera enhetslistan.
* Avsnittet **Enhetstvilling – Önskade egenskaper** gör det möjligt för dig att ange egenskapsvärden som ska skickas till enheten.
* Avsnittet **Enhetstvilling – Rapporterade egenskaper** visar egenskapsvärden som skickas från enheten.
* Avsnittet **Enhetsegenskaper** visar information från identitetsregistret, till exempel enhetens ID och autentiseringsnycklar.
* Avsnittet **Senaste jobb** visar information om alla jobb som nyligen haft den här enheten som mål.

## <a name="filter-the-device-list"></a>Filtrera enhetslistan

Du kan använda ett filter om du vill visa enbart de enheter som skickar oväntade temperaturvärden. Den fjärranslutna förinställda övervakningslösningen innehåller filtret **Ej felfria enheter** som används för att visa enheter med ett medeltemperaturvärde högre än 60. Du kan även [skapa egna filter](#add-a-filter).

1. Välj **Öppna sparat filter** så visas en lista över tillgängliga filter. Välj sedan **Ej felfria enheter** för att tillämpa filtret:

    ![Visa listan över filter][img-unhealthy-filter]

1. I enhetslistan visas nu endast enheter med ett medeltemperaturvärde högre än 60.

    ![Visa listan med filtrerade enheter, där enheter med feltillstånd visas][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Uppdatera önskade egenskaper

Nu har du identifierat en uppsättning enheter som kan behöva repareras. Du anser dock att datafrekvensen på 15 sekunder inte räcker för en tydlig diagnos av problemet. Du vill ändra telemetrifrekvensen till fem sekunder så att du får fler datapunkter för att kunna diagnostisera problemet på ett bättre sätt. Du kan skicka konfigurationsändringen till din fjärranslutna enheter från lösningsportalen. Du kan göra ändringen en gång, utvärdera effekterna och sedan agera utifrån resultaten.

Följ stegen nedan för att köra ett jobb som förändrar den önskade **TelemetryInterval**-egenskapen för de berörda enheterna. När enheterna får det nya **TelemetryInterval**-egenskapsvärdet ändrar de konfigurationen så att telemetri skickas var femte sekund i stället för var 15:e sekund:

1. När du visar listan över enheter med feltillstånd i enhetslistan väljer du **Jobbschema**, och sedan **Redigera enhetstvilling**.

1. Anropa jobbet **Ändra telemetriintervall**.

1. Ändra värdet för namnet **Önskad egenskap** **desired.Config.TelemetryInterval** till fem sekunder.

1. Välj **Schema**.

    ![Ändra egenskapen TelemetryInterval till fem sekunder][img-change-interval]

1. Du kan övervaka status för det jobb som du schemalagt på sidan **Hanteringsjobb** i portalen.

> [!NOTE]
> Om du vill ändra ett önskat egenskapsvärde för en enskild enhet använder du avsnittet **Önskade egenskaper** på panelen **Enhetsinformation** i stället för att köra ett jobb.

Det här jobbet anger värdet för den önskade **TelemetryInterval**-egenskapen hos enhetstvillingen för alla enheter som valts med hjälp av filtret. Enheterna hämtar det här värdet från enhetstvillingen och uppdaterar beteendet. När en enhet hämtar och bearbetar en önskad egenskap från en enhetstvilling anger den motsvarande rapporterade värdeegenskap.

## <a name="invoke-methods"></a>Anropningsmetoder

När jobbet körs du ser i listan över enheter med feltillstånd att alla dessa enheter har gamla (tidigare än version 1.6) versioner av inbyggd programvara.

![Visa den rapporterade versionen av den inbyggda programvaran för enheter med feltillstånd][img-old-firmware]

Denna version av inbyggd programvara kan vara grundorsaken till oväntade temperaturvärden eftersom du vet att andra felfria enheter nyligen har uppdaterats till version 2.0. Du kan använda det inbyggda filtret **Old firmware devices** (Enheter med föråldrad inbyggd programvara) för att identifiera alla enheter med äldre versioner av inbyggd programvara. Från portalen kan du sedan fjärruppdatera alla enheter som fortfarande kör äldre versioner av inbyggd programvara:

1. Välj **Öppna sparat filter** så visas en lista över tillgängliga filter. Välj sedan **Old firmware devices** (Enheter med föråldrad inbyggd programvara) och tillämpa filtret:

    ![Visa listan över filter][img-old-filter]

1. I enhetslistan visas nu endast enheter med äldre versioner av inbyggd programvara. Listan innehåller fem enheter som har identifieras av filtret **Ej felfria enheter** och tre ytterligare enheter:

    ![Visa listan över filtrerade enheter där gamla enheter visas][img-filtered-old-list]

1. Välj **Jobbschema**, sedan **Anropa metod**.

1. Ange **Jobbnamn** till **Firmware update to version 2.0** (Uppdatera inbyggd programvara till version 2.0).

1. Välj **InitiateFirmwareUpdate** som **Metod**.

1. Ange parametern **FwPackageUri** till **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Välj **Schema**. Standardvärdet är att jobbet ska köras nu.

    ![Skapa jobb för att uppdatera den inbyggda programvaran för de valda enheterna][img-method-update]

> [!NOTE]
> Om du vill anropa en metod i en enskild enhet väljer du **Metoder** på panelen **Enhetsinformation** i stället för att köra ett jobb.

Det här jobbet anropar direktmetoden **InitiateFirmwareUpdate** på alla enheter som har valts med filtret. Enheterna svarar omedelbart till IoT Hub och startar sedan uppdateringsprocessen för den fasta programvaran asynkront. Enheterna visar statusinformation om uppdateringen av den fasta programvaran genom de rapporterade egenskapsvärdena, som visas på följande skärmdumpar. Välj ikonen **Uppdatera** för att uppdatera information i enhets- och jobblistorna:

![Jobblista där en lista för uppdatering av inbyggd programvara körs][img-update-1]
![Lista över enheter där uppdateringsstatus för inbyggd programvara visas][img-update-2]
![Jobblista som visar att listan över uppdatering av inbyggd programvara har slutförts][img-update-3]

> [!NOTE]
> Du kan schemalägga jobb att köras under en angiven underhållsperiod i en produktionsmiljö.

## <a name="scenario-review"></a>Scenariogranskning

I det här scenariot har du identifierat ett potentiellt problem med vissa fjärranslutna enheter m ed hjälp av larmhistoriken på instrumentpanelen och ett filter. Du använde sedan filtret och ett jobb för att fjärrkonfigurera enheterna och få fram mer information för att diagnostisera problemet. Slutligen kan använde du ett filter och ett jobb för att schemalägga underhåll av de berörda enheterna. Om du går tillbaka till instrumentpanelen kan du kontrollera att det inte längre finns några larm som kommer från enheter i din lösning. Du kan använda ett filter för att kontrollera att den inbyggda programvaran är uppdaterad på alla enheter i din lösning och att det inte finns några enheter med fel:

![Filter som visar att alla enheter som har uppdaterad inbyggd programvara][img-updated]

![Filter som visar att alla enheter är felfria][img-healthy]

## <a name="other-features"></a>Andra funktioner

I följande avsnitt beskrivs vissa ytterligare funktioner för den fjärranslutna förinställda övervakningslösning som inte beskrivs som en del av scenariot ovan.

### <a name="customize-columns"></a>Anpassa kolumner

Du kan anpassa informationen som visas i enhetslistan genom att klicka på **Kolumnredigerare**. Du kan lägga till och ta bort kolumner som visar rapporterade egenskap- och taggvärden. Du kan även ändra ordningen och byta namn på kolumner:

   ![Ikonen för kolumnredigeraren med listan över enheter][img-columneditor]

### <a name="customize-the-device-icon"></a>Anpassa enhetsikonen

Du kan anpassa enhetsikonen som visas i enhetslistan från panelen **Enhetsinformation** på följande sätt:

1. Klicka på pennikonen så öppnas panelen **Redigera avbildning** för en enhet:

   ![Öppna redigeringsprogrammet för enhetsavbildningar][img-startimageedit]

1. Ladda upp en ny avbildning eller använd en befintlig avbildning och klicka sedan på **Spara**:

   ![Redigera redigeringsprogrammet för enhetsavbildningar][img-imageedit]

1. Den valda avbildningen visas nu i kolumnen **Ikon** för enheten.

> [!NOTE]
> Avbildningen lagras i Blob Storage. En tagg i enhetstvillingen innehåller en länk till avbildning i Blob Storage.

### <a name="add-a-device"></a>Lägg till en enhet

När du distribuerar den förkonfigurerade lösningen etablerar du automatiskt 25 exempelenheter som du ser i enhetslistan. Dessa enheter är *simulerade enheter* som körs i ett Azure-webbjobb. Simulerade enheter gör det lätt att experimentera med den förkonfigurerade lösningen utan att behöva distribuera verkliga fysiska enheter. Om du vill ansluta en verklig enhet till lösningen går du självstudiekursen [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm].

Följande steg beskriver hur du lägger till en simulerad enhet i lösningen:

1. Gå tillbaka till enhetslistan.

1. Klicka på **+ Lägg till en enhet** i det nedre vänstra hörnet för att lägga till en enhet.

   ![Lägga till en enhet i den förkonfigurerade lösningen][img-adddevice]

1. Klicka på **Lägg till ny** på panelen **Simulerad enhet**.

   ![Ange information om den nya enheten på instrumentpanelen][img-addnew]

   Förutom att skapa en ny simulerad enhet kan du också lägga till en fysisk enhet om du väljer att skapa en **anpassad enhet**. Mer information om hur du ansluter fysiska enheter till lösningen finns i [Ansluta enheten till den förkonfigurerade övervakningslösningen i IoT Suite][lnk-connect-rm].

1. Välj **Låt mig ange mitt eget enhets-ID** och ange ett unikt enhets-ID, t.ex. **mydevice_01**.

1. Välj **Skapa**.

   ![Spara den nya enheten][img-definedevice]

1. I steg 3 under **Lägg till en simulerad enhet** klickar du på **Klar** för att gå tillbaka till enhetslistan.

1. Du kan se att din enhet **körs** i listan över enheter.

    ![Visa den nya enheten i enhetslistan][img-runningnew]

1. Du kan också visa den simulerade telemetrin från den nya enheten på instrumentpanelen:

    ![Visa telemetri från den nya enheten][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Inaktivera och ta bort en enhet

Du kan inaktivera en enhet och när den har inaktiverats kan du ta bort den:

![Inaktivera och ta bort en enhet][img-disable]

### <a name="add-a-rule"></a>Lägg till en regel

Det finns inga regler för den nya enheten som du har lagt till. I det här avsnittet ska du lägga till en regel som utlöser ett larm när temperaturen som rapporteras av den nya enheten överstiger 47 grader. Notera innan du börjar att telemetrihistoriken för den nya enheten på instrumentpanelen visar att enhetens temperatur aldrig överstiger 45 grader.

1. Gå tillbaka till enhetslistan.

1. Om du vill lägga till en regel för enheten väljer du den nya enheten i **enhetslistan** och klickar sedan på **Lägg till regel**.

1. Skapa en regel som använder **Temperatur** som datafält och **AlarmTemp** som utdata när temperaturen överstiger 47 grader:

    ![Lägga till en enhetsregel][img-adddevicerule]

1. Spara ändringarna genom att klicka på **Spara och visa regler**.

1. Klicka på **Kommandon** i rutan med enhetsinformation för den nya enheten.

    ![Lägga till en enhetsregel][img-adddevicerule2]

1. Välj **ChangeSetPointTemp** från kommandolistan och ange **SetPointTemp** till 45. Välj sedan **kommandot Skicka**:

    ![Lägga till en enhetsregel][img-adddevicerule3]

1. Gå tillbaka till instrumentpanelen. Efter en kort stund visas en ny post i rutan **Larmhistorik** när temperaturen som rapporteras av den nya enheten överstiger tröskelvärdet på 47 grader:

    ![Lägga till en enhetsregel][img-adddevicerule4]

1. Du kan granska och redigera alla regler på sidan **Regler** på instrumentpanelen:

    ![Visa en lista över enhetsregler][img-rules]

1. Du kan granska och redigera alla åtgärder som kan vidtas som svar på en regel på sidan **Åtgärder** på instrumentpanelen:

    ![Visa en lista över enhetsåtgärder][img-actions]

> [!NOTE]
> Du kan definiera åtgärder som kan skicka ett e-postmeddelande eller ett SMS som svar på en regel eller integrera med ett affärssystem via en [logikapp][lnk-logic-apps]. Mer information finns i [Ansluta logikapp till den förkonfigurerade fjärrövervakningslösningen i Azure IoT Suite][lnk-logicapptutorial].

### <a name="manage-filters"></a>Hantera filter

I enhetslistan kan du skapa, spara och ladda filter för att visa en anpassad enhetslista som är ansluten till hubben. Skapa ett filter:

1. Klicka på filterredigeringsikonen ovanför listan med enheter:

    ![Öppna filterredigeraren][img-editfiltericon]

1. Lägg till fält, operatorer och värden för att filtrera enhetslistan i **Filterredigeraren**. Du kan lägga till fler villkor för att förfina filtreringen. Klicka på **Filtrera** för att tillämpa filtret:

    ![Skapa ett filter][img-filtereditor]

1. I det här exemplet filtreras listan efter tillverkare och modellnummer:

    ![Filtrerad lista][img-filterelist]

1. Om du vill spara filtret med ett anpassat namn klickar du på ikonen **Spara som**:

    ![Spara ett filter][img-savefilter]

1. Om du vill återanvända ett filter som du har sparat tidigare klickar du på ikonen **Öppna sparat filter**:

    ![Öppna ett filter][img-openfilter]

Du kan skapa filter som baseras på enhets-id, enhetstillstånd, önskade egenskaper, rapporterade egenskaper och taggar. Du kan lägga till egna anpassade taggar för en enhet i avsnittet **Taggar** på panelen **Enhetsinformation** eller köra ett jobb för att uppdatera taggar på flera enheter.

> [!NOTE]
> I **Filterredigeraren** kan du använda **Avancerad vy** för att redigera frågetexten direkt.

### <a name="commands"></a>Kommandon

Du kan skicka kommandon till enheten från panelen **Enhetsinformation**. Första gången en enhet startar skickar den information om de kommandon som den stöder till lösningen. En beskrivning av skillnaderna mellan kommandon och metoder finns i [Azure IoT Hub cloud-to-device options][lnk-c2d-guidance] (Alternativ för moln-till-enhet i Azure IoT Hub).

1. Klicka på **Kommandon** på panelen **Enhetsinformation** för den valda enheten:

   ![Enhetskommandon på instrumentpanelen][img-devicecommands]

1. Välj **PingDevice** i kommandolistan.

1. Klicka på **Skicka kommando**.

1. Du kan se statusen för kommandot i kommandohistoriken.

   ![Kommandostatus på instrumentpanelen][img-pingcommand]

Lösningen spårar statusen för varje kommando som skickas. Resultatet är till en början **Väntande**. När enheten rapporterar att den har kört kommandot ändras resultatet till **Lyckades**.

## <a name="behind-the-scenes"></a>I bakgrunden

När du distribuerar en förkonfigurerad lösning skapar distributionsprocessen flera resurser i Azure-prenumerationen som du valt. Du kan visa dessa resurser på Azure-[portalen][lnk-portal]. Under distributionsprocessen skapas en **resursgrupp** med ett namn baserat på det namn som du valde för den förkonfigurerade lösningen:

![Förkonfigurerad lösning på Azure-portalen][img-portal]

Du kan visa inställningarna för varje resurs genom att välja resursen i listan över resurser i resursgruppen.

Du kan också visa källkoden för den förkonfigurerade lösningen. Källkoden för den förkonfigurerade fjärrövervakningslösningen finns i [azure-iot-remote-monitoring][lnk-rmgithub]-databasen på GitHub:

* Mappen **DeviceAdministration** innehåller källkoden för instrumentpanelen.
* Mappen **Simulator** innehåller källkoden för den simulerade enheten.
* Mappen **EventProcessor** innehåller källkoden för backend-processen som hanterar den inkommande telemetrin.

När du är klar kan du ta bort den förkonfigurerade lösningen från Azure-prenumerationen på webbplatsen [azureiotsuite.com][lnk-azureiotsuite]. På den här webbplatsen kan du enkelt ta bort alla resurser som etablerades när du skapade den förkonfigurerade lösningen.

> [!NOTE]
> För att vara säker på att du tar bort allt relaterat till den förkonfigurerade lösningen tar du bort den från [azureiotsuite.com][lnk-azureiotsuite] i stället för att ta bort resursgruppen på portalen.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en fungerande förkonfigurerad lösning kan du fortsätta och lära dig mer om IoT Suite genom att läsa följande artiklar:

* [Genomgång av den förkonfigurerade lösningen för fjärrövervakning][lnk-rm-walkthrough]
* [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm]
* [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-faq.md
