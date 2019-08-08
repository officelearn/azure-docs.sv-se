---
title: Project-akustiskt Unreal och Wwise-integrering
titlesuffix: Azure Cognitive Services
description: Den här instruktionen beskriver integrering av projektet akustiskt Unreal och Wwise-plugin-program i ditt projekt.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 3fe9a28a99ea8becbfc40e1e64d1f5b109caace3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854374"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project-akustiskt Unreal och Wwise-integrering
Den här instruktionen innehåller detaljerade integrerings steg för plugin-paketet för Project Akustiske-plugin-program i ditt befintliga Unreal-och Wwise Game-projekt. 

Program varu krav:
* [Unreal-motor](https://www.unrealengine.com/) 4,20 eller 4,21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1.\*
* [Wwise-plugin-program för Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Om du använder en direkt integrering av Wwise SDK i stället för att använda Wwise Unreal-plugin-programmet, kan du läsa mer i Project Akustiske Unreal-plugin-programmet och justera Wwise API-anrop.

Om du vill använda projekt Akustisker med en annan ljud motor än Wwise gör du en förbättrings förfrågan i diskussions [forumet för projekt akustiskt](https://github.com/microsoft/ProjectAcoustics/issues). Du kan använda plugin-programmet för Project akustiskt Unreal för att fråga akustiska data och sedan göra API-anrop till din motor.

## <a name="download-project-acoustics"></a>Ladda ned projekt akustiska
Om du inte redan har gjort det kan du ladda ned [projektet akustiskt Unreal & Wwise plugin-paketet](https://www.microsoft.com/download/details.aspx?id=58090)). 

Vi har inkluderat ett Unreal motor-plugin-program och ett Wwise-mixer-plugin-program i paketet. Unreal-plugin-programmet ger redigerings-och körnings integration. Under spelets upplevelse beräknar projektet akustiskt Unreal-plugin parametrar som ocklusion för varje spel objekt varje bild ruta. Dessa parametrar översätts till Wwise-API-anrop.

## <a name="review-integration-steps"></a>Granska integrerings steg

Det finns viktiga steg för att installera paketet och distribuera det i spelet.
1. Installera plugin-programmet för Project Akustisker Wwise-mixer
2. (Re) distribuera Wwise till ditt spel. Det här steget sprider mixer-plugin-programmet till spel projektet.
3. Lägg till projektet Akustisker Unreal-plugin-programmet i spelet
4. Utöka Wwise-funktioner för Unreal-plugin
5. Utveckla spel och kontrol lera python är aktiverat
6. Konfigurera ditt Wwise-projekt för att använda projekt akustiska
7. Ljud konfiguration i Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Installera plugin-programmet för Project akustiska mixer
* Öppna Wwise start och välj **Lägg till från katalog**under **installera nya plugin**-program på fliken **plugin** -program. 

    ![Skärm bild av installation av ett plugin-program i Wwise Launcher](media/wwise-install-new-plugin.png)

* Välj den `AcousticsWwisePlugin\ProjectAcoustics` katalog som ingår i paketet som du laddade ned. Den innehåller Wwise mixer plugin-paketet.

* Wwise kommer att installera plugin-programmet. Project-Akustisker bör nu visas i listan installerade plugin-program i Wwise.
![Skärm bild av Wwise installerade plugin-program efter projekt akustiskt installation](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re) distribuera Wwise till ditt spel
Distribuera Wwise på nytt till spelet även om du redan har integrerat Wwise. Detta hämtar Wwise-plugin-programmet för Project-Akustisker.

* **Motorns plugin-program:** Om du har Wwise installerat som ett spel-plugin-program C++ i ett Unreal-projekt hoppar du över det här steget. Om det är installerat i stället för ett motor-plugin-program, till exempel eftersom ditt Unreal-projekt bara är skiss, är Wwise-distributionen med vårt mixer-plugin-program mer komplex. Skapa en dummy, Tom Unreal C++ -projekt, Stäng den om Unreal-redigeraren öppnas och följ den återstående proceduren för att distribuera Wwise i detta dummy-projekt. Kopiera sedan det distribuerade Wwise-plugin-programmet.
 
* Från Wwise Launcher klickar du på fliken **Unreal motor** och klickar sedan på Hamburger-menyn bredvid de **senaste Unreal-motorns projekt** och väljer **Bläddra efter projekt**. Öppna spelets Unreal-projekt `.uproject` fil.

    ![Skärm bild av Wwise Launcher-fliken Unreal](media/wwise-unreal-tab.png)

* Klicka sedan på **integrera Wwise i projektet** eller **ändra Wwise i projektet**. Det här steget (åter) integrerar Wwise-binärfiler i ditt projekt, och kan nu ta med i projektet akustiska mixer-plugin.

* **Motorns plugin-program:** Om du använder Wwise som motor-plugin och skapat provdocka-projektet som ovan, kopierar du mappen Wwise distribuerad: `[DummyUProject]\Plugins\Wwise` och klistrar in den över. `[UESource]\Engine\Plugins\Wwise` `[DummyUProject]`är den tomma Unreal C++ -projekt Sök vägen `[UESource]` och är där Unreal-motor källorna är installerade. När du är klar med att kopiera kan du ta bort provdocka projektet.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Lägg till projektet Akustisker Unreal-plugin-programmet i spelet
 
* Kopiera mappen i plugin-paketet och skapa en ny mapp `[UProjectDir]\Plugins\ProjectAcoustics`, där `UProjectDir` `.uproject` är spelets projektmapp som innehåller filen. `Unreal\ProjectAcoustics`
  * **Motorns plugin-program**: Om du använder Wwise som ett motor-plugin-program, bör du även använda Project-Akustisker som ett Unreal-motorprogram. I stället för ovanstående mål katalog använder du: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Bekräfta att du ser `Wwise` en mapp `ProjectAcoustics` bredvid mappen. Den innehåller Wwise-plugin-programmet tillsammans med binärfiler för det mixer-plugin-program som du (återinstallerade i steg 2 ovan).

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Utöka Wwise-funktioner för Unreal-plugin
* Unreal-plugin-programmet för Project-Akustisker kräver att ytterligare beteenden exponeras från Wwise Unreal plugin API enligt [dessa rikt linjer](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Vi har inkluderat en kommando fil för att automatisera korrigerings proceduren. 
* I `Plugins\ProjectAcoustics\Resources` kör`PatchWwise.bat`du. I exempel bilden nedan används vårt AcousticsGame-exempel projekt.

    ![Skärm bild av fönster i Utforskaren som har angett skriptet för korrigerings Wwise](media/patch-wwise-script.png)

* Om du inte har installerat DirectX SDK måste du kommentera ut raden som innehåller DXSDK_DIR i`[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Skärm bild av kod redigeraren som visar DXSDK kommenterad](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Utveckla spel och kontrol lera python är aktiverat

* Kompilera spelet och se till att det fungerar korrekt. Annars kan du kontrol lera föregående steg noggrant innan du fortsätter. 
* Öppna projektet i Unreal-redigeraren. 
* **Motorns plugin-program:** Om du använder ProjectAcoustics som motor-plugin-program måste du också kontrol lera att den är aktive rad under inbyggda plugin-program.
* Du bör se ett nytt läge, som visar att projekt akustiska har integrerats.

    ![Skärm bild av Unreal visar att akustiskt läge är fullt](media/acoustics-mode-full.png)

* Bekräfta att du har python-plugin-programmet för Unreal aktiverat. Detta krävs för att redigerings integrationen ska fungera korrekt.

    ![Skärm bild som aktiverar python-tillägg i Unreal-redigeraren](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Wwise projekt konfiguration

Ett exempel på ett Wwise-projekt ingår i hämtningen av exempel. Vi rekommenderar att du tittar på den tillsammans med de här anvisningarna. Skärm bilderna nedan hämtas från det här projektet.

### <a name="bus-setup"></a>Buss konfiguration
* Unreal-plugin-programmet för Project akustiskt kommer att leta efter tillhör ande mixer-plugin -program på `Project Acoustics Bus`en buss med det här exakta namnet:. Skapa en ny ljud buss med det här namnet. Mixer-plugin-programmet kan fungera i olika konfigurationer, men för närvarande förutsätter vi att det endast används för att bearbeta reverb. Den här bussen bär den blandade reverb-signalen för alla källor som använder akustiska signaler. Det kan blanda uppladdning i en struktur blandnings struktur, ett exempel visas nedan, som tas från vårt Wwise-exempel projekt som ingår i hämtnings exemplet.

    ![Skärm bild av Wwise Busses som visar projekt Akustisker Bus](media/acoustics-bus.png)

* Kanal konfigurationen på bussen måste anges till en av: `1.0, 2.0, 4.0, 5.1 or 7.1`. Andra konfigurationer medför inga utdata på den här bussen.

    ![Skärm bild av kanal konfigurations alternativ för Project Akustiske Bus](media/acoustics-bus-channel-config.png)

* Gå nu till informationen om projektets akustiska bussar och se till att du kan se fliken mixer-plugin

    ![Skärm bild av Wwise som visar hur du aktiverar fliken mixer-plugin för projektets akustiska buss](media/mixer-tab-enable.png)

* Gå sedan till fliken mixer-plugin och Lägg till ett plugin-program för Project akustiskt mixer i bussen

    ![Screenshow för Wwise-bussen som visar hur du lägger till projektet akustiska mixer-plugin](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Installation av aktör – mixer-hierarki
* Av prestanda skäl tillämpar projekt akustiskt ljud-DSP för alla källor samtidigt. Detta kräver att plugin-programmet körs som ett mixer-pluginprogram. Wwise kräver att mixer-plugin-program finns på utdataporten, även om utmatnings bussen vanligt vis har den torra signalen av utdata. För att projicera akustiska signaler krävs att den torra signalen dirigeras via AUX Busses medan den våta signalen utförs `Project Acoustics Bus`på. Följande process stöder gradvis migrering till det här signal flödet.

* Anta att du har ett befintligt projekt med en aktörs mixer-hierarki som innehåller följer, vapen och andra på den högsta nivån. Varje har motsvarande utmatnings buss för dess torra mix. Du kan säga att du vill migrera följer för att använda akustisker. Skapa först en motsvarande AUX-buss för att transportera sin torra submix som är underordnad följer utmatnings buss. Vi har till exempel använt ett "torrt" prefix i bilden nedan för att ordna dessa, även om det exakta namnet inte är viktigt. Eventuella mätare eller effekter som du hade på följer-bussen fungerar fortfarande som tidigare.

    ![Skärm bild av Rekommenderad Wwise torr mix-installation](media/wwise-dry-mix-setup.png)

* Ändra sedan strukturen för Bus-utdata för följer aktör-mixer enligt följande, med Project Akustisker Bus inställd som utdataport och Dry_Footsteps angetts som en användardefinierad AUX-buss.

    ![Skärm bild av den rekommenderade Wwise aktörs mixer-installationen](media/actor-mixer-bus-settings.png)

* Nu ger alla följer en akustisk behandling och utvärderar sin reverb på projektets akustiska buss. Den torra signalen dirigeras via Dry_Footsteps och är som vanligt.

* Projekt akustiska gäller bara för ljud som har en 3D-plats i världen. Enligt [Wwise-dokumentationen](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)måste placerings egenskaperna anges som de visas. Inställningen "3D-spatialization" kan vara antingen "position" eller "position + orientering" efter behov.

    ![Skärm bild av rekommenderade inställningar för Wwise aktörs placering](media/wwise-positioning.png)

* Att ställa in utmatnings bussen till en annan buss som blandar överströms till **Project akustisker Bus** fungerar inte. Wwise tillämpar detta krav på mixer-plugin-program.

* Om du vill att en underordnad i hierarkin följer aktör-mixer inte ska använda akustisker, kan du alltid använda "Åsidosätt överordnad" på den för att välja den.

* Om du använder spel-eller användardefinierade sändningar för reverb på en aktörs mixer i spelet, så inaktivera dem på den här aktörens mixer för att undvika att använda reverb två gånger.

### <a name="spatialization"></a>Spatialization
Som standard tillämpar Wwise mixer-plugin-programmet Convolution reverb, vilket lämnar Wwise för att göra panorering spatialization. När du använder projekt akustiska objekt i den här standard konfigurationen för reverb är det dags att använda valfri kanal konfiguration och spatialization-metod på din torra mix, så att du kan blanda och matcha nästan alla Spatializer med projekt akustiska reverb. Alternativen omfattar [Ambisonics-baserade binaural-spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) och [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Projekt akustiska innehåller en valfri Spatializer som stöder både objektbaserade HRTF-rendering med hög upplösning och panorering. Markera kryss rutan "utför spatialization" i inställningarna för mixer-plugin-programmet och välj mellan HRTF eller panorering och inaktivera användardefinierade AUX skickar inställningar ovan till alla torra Busses för att undvika att det går att frigöra två gånger, både med projekt akustiskt mixer plugin och Wwise. Spatialization-läget kan inte ändras i real tid, eftersom det kräver en regenerering av en sund bank. Du måste starta om Unreal och sedan återskapa soundbanks innan du trycker på Play för att hämta konfigurations ändringar för mixer-plugin, till exempel kryss rutan utföra spatialization.

![Skärm bild av Wwise mixer plugin spatialization-inställningar](media/mixer-spatial-settings.png)

Tyvärr kan andra objektbaserade Spatializer-plugin-program inte stödjas just nu eftersom de implementeras som mixer-plugin-program, och Wwise tillåter för närvarande inte flera mixer-plugin-program som tilldelas en enda aktörs mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Ljud konfiguration i Unreal
* Först måste du gå in på spel nivån för att skapa en akustisk till gång, som kommer att placeras i `Content\Acoustics`. Läs [själv studie kursen om Unreal](unreal-baking.md) i bagerien och fortsätt här. Vissa för bakade-nivåer ingår i exempel paketet.
* Skapa en akustiskt utrymmes aktör i din scen. Skapa bara en av dessa aktörer på en nivå eftersom den representerar de akustiska värdena för hela nivån. 

    ![Skärm bild av Unreal-redigeraren som visar skapande av akustiskt utrymmes skådespelare](media/create-acoustics-space.png)

* Tilldela nu den bakade akustiska data till gången till den akustiska data platsen på den akustiska ytans aktör. Nu har din scen akustiska ljud!

    ![Skärm bild av Unreal-redigeraren s howing-akustiska till gångs tilldelning](media/acoustics-asset-assign.png)

* Lägg nu till en tom aktör och gör följande:

    ![Skärm bild av Unreal-redigeraren som visar akustiska komponent användning i en tom aktör](media/acoustics-component-usage.png)

1. Lägg till ljud komponenten akustiskt i aktören. Den här komponenten utökar komponenten Wwise-ljud med funktioner för projekt akustiska funktioner.
2. Rutan spela upp på Start är markerad som standard, vilket utlöser tillhör ande Wwise-händelse på nivån start.
3. Använd kryss rutan Visa akustiska parametrar för att skriva ut felsöknings information om källan på skärmen.
    ![Skärm bild av Unreal Editor Akustisker panel på ljud källa med aktiverade fel söknings värden](media/debug-values.png)
4. Tilldela en Wwise-händelse per vanligt Wwise-arbetsflöde
5. Kontrol lera att Använd spatial ljud är inaktiverat. Om du använder ett projekt akustiskt objekt för en viss ljud komponent kan du för närvarande inte samtidigt använda Wwises avstånds ljud motor för akustiska enheter.

Då var allt klart. Flytta runt scenen och utforska akustiska effekter!

## <a name="next-steps"></a>Nästa steg
* [Design](unreal-workflow.md) självstudie för projekt akustiska i Unreal/Wwise
* [Lär dig att göra bagerier](unreal-baking.md) för spelets scener 
