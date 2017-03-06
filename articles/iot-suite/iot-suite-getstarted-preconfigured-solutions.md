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
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Självstudiekurs: Komma igång med förkonfigurerade lösningar
## <a name="introduction"></a>Introduktion
Azure IoT Suites [förkonfigurerade lösningar][lnk-preconfigured-solutions] kombinerar flera Azure IoT-tjänster för att leverera lösningar från slutpunkt till slutpunkt som implementerar vanliga IoT-företagsscenarier. Med den förkonfigurerade *fjärrövervakningslösningen* kan du ansluta till och övervaka dina enheter. Du kan använda lösningen för att analysera dataströmmen från dina enheter och för att förbättra affärsresultat genom att konfigurera processer så att de svarar automatiskt på dataströmmen.

I den här självstudiekursen lär du dig hur du etablerar den förkonfigurerade lösningen för fjärrövervakning. Vi går också igenom de grundläggande funktionerna i den förkonfigurerade lösningen. Du kan komma åt många av dessa funktioner från instrumentpanelen för lösningen som distribueras som en del av den förkonfigurerade lösningen:

![Instrumentpanel för den förkonfigurerade fjärrövervakningslösningen][img-dashboard]

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

> [!NOTE]
> Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Visa instrumentpanelen för lösningen
På instrumentpanelen för lösningen kan du hantera den distribuerade lösningen. Du kan till exempel visa telemetri, lägga till enheter och konfigurera regler.

1. När etableringen har slutförts och panelen för din förkonfigurerade lösning visar statusen **Klar** klickar du på **Starta** för att öppna portalen för fjärrövervakningslösningen på en ny flik.
   
   ![Starta den förkonfigurerade lösningen][img-launch-solution]
2. Som standard visar lösningsportalen *instrumentpanelen för lösningen*. Du kan välja andra vyer från menyn till vänster.
   
   ![Instrumentpanel för den förkonfigurerade fjärrövervakningslösningen][img-dashboard]

Följande information visas på instrumentpanelen:

* Kartan visar platsen för alla enheter som är kopplade till lösningen. Första gången du kör lösningen finns det fyra simulerade enheter. De simulerade enheterna implementeras som Azure WebJobs och lösningen använder Bing Maps-API:et för att rita information på kartan.
* Panelen **Telemetrihistorik** ritar upp fuktighets- och temperaturtelemetri från en vald enhet nästan i realtid och visar aggregerade data, till exempel högsta, lägsta och genomsnittlig fuktighet.
* Panelen **Larmhistorik** visar de senaste larmhändelserna när ett telemetrivärde har överskridit ett tröskelvärde. Du kan definiera dina egna larm förutom exemplen som skapas med den förkonfigurerade lösningen.
* Panelen **Jobb** visar information om schemalagda jobb. Du kan schemalägga egna jobb på sidan **Hanteringsjobb**.

## <a name="view-the-device-list"></a>Visa enhetslistan
*Enhetslistan* visar alla registrerade enheter i lösningen. Från enhetslistan kan du se och redigera enhetsmetadata, lägga till eller ta bort enheter och anropa metoder på enheter.

1. Klicka på **Enheter** i den vänstra menyn för att visa enhetslistan för den här lösningen.
   
   ![Enhetslista på instrumentpanelen][img-devicelist]
2. Enhetslistan visar inledningsvis fyra simulerade enheter som skapats av etableringsprocessen. Du kan lägga till ytterligare simulerade och fysiska enheter till lösningen.
3. Du kan anpassa informationen som visas i enhetslistan genom att klicka på **Kolumnredigerare**. Du kan lägga till och ta bort kolumner som visar rapporterade egenskap- och taggvärden. Du kan även ändra ordningen och byta namn på kolumner:
   
   ![Kolumnredigeraren i instrumentpanelen][img-columneditor]
4. Klicka på en enhet i enhetslistan för att visa information om enheten.
   
   ![Enhetsinformation på instrumentpanelen][img-devicedetails]

Panelen **Enhetsinformation** innehåller sex delar:

