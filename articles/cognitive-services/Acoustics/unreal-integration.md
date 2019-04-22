---
title: Projektet Akustik Unreal och Wwise integrering
titlesuffix: Azure Cognitive Services
description: Den här anvisningen beskriver integrering av projektet Akustik Unreal och Wwise plugin-program i ditt projekt.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: c6baa9f8330338c1e5fdc9ee0b5a8cc8b344e871
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006497"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projektet Akustik Unreal och Wwise integrering
Den här anvisningen ger detaljerad integreringen av paketets projekt Akustik plugin-programmet till ditt befintliga Unreal och Wwise spel projekt. 

Programvarukrav:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 eller 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Wwise plugin-program för Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Om du använder en direkt integrering av Wwise SDK istället för att använda Wwise Unreal plugin-program, se projekt Akustik Unreal plugin-programmet och justera Wwise API-anrop.

Om du vill använda projekt Akustik med en ljud motor än Wwise kontaktar du oss på den [projekt Akustik forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics). Du kan använda projekt Akustik Unreal plugin-programmet för att fråga efter Akustik data och gör sedan API-anrop till din motor.

## <a name="download-project-acoustics"></a>Ladda ned projektet Akustik
Om du inte redan gjort ladda ned den [projekt Akustik Unreal & Wwise-plugin-paketet](https://www.microsoft.com/download/details.aspx?id=58090)). 

Vi har inkluderat en Unreal Engine-plugin-programmet och ett Wwise mixer plugin-program i paketet. Plugin-programmet Unreal ger redigeraren och runtime-integrering. Under spel beräknar plugin-programmet projekt Akustik Unreal parametrar, till exempel är spärrat för varje spel objekt varje ram. Dessa parametrar översätts till Wwise API-anrop.

## <a name="review-integration-steps"></a>Granska integreringen

Det finns dessa steg att installera paketet och distribuerar den i ditt spel.
1. Installera plugin-programmet för projektet Akustik Wwise mixer
2. Distribuera Wwise till ditt spel (åter). Det här steget sprider mixer-plugin-programmet i projektet spel.
3. Lägg till projektet Akustik Unreal plugin-programmet i spelet
4. Utöka funktionerna i Wwise's Unreal plugin-programmet
5. Skapa spel och kontrollera Python är aktiverad
6. Konfigurera Wwise projekt att använda projekt Akustik
7. Ljud-konfiguration i Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Installera plugin-programmet för projektet Akustik mixer
* Öppna Wwise starta sedan i den **plugin-program** fliken, under **installera nya plugin-program**väljer **Lägg till från katalog**. 

    ![Skärmbild för att installera ett plugin-program i Wwise starta](media/wwise-install-new-plugin.png)

* Välj den `AcousticsWwisePlugin\ProjectAcoustics` katalog som ingick i paketet som du hämtade. Den innehåller Wwise mixer-plugin-paketet.

* Wwise kommer att installera plugin-programmet. Projektet Akustik visas nu i listan över installerade plugin-program i Wwise.
![Skärmbild av Wwise installerat plugin-program listan efter projekt Akustik installationen](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Åter) distribuera Wwise i ditt spel
Distribuera Wwise i spelet även om du redan har integrerat Wwise. Detta hämtar projekt Akustik Wwise plugin-programmet.

* **Motorn för plugin-programmet:** Om du har Wwise installeras som ett spel plugin-program i ett Unreal C++-projekt kan du hoppa över det här steget. Om den är installerad i stället som en motor-plugin-programmet, för instansen eftersom projektet Unreal är skissen endast, Wwise-distribution med vår mixer-plugin-programmet är mer komplexa. Skapa ett dummy, tom Unreal C++-projekt, Stäng den om Unreal redigerare öppnas och följ de återstående stegen för att distribuera Wwise till dummy projektet. Kopiera sedan ut distribuerade Wwise plugin-programmet.
 
* Från Wwise starta klickar du på den **Unreal Engine** och klicka sedan på hamburgarmenyn bredvid **senaste Unreal Engine projekt** och välj **Bläddra efter projekt**. Öppna ditt spel Unreal projekt `.uproject` fil.

    ![Skärmbild av Wwise hanterar Unreal fliken](media/wwise-unreal-tab.png)

* Klicka sedan på **integrera Wwise i projektet** eller **ändra Wwise i projektet**. Det här steget (åter) integreras Wwise binärfiler i ditt projekt, inklusive nu projekt Akustik mixer-plugin-programmet.

* **Motorn för plugin-programmet:** Om du använder Wwise som en motor för plugin-programmet och skapat dummy-projekt som ovan, kopiera mappen Wwise distribueras: `[DummyUProject]\Plugins\Wwise` och klistra in det över `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` är den tom Unreal C++ projektsökvägen och `[UESource]` är när du har Unreal Engine-källor som installerats. När du är klar kopierar, kan du ta bort dummy-projekt.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Lägg till projektet Akustik Unreal plugin-programmet i spelet
 
* Kopiera den `Unreal\ProjectAcoustics` mapp i plugin-programmet paketera och skapa en ny mapp `[UProjectDir]\Plugins\ProjectAcoustics`, där `UProjectDir` är ditt spel projektmapp som innehåller den `.uproject` filen.
  * **Motorn för plugin-programmet**: Om du använder Wwise som en motor för plugin-programmet, använder du projektet Akustik som en Unreal engine-plugin-programmet även. Istället för att ovanstående målkatalogen: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Bekräfta att du ser en `Wwise` mappen tillsammans med den `ProjectAcoustics` mapp. Den innehåller Wwise plugin-programmet tillsammans med binärfilerna för mixer-plugin-programmet som du (re-) distribuerade i steg 2 ovan.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Utöka funktionerna i Wwise's Unreal plugin-programmet
* Projektet Akustik Unreal plugin-programmet kräver ytterligare beteenden exponeras från Wwise Unreal plugin-programmet API per [dessa riktlinjer](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Vi har inkluderat en kommandofil för att automatisera uppdatering proceduren. 
* Inuti `Plugins\ProjectAcoustics\Resources`kör `PatchWwise.bat`. På bilden nedan använder vårt AcousticsGame exempelprojektet.

    ![Skärmbild av Windows Explorer-fönstret markering tillhandahålls skriptet att korrigera Wwise](media/patch-wwise-script.png)

* Om du inte har DirectX-SDK är installerat, måste du kommentera ut den rad som innehåller DXSDK_DIR i `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Skärmbild av Kodredigerare som visar DXSDK utkommenterad](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Skapa spel och kontrollera Python är aktiverad

* Kompilera ditt spel och se till att den bygger på rätt sätt. I annat fall söker i föregående steg noggrant innan du fortsätter. 
* Öppna projektet i Unreal Editor. 
* **Motorn för plugin-programmet:** Om du använder ProjectAcoustics as engine-plugin-programmet, se också till att den är aktiverad, visas under ”inbyggd” plugin-program.
* Du bör se en ny läge, vilket anger att projektet Akustik har integrerats.

    ![Skärmbild av Unreal som visar Akustik läge fullständig](media/acoustics-mode-full.png)

* Bekräfta att du har Python-plugin-programmet för Unreal aktiverat. Detta krävs för redigeraren integrationen ska fungera korrekt.

    ![Skärmbild över att aktivera Python-tillägg i Unreal redigeraren](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Wwise projektkonfiguration

Ett exempelprojekt Wwise ingår exempel nedladdningen. Vi rekommenderar att du tittar på det tillsammans med de här anvisningarna. Skärmbilderna nedan är hämtade från det här projektet.

### <a name="bus-setup"></a>Bus installationen
* Projektet Akustik Unreal plugin-programmet söker efter associerade mixer-plugin-programmet på en buss med den här ***exakta*** namn: `Project Acoustics Bus`. Skapa en ny ljud bus med det här namnet. Mixer-plugin-programmet fungerar i olika konfigurationer, men nu antar vi kommer att användas för att endast eko bearbetning. Den här bus har blandat eko signalen för alla datakällor som använder Akustik. Det kan blanda uppströms till alla bus blanda struktur, visas ett exempel nedan, kommer från våra Wwise exempelprojektet som ingår i exemplet nedladdningen.

    ![Skärmbild av Wwise bussar som visar projekt Akustik Bus](media/acoustics-bus.png)

* Kanalkonfiguration på bussen måste anges till något av: `1.0, 2.0, 4.0, 5.1 or 7.1`. Andra konfigurationer medför inga utdata på den här bussen.

    ![Skärmbild av kanal konfigurationsalternativ för projektet Akustik Bus](media/acoustics-bus-channel-config.png)

* Gå nu till projektet Akustik bus information och se till att du kan se fliken Mixer plugin-programmet

    ![Skärmbild av Wwise som visar hur du aktiverar fliken Mixer plugin-programmet för projektet Akustik Bus](media/mixer-tab-enable.png)

* Sedan går du till fliken Mixer plugin-programmet och lägga till plugin-programmet för projektet Akustik mixer till bussen

    ![Screenshow Wwise bus som visar hur du lägger till projektet Akustik Mixer-plugin-programmet](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Installationsprogrammet för aktören mixer-hierarki
* Av prestandaskäl gäller projekt Akustik ljud DSP till alla samtidigt. Detta kräver plugin-programmet att fungera som ett mixer-plugin-program. Wwise kräver mixer-plugin-program på utdata-bus, även om utdata-bus innehåller vanligen signalen torr utdata. Projektet Akustik kräver torr signalen dirigeras via aux bussar medan våt signalen utförs den `Project Acoustics Bus`. Gradvis migrering till den här signalen flow har stöd för följande process.

* Anta att du har ett befintligt projekt med en aktör mixer-hierarki som innehåller fotsteg och vapen på högsta nivån. Var och en har motsvarande utdata-bussen för dess påse. Kan anta att du vill migrera fotsteg om du vill använda Akustik. Skapa först en motsvarande aux bus för att utföra sina torr submix som är underordnad fotsteg utdata bus. Exempelvis kan har vi använt ett ”Dry” prefix i bilden nedan för att ordna dem, även om det exakta namnet är inte viktigt. Eventuella mätare och effekter som du hade på bussen fotsteg fortsätter att fungera som tidigare.

    ![Skärmbild av rekommenderade Wwise torr blandning installationen](media/wwise-dry-mix-setup.png)

* Ändra bus utdata strukturen för fotsteg aktör-mixer på följande sätt, med projekt Akustik Bus utdata Bus och Dry_Footsteps Ställ in som en användardefinierad aux-buss.

    ![Skärmbild av rekommenderade Wwise aktören Mixer Bus installationen](media/actor-mixer-bus-settings.png)

* Nu alla fotsteg får Akustik behandling och mata ut sina eko på Akustik-bussen för projektet. Torr signalen dirigeras via Dry_Footsteps och spatialized som vanligt.

* Projektet Akustik gäller endast ljud som har en 3D-plats i världen. Följa [Wwise dokumentation](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), placeringsegenskaper måste anges som visas. Inställningen ”3D Spatialization” kan vara antingen ”Position” eller ”Position + orientering” efter behov.

    ![Skärmbild av rekommenderade Wwise aktören placering inställningar](media/wwise-positioning.png)

* Vissa bus där en uppströms till att ställa in en utdata-Bus **projekt Akustik Bus** fungerar inte. Wwise inför det här kravet på mixer-plugin-program.

* Om du vill att en underordnad i hierarkin fotsteg aktören mixer att inte använda Akustik kan du alltid använda ”åsidosätta överordnade” på den för att avanmäla dig.

* Om du använder spel eller användardefinierade skickar för eko på alla aktören mixer i spelet, inaktivera dem på den här aktören mixer att undvika att tillämpa eko två gånger.

### <a name="spatialization"></a>Spatialization
Som standard tillämpar plugin-programmet för projektet Akustik Wwise mixer Faltning eko, lämna Wwise om du vill göra panoreringsverktyget spatialization. När projektet Akustik i den här standardkonfigurationen för endast eko, är du kan använda valfri kanal konfiguration och spatialization metod på din påse så att du kan blanda och matcha nästan alla spatializer med projekt Akustik eko. Du kan välja mellan [Ambisonics-baserade binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) och [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Projektet Akustik innehåller ett valfritt spatializer som stöder både objektbaserad högupplösta HRTF rendering och panorering. Markera kryssrutan ”utföra Spatialization” mixer-plugin-programmet inställningar, och välja mellan HRTF eller panorering och inaktivera användardefinierade aux skickar som angetts ovan till alla torr bussar att undvika spatializing två gånger, både med projekt Akustik mixer-plugin-programmet och Wwise. Spatialization-läge kan inte ändras i realtid, eftersom den kräver en bra bank återskapas. Du måste starta om Unreal och sedan återskapa ljudbank innan du stöter på play för att hämta mixer-plugin-programmet config ändringar, till exempel utföra Spatialization kryssrutan.

![Skärmbild av Wwise Mixer-plugin-programmet Spatialization inställningar](media/mixer-spatial-settings.png)

Tyvärr stöds andra objektbaserad spatializer plugin-program inte just nu eftersom de implementeras som mixer-plugin-program och Wwise för närvarande tillåter flera mixer plugin-program tilldelade till en enda aktören mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Ljud-konfiguration i Unreal
* Först måste du skapa ditt spel nivå för att skapa en Akustik tillgång, som kommer att placeras i `Content\Acoustics`. Läs den [Unreal skapa självstudien](unreal-baking.md) och återuppta här. Vissa före bakade nivåer ingår i exempelpaketet.
* Skapa en aktör Akustik utrymme i din scen. Bara skapa en av dessa aktörer i en nivå som representerar Akustik för hela nivå. 

    ![Skärmbild av Unreal redigerare som visar skapandet av Akustik utrymme aktör](media/create-acoustics-space.png)

* Nu tilldela bakade akustiska datatillgången till facket Akustik Data hos aktören för Akustik utrymme. Din scen har nu Akustik!

    ![Skärmbild av Unreal redigeraren s howing Akustik tillgången tilldelning](media/acoustics-asset-assign.png)

* Lägg till en tom aktör och gör följande:

    ![Skärmbild av Unreal redigerare som visar Akustik komponenten användning i en tom aktör](media/acoustics-component-usage.png)

1. Lägga till komponenten Akustik ljud till aktören. Den här komponenten utökar komponenten Wwise ljud med funktioner för projektet Akustik.
2. Play på Start-ruta är markerad som standard, vilket utlöser den associerade Wwise händelsen på startskripet.
3. Använd kryssrutan Visa Akustik parametrar för att skriva ut anvisningarna på skärmen felsökningsinformation om källan.
    ![Skärmbild av Unreal redigeraren Akustik panelen för ljudkälla med debug-värden som aktiverats](media/debug-values.png)
4. Tilldela en Wwise händelse per vanligt Wwise arbetsflöde
5. Se till att använda Spatial ljud är avstängd. Just nu, om du använder projekt Akustik för en viss komponent ljud, kan inte du samtidigt använda Wwise's Spatial ljud motor för Akustik.

Då var allt klart. Flytta runt scenen och utforska akustiska effekter!

## <a name="next-steps"></a>Nästa steg
* [Design](unreal-workflow.md) självstudierna projekt Akustik i Unreal/Wwise
* [Lär dig hur du gör bakes](unreal-baking.md) för dina spel scener 