* En uppsättning länkar som gör att du kan anpassa på enhetsikonen, inaktivera enheten, lägga till en regel, anropa en metod eller skicka ett kommando. En jämförelse av kommandon (meddelanden från enheten till molnet) och metoder (direkta metoder) finns i [Cloud-to-device communications guidance][lnk-c2d-guidance] (Vägledning för kommunikation från moln till enhet).
* Avsnittet **Enhetstvilling – taggar** gör det möjligt att redigera taggvärden för enheten. Du kan visa taggvärden i enhetslistan och använda taggvärden för att filtrera enhetslistan.
* Avsnittet **Enhetstvilling – Önskade egenskaper** gör det möjligt för dig att ange egenskapsvärden som ska skickas till enheten.
* Avsnittet **Enhetstvilling – Rapporterade egenskaper** visar egenskapsvärden som skickas från enheten.
* Avsnittet **Enhetsegenskaper** visar information från identitetsregistret, till exempel enhetens ID och autentiseringsnycklar.
* Avsnittet **Senaste jobb** visar information om alla jobb som nyligen haft den här enheten som mål.

## <a name="customize-the-device-icon"></a>Anpassa enhetsikonen

Du kan anpassa enhetsikonen som visas i enhetslistan från panelen **Enhetsinformation** på följande sätt:

1. Klicka på pennikonen för att öppna panelen **Redigera avbildning** för en enhet:
   
   ![Öppna redigeringsprogrammet för enhetsavbildningar][img-startimageedit]
2. Överför en ny avbildning eller använd en befintlig avbildning och klicka sedan på **Spara**:
   
   ![Redigera redigeringsprogrammet för enhetsavbildningar][img-imageedit]
3. Den valda avbildningen visas nu i kolumnen **Ikon** för enheten.

> [!NOTE]
> Avbildningen lagras i Blob Storage. En tagg i enhetstvillingen innehåller en länk till avbildning i Blob Storage.
> 
> 

## <a name="invoke-a-method-on-a-device"></a>Anropa en metod på en enhet
Du kan anropa metoder på enheten från panelen **Enhetsinformation**. Första gången en enhet startar skickar den information om de metoder som den stöder till lösningen.

1. Klicka på **Metoder** på panelen **Enhetsinformation** för den valda enheten:
   
   ![Enhetsmetoder på instrumentpanelen][img-devicemethods]
2. Välj **Omstart** i metodlistan.
3. Klicka på **Anropa metod**.
4. Du kan se statusen för anropet av metoden i metodhistoriken.
   
   ![Metodstatus på instrumentpanelen][img-pingmethod]

Lösningen spårar statusen för varje metod den anropar. När enheten är färdig med metoden ser du en ny post i tabellen över metodhistorik.

Vissa metoder startar asynkrona jobb på enheten. Metoden **InitiateFirmwareUpdate** startar till exempel en asynkron uppgift för att utföra uppdateringen. Enheten använder rapporterade egenskaper för att rapportera om statusen för uppdateringen av den inbyggda programvaran under tiden den pågår.

## <a name="send-a-command-to-a-device"></a>Skicka ett kommando till en enhet
Du kan skicka kommandon till enheten från panelen **Enhetsinformation**. Första gången en enhet startar skickar den information om de kommandon som den stöder till lösningen.

1. Klicka på **Kommandon** på panelen **Enhetsinformation** för den valda enheten:
   
   ![Enhetskommandon på instrumentpanelen][img-devicecommands]
2. Välj **PingDevice** i kommandolistan.
3. Klicka på **Skicka kommando**.
4. Du kan se statusen för kommandot i kommandohistoriken.
   
   ![Kommandostatus på instrumentpanelen][img-pingcommand]

Lösningen spårar statusen för varje kommando som skickas. Resultatet är till en början **Väntande**. När enheten rapporterar att den har kört kommandot ändras resultatet till **Lyckades**.

## <a name="add-a-new-simulated-device"></a>Lägga till en ny simulerad enhet
När du distribuerar den förkonfigurerade lösningen etablerar du automatiskt fyra exempelenheter som du ser i enhetslistan. Dessa enheter är *simulerade enheter* som körs i ett Azure-webbjobb. Simulerade enheter gör det lätt att experimentera med den förkonfigurerade lösningen utan att behöva distribuera verkliga fysiska enheter. Om du vill ansluta en verklig enhet till lösningen går du självstudiekursen [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm].

Följande steg beskriver hur du lägger till en simulerad enhet i lösningen:

1. Gå tillbaka till enhetslistan.
2. Klicka på **+ Lägg till en enhet** i det nedre vänstra hörnet för att lägga till en ny enhet.
   
   ![Lägga till en enhet i den förkonfigurerade lösningen][img-adddevice]
3. Klicka på **Lägg till ny** på panelen **Simulerad enhet**.
   
   ![Ange information om den nya enheten på instrumentpanelen][img-addnew]
   
   Förutom att skapa en ny simulerad enhet kan du också lägga till en fysisk enhet om du väljer att skapa en **anpassad enhet**. Mer information om hur du ansluter fysiska enheter till lösningen finns i [Ansluta enheten till den förkonfigurerade övervakningslösningen i IoT Suite][lnk-connect-rm].
4. Välj **Låt mig ange mitt eget enhets-ID** och ange ett unikt enhets-ID, t.ex. **mydevice_01**.
5. Klicka på **Skapa**.
   
   ![Spara den nya enheten][img-definedevice]
6. I steg 3 i **Lägga till en simulerad enhet** klickar du på **Klar** för att gå tillbaka till enhetslistan.
7. Du kan se att din enhet **körs** i listan över enheter.
   
    ![Visa den nya enheten i enhetslistan][img-runningnew]
8. Du kan också visa den simulerade telemetrin från den nya enheten på instrumentpanelen:
   
    ![Visa telemetri från den nya enheten][img-runningnew-2]

## <a name="device-properties"></a>Enhetsegenskaper
Den förkonfigurerade lösningen för fjärrövervakning använder [enhetstvillingar][lnk-device-twin] för att synkronisera enhetsmetadata mellan enheter och lösningens backend-servrar. En enhetstvilling är ett JSON-dokument som lagras i IoT Hub som lagrar egenskapsvärden för en enskild enhet. Enheterna skickar regelbundet metadata till lösningens backend-server som *rapporterade egenskaper* som sedan lagras i enhetstvillingen. Lösningens backend-server kan ställa in *önskade egenskaper* i enhetstvillingen för att skicka metadatauppdateringar till enheter. De rapporterade egenskaperna visar de senaste metadatavärdena som skickats av enheten. Mer information finns i [Enhetsidentitetsregistret, enhetstvilling och DocumentDB][lnk-devicemetadata].

> [!NOTE]
> Lösningen använder även DocumentDB-databasen för att lagra enhetsspecifika data som rör kommandon och metoder.
> 
> 

1. Gå tillbaka till enhetslistan.
2. Välj den nya enheten i **enhetslistan** och klicka sedan på **Redigera** för att redigera **Enhetstvilling – Önskade egenskaper**:
   
   ![Redigera önskade egenskaper för enheter][img-editdevice]
3. Ange **Önskat egenskapsnamn** till **Latitud** och ange värdet till **47.639521**. Klicka sedan på **Spara ändringar i enhetsregistret**:
   
    ![Redigera önskad egenskap för enheter][img-editdevice2]
4. I panelen **enhetsinformation** visas det nya latitudvärdet inledningsvis som en önskad egenskap och det gamla latitudvärdet som en rapporterade egenskap:
   
    ![Visa rapporterad egenskap][img-editdevice3]
5. Simulerade enheter i den förinställda lösningen processar för närvarande endast de önskade egenskaperna **Desired.Config.TemperatureMeanValue** och **Desired.Config.TelemetryInterval**. En riktig enhet bör läsa alla önskade egenskaper från IoT Hub, göra ändringar på dess konfiguration och rapportera de nya värdena till hubben som rapporterade egenskaper.

På panelen **Enhetsinformation** kan du även redigera **Enhetstvilling - Taggar** på samma sätt som du redigerar **Enhetstvilling – Önskade egenskaper**. Men till skillnad från önskade egenskaper synkroniseras inte taggar med enheten. Taggar finns bara i enhetstvillingen i IoT Hub. Taggar är användbara för att skapa anpassade filter i enhetslistan.

## <a name="sort-the-device-list"></a>Sortera enhetslistan

Du kan sortera enhetslistan genom att klicka bredvid en kolumnrubrik. Första gången du klickar sorteras listan i stigande ordning, andra gången sorteras den i fallande ordning:

![Sortera enhetslista][img-sortdevices]

## <a name="filter-the-device-list"></a>Filtrera enhetslistan

I enhetslistan kan du skapa, spara och ladda filter för att visa en anpassad enhetslista som är ansluten till hubben. Skapa ett filter:

1. Klicka på redigera filter-ikonen ovanför listan med enheter:
   
   ![Öppna filterredigeraren][img-editfiltericon]
2. Lägg till fält, operatorer och värden för att filtrera enhetslistan i **Filterredigeraren**. Du kan lägga till fler villkor för att förfina filtreringen. Klicka på **Filtrera** för att tillämpa filtret:
   
   ![Skapa ett filter][img-filtereditor]
3. I det här exemplet filtreras listan efter tillverkare och modellnummer:
   
   ![Filtrerad lista][img-filterelist]
4. Om du vill spara filtret med ett anpassat namn klickar du på den ikonen **Spara som**:
   
   ![Spara ett filter][img-savefilter]
5. Om du vill återanvända ett filter som du sparade tidigare klickar du på ikonen **Öppna sparat filter**:
   
   ![Öppna ett filter][img-openfilter]

Du kan skapa filter som baseras på enhets-id, enhetstillstånd, önskade egenskaper, rapporterade egenskaper och taggar.

> [!NOTE]
> I **Filterredigeraren** kan du använda **Avancerad vy** för att redigera frågetexten direkt.
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>Lägga till en regel för den nya enheten
Det finns inga regler för den nya enheten som du har lagt till. I det här avsnittet ska du lägga till en regel som utlöser ett larm när temperaturen som rapporteras av den nya enheten överstiger 47 grader. Notera innan du börjar att telemetrihistoriken för den nya enheten på instrumentpanelen visar att enhetens temperatur aldrig överstiger 45 grader.

1. Gå tillbaka till enhetslistan.
2. För att lägga till en regel till enheten väljer du den nya enheten i **enhetslistan** och klicka sedan på **Lägg till regel**.
3. Skapa en regel som använder **Temperatur** som datafält och **AlarmTemp** som utdata när temperaturen överstiger 47 grader:
   
    ![Lägga till en enhetsregel][img-adddevicerule]
4. Spara ändringarna genom att klicka på **Spara och visa regler**.
5. Klicka på **Kommandon** i rutan med enhetsinformation för den nya enheten.
   
   ![Lägga till en enhetsregel][img-adddevicerule2]
6. Välj **ChangeSetPointTemp** från kommandolistan och ange **SetPointTemp** till 45. Klicka på **Skicka kommando**:
   
   ![Lägga till en enhetsregel][img-adddevicerule3]
7. Gå tillbaka till instrumentpanelen för lösningen. Efter en kort stund visas en ny post i rutan **Larmhistorik** när temperaturen som rapporteras av den nya enheten överstiger tröskelvärdet på 47 grader:
   
   ![Lägga till en enhetsregel][img-adddevicerule4]
8. Du kan granska och redigera alla regler på sidan **Regler** på instrumentpanelen:
   
    ![Visa en lista över enhetsregler][img-rules]
9. Du kan granska och redigera alla åtgärder som kan vidtas som svar på en regel på sidan **Åtgärder** på instrumentpanelen:
   
    ![Visa en lista över enhetsåtgärder][img-actions]

> [!NOTE]
> Du kan definiera åtgärder som kan skicka ett e-postmeddelande eller ett SMS som svar på en regel eller integrera med ett affärssystem via en [logikapp][lnk-logic-apps]. Mer information finns i [Ansluta logikapp till den förkonfigurerade fjärrövervakningslösningen i Azure IoT Suite][lnk-logicapptutorial].
> 
> 

## <a name="disable-and-remove-devices"></a>Inaktivera och ta bort enheter
Du kan inaktivera en enhet och när den har inaktiverats kan du ta bort den:

![Inaktivera och ta bort en enhet][img-disable]

## <a name="run-jobs"></a>Kör jobb
Du kan schemalägga jobb för att utföra massåtgärder på dina enheter. Du skapar ett jobb för en enhetslista. Den här listan kan innehålla alla enheter eller vara en filtrerad lista som du skapat med [filterverktygen](#filter-the-device-list) i enhetslistan. Ett jobb kan anropa en metod på varje enhet i listan eller uppdatera enhetstvillingen för varje enhet i enhetslistan.

### <a name="create-a-job-to-invoke-a-method"></a>Skapa ett jobb för att anropa en metod

Följande steg visar hur du skapar ett jobb som anropar metoden för att uppdatera den inbyggda programvaran på varje enhet i en lista. Metoden anropas endast på enheter som har stöd för metoden:

1. Använd filterverktygen i enhetslistan för att skapa en lista över enheter som ska ta emot uppdateringen för den inbyggda programvaran:
   
   ![Öppna filterredigeraren][img-editfiltericon]
2. Klicka på **Jobbscheman** i den filtrerade listan:
   
   ![Öppna jobbschemat][img-clickjobscheduler]
3. På panelen **Schemalägg jobb** klickar du på **Anropa metod**.
4. På sidan **Anropa metod** anger du sedan information om metoden du vill anropa och klickar sedan på **Schemalägg**:
   
   ![Konfigurera jobbmetoden][img-invokemethodjob]

Metoden **InitiateFirmwareUpdate** startar en aktivitet asynkront på enheten och returnerar omedelbart. Uppdateringsprocessen för den inbyggda programvaran använder sedan rapporterade egenskaper för att rapportera om statusen för uppdateringsprocessen under tiden den pågår.

### <a name="create-a-job-to-edit-the-device-twin"></a>Skapa ett jobb för att redigera enhetstvillingen

Följande steg visar hur du skapar ett jobb som redigerar enhetstvillingen på varje enhet i en lista:

1. Använd filterverktygen i enhetslistan för att skapa en lista över enheter som ska ta emot redigeringar av enhetstvillingar:
   
   ![Öppna filterredigeraren][img-editfiltericon]
2. Klicka på **Jobbscheman** i den filtrerade listan:
   
   ![Öppna jobbschemat][img-clickjobscheduler]
3. På panelen **Schemalägg jobb** klickar du på **Redigera enhetstvilling**.
4. På sidan **Redigera enhetstvilling** anger du information om de **Önskade egenskaperna** och **Taggarna** du vill redigera och klickar sedan på **Schema**:
   
   ![Konfigurera jobbmetoden][img-edittwinjob]

### <a name="monitor-the-job"></a>Övervaka jobbet
Du kan övervaka status för jobb som du schemalagt på sidan **Hanteringsjobb**. Panelen **Jobbinformation** visar information om det valda jobbet:
   
   ![Visa jobbstatus][img-jobstatus]

Du kan även se information om jobb i **Instrumentpanelen**:
   
   ![Visa jobb i instrumentpanelen][img-jobdashboard]


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
> 
> 

## <a name="next-steps"></a>Nästa steg
Nu när du har distribuerat en fungerande förkonfigurerad lösning kan du fortsätta och lära dig mer om IoT Suite genom att läsa följande artiklar:

* [Genomgång av den förkonfigurerade lösningen för fjärrövervakning][lnk-rm-walkthrough]
* [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm]
* [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
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
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
