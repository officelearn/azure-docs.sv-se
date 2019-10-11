---
title: Project-akustiskt Unreal och Wwise-integrering
titlesuffix: Azure Cognitive Services
description: I den här artikeln beskrivs integrering av Unreal-och Wwise-plugin-program i projektet.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243050"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project-akustiskt Unreal och Wwise-integrering
Den här artikeln beskriver hur du integrerar plugin-paketet för Project akustiskt i ditt befintliga Unreal-och Wwise Game-projekt.

Program varu krav:
* [Unreal-motor](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1
* [Wwise-plugin-program för Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Om du använder en direkt integrering av Wwise SDK i stället för Wwise Unreal-plugin-programmet kan du läsa plugin-programmet för Project akustiskt Unreal och justera Wwise API-anrop.

Om du vill använda projekt akustiska data med en annan ljud motor än Wwise gör du en förbättrings förfrågan i [diskussions forumet för projekt akustiskt](https://github.com/microsoft/ProjectAcoustics/issues). Du kan använda plugin-programmet för Project Akustiske Unreal för att fråga akustiska data och göra API-anrop till din motor.

## <a name="download-project-acoustics"></a>Ladda ned projekt akustiska
Hämta [plugin-paketet för Project akustisker Unreal och Wwise](https://www.microsoft.com/download/details.aspx?id=58090) om du inte redan gjort det.

Ett plugin-program för Unreal-motorn och ett Wwise mixer-plugin-program ingår i paketet. Plugin-programmet Unreal innehåller redigerings-och körnings integration. Under spel upplevelsen kan projektet akustiskt Unreal-plugin-programmet beräkna parametrar som ocklusion för varje spel objekt för varje bild ruta. Dessa parametrar översätts till Wwise-API-anrop.

## <a name="integration-steps"></a>Integrerings steg

Följ de här stegen för att installera paketet och distribuera det i spelet.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Installera plugin-programmet för ljud mixnings mixer i Project
1. Öppna Start programmet för Wwise. Välj **Lägg till från katalog**under **installera nya plugin-program**på fliken **plugin-program** .

    ![Installera ett plugin-program i Wwise Launcher](media/wwise-install-new-plugin.png)

1. Välj den *AcousticsWwisePlugin\ProjectAcoustics* -katalog som finns i nedladdnings paketet. Den innehåller plugin-paketet Wwise mixer.

   Wwise kommer att installera plugin-programmet. Project-Akustisker bör visas i listan installerade plugin-program i Wwise.  
![Wwise installerade plugin-program visas efter installationen av Project akustiskt](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Distribuera Wwise i spelet
Distribuera om Wwise i spelet även om du redan har integrerat Wwise. I det här steget integreras plugin-programmet för Project Akustisker Wwise.

   > [!NOTE]
   > **Motorns plugin-program:** Om du har Wwise installerat som ett spel-plugin-program i ett C++ Unreal-projekt hoppar du över det här steget. Om det är installerat i stället för ett motor-plugin-program, till exempel eftersom ditt Unreal-projekt bara är skiss, är Wwise-distribution med vår mixer-plugin mer komplex. Skapa ett tomt Unreal C++ -projekt. Stäng Unreal-redigeraren om den öppnas och följ den återstående proceduren för att distribuera Wwise till dummy-projektet. Kopiera sedan det distribuerade Wwise-plugin-programmet.
 
1. Välj fliken **Unreal motor** från Wwise start. Välj fliken "Hamburger" (ikon) bredvid de **senaste Unreal-motorns projekt** och välj sedan **Bläddra efter projekt**. Öppna spelets Unreal Project *. Project* -fil.

    ![Fliken Wwise Launcher Unreal](media/wwise-unreal-tab.png)

1. Välj **integrera Wwise i projektet** eller **ändra Wwise i projektet**. I det här steget integreras Wwise-binärfiler i ditt projekt, inklusive plugin-programmet för akustiska mixers mixer.

   > [!NOTE]
   > **Motorns plugin-program:** Om du använder Wwise som ett motor-plugin-program och du har skapat provdocka-projektet enligt beskrivningen ovan, kopierar du mappen som Wwise distribuerade: *[DummyUProject] \Plugins\Wwise*. Klistra in den över *[UESource] \Engine\Plugins\Wwise*. *[DummyUProject]* är den tomma Unreal C++ projekt Sök vägen och *[UESource]* är den plats där Unreal-motor källorna är installerade. När du har kopierat mappen kan du ta bort dummy-projektet.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Lägg till plugin-programmet för Project Akustiske Unreal i spelet
 
1. Kopiera mappen *Unreal\ProjectAcoustics* i plugin-paketet. Skapa en ny mapp *[UProjectDir] \Plugins\ProjectAcoustics*, där *[UProjectDir]* är spelets projektmapp som innehåller *. uproject* -filen.

   > [!NOTE]
   > **Motor-plugin-program**: om du använder Wwise som ett motor-plugin-program bör du använda Project-akustiskt som en Unreal-motor. I stället för mål katalogen som anges tidigare använder du *[UESource] \Engine\Plugins\ProjectAcoustics*.

1. Bekräfta att du ser en *Wwise* -mapp tillsammans med mappen *ProjectAcoustics* . Den innehåller plugin-programmet Wwise tillsammans med binärfiler för det mixer-plugin-program som du distribuerade tidigare.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Utöka funktioner för Wwise Unreal-plugin
Plugin-programmet för Project Akustiske Unreal kräver ytterligare beteende som exponeras från API: et för Wwise-Unreal enligt [dessa rikt linjer](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Vi har inkluderat en kommando fil för att automatisera korrigerings proceduren.

* I *Plugins\ProjectAcoustics\Resources*kör du *PatchWwise. bat*. I följande exempel bild används vårt AcousticsGame-exempel projekt.

    ![Ett Windows Explorer-fönster med skriptet för att korrigera Wwise är markerat](media/patch-wwise-script.png)

* Om du inte har installerat DirectX SDK: beroende på vilken version av Wwise du använder kan du behöva kommentera ut raden som innehåller `DXSDK_DIR` i *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Kod redigeraren visar ' DXSDK '-kommenterad](media/directx-sdk-comment.png)

* Om du kompilerar med hjälp av Visual Studio 2019: om du vill undvika ett länknings fel med Wwise ändrar du standard `VSVersion`-värdet manuellt i *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* till **vc150**:

    ![Kod redigeraren som visar "VSVersion" har ändrats till "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Bygg spelet och kontrol lera att python är aktiverat

1. Kompilera ditt spel och se till att det fungerar som det ska. Om den inte bygger kan du kontrol lera föregående steg noggrant innan du fortsätter.

1. Öppna projektet i Unreal-redigeraren.

    > [!NOTE]
    > **Motorns plugin-program:** Om du använder ProjectAcoustics som ett motor-plugin-program ser du också till att det är aktiverat under inbyggda plugin-program.

    Du bör se ett nytt läge som visar att projekt akustiskt har integrerats.

    ![Akustiskt läge är fullt i Unreal](media/acoustics-mode-full.png)

1. Bekräfta att python-plugin-programmet för Unreal har Aktiver ATS så att Editor-integreringen fungerar korrekt.

    ![Python-tilläggen i Unreal-redigeraren är aktiverade](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Konfigurera ditt Wwise-projekt för att använda projekt akustiska

Ett exempel på ett Wwise-projekt ingår i hämtnings exempel. Vi rekommenderar att du visar det tillsammans med de här anvisningarna. Skärm bilderna längre fram i den här artikeln är från det här projektet.

#### <a name="bus-setup"></a>Buss konfiguration
Plugin-programmet för Project Akustisker Unreal letar efter det associerade mixer-plugin-programmet på en buss som har det exakta namnet `Project Acoustics Bus`. Skapa en ny ljud buss med samma namn. Mixer-plugin-programmet kan fungera i olika konfigurationer. Men för närvarande förutsätter vi att det endast kommer att användas för reverb bearbetning. Den här bussen bär den blandade reverb-signalen för alla källor som använder akustiska signaler. Det kan blanda överordnat i en struktur som kan kombineras. Ett exempel visas här från Wwise-exempelprojektet som ingår i exempel hämtningen.

![Wwise bussar visar projekt akustiska bussar](media/acoustics-bus.png)

1. Ange kanal konfigurationen på bussen till *1,0*, *2,0*, *4,0*, *5,1*eller *7,1*. Alla andra inställningar medför inga utdata på bussen.

    ![Kanal konfigurations alternativ för Project Akustiske Bus](media/acoustics-bus-channel-config.png)

1. Gå till informationen om projektets akustiska bussar och se till att du kan se fliken **mixer-plugin** .

    ![Wwise med fliken mixer-plugin för att projicera akustiska bussar är aktive rad](media/mixer-tab-enable.png)

1. Gå till fliken **mixer-plugin** och Lägg till plugin-programmet för Project Akustiske mixer i bussen.

    ![Diagram över hur du lägger till projekt akustiskt mixer-plugin-programmet i Wwise-bussen](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Installation av aktör – mixer-hierarki
För bästa prestanda tillämpar Project-akustiskt ljud bearbetning av digitala signaler till alla källor samtidigt. Plugin-programmet måste därför köras som ett mixer-pluginprogram. Wwise kräver att mixer-plugin-program finns på utdataporten, även om utmatnings bussen vanligt vis har den torra signalen. Project-Akustisker kräver att den torra signalen dirigeras via AUX-bussar, medan den våta signalen överförs på `Project Acoustics Bus`. Följande process stöder gradvis migrering till det här signal flödet.

Anta att du har ett befintligt projekt med en aktörs mixer hierarki som innehåller *följer*, *vapen*och andra på den högsta nivån. Varje har en motsvarande utmatnings buss för dess torra mix. Anta att du vill migrera följer för att använda akustisker. Börja med att skapa en motsvarande AUX-buss för att transportera den torra submix som är underordnad följer utmatnings buss. Vi använde till exempel ett "torrt"-prefix i följande bild för att ordna bussar, även om det exakta namnet inte är viktigt. Eventuella mätare eller effekter som du hade i följer-bussen fungerar fortfarande som tidigare.

![Rekommenderad Wwise torr mix-installation](media/wwise-dry-mix-setup.png)

Ändra sedan strukturen för Bus-utdata för följer aktör-mixer enligt följande, med *Project akustisker-buss* uppsättning som **utdataporten**och *Dry_Footsteps* angetts som en användardefinierad AUX-buss.

![Rekommenderad Wwise aktörs mixer-installation](media/actor-mixer-bus-settings.png)

Nu ger alla följer en akustisk behandling och utvärderar sin reverb på projektets akustiska buss. Den torra signalen dirigeras via Dry_Footsteps och är som vanligt.

Projekt akustiska gäller bara för ljud som har en 3D-plats i världen. Enligt [Wwise-dokumentationen](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)måste placerings egenskaperna anges som de visas. Inställningen **3D-spatialization** kan vara antingen *position* eller *position + orientering* vid behov.

![Rekommenderade placerings inställningar för Wwise aktör](media/wwise-positioning.png)

Du kan inte ange **utmatnings buss** till någon annan buss som blandar överström till *Project akustisker Bus*. Wwise tillämpar detta krav på mixer-plugin-program.

Om du vill att en underordnad i hierarkin följer aktör-mixer inte ska använda akustisker kan du använda "Åsidosätt överordnad" på den för att välja den.

Om du använder speldefinierade eller användardefinierade sändningar för reverb på valfri aktörs mixer i spelet, kan du inaktivera dem på aktörens mixer för att undvika att använda reverb två gånger.

#### <a name="spatialization"></a>Spatialization
Plugin-programmet för projekt Akustisker Wwise-mixer tillämpar Convolution reverb som standard, vilket gör att Wwise kan panorera spatialization. När du använder projekt akustiska objekt i den här standard konfigurationen för reverb kan du använda valfri kanal konfiguration och spatialization metod på din torra mix. Det betyder att du kan blanda och matcha nästan alla Spatializer med projektet akustiska reverb. Alternativen omfattar [Ambisonics-baserade binaural-spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) och [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Project-Akustisker innehåller en valfri Spatializer som stöder både objektorienterad HRTF åter givning och panorering. Markera kryss rutan **utför spatialization** i inställningarna för mixer-plugin och välj mellan *HRTF* eller *panorering*. Inaktivera även användardefinierade AUX skickar till alla torra bussar för att undvika att gå två gånger i projektet akustiska plugin-program och Wwise. Spatialization-läget kan inte ändras i real tid eftersom det kräver en regenerering av en sund bank. Starta om Unreal och återskapa sedan soundbanks innan du väljer spela upp för att integrera mixer-konfigurations ändringar, till exempel kryss Rute inställningen **utför spatialization** .

![Spatialization inställningar för Wwise mixer-plugin](media/mixer-spatial-settings.png)

Tyvärr stöds inte andra objektbaserade Spatializer-plugin-program för närvarande. De implementeras som mixer-plugin-program och Wwise tillåter inte att flera mixer-plugin-program tilldelas till en enda aktörs mixer.  

### <a name="audio-setup-in-unreal"></a>Ljud konfiguration i Unreal
1. Först måste du gå in på spel nivån för att skapa en akustisk till gång, som kommer att placeras i *Content\Acoustics*. Läs [själv studie kursen om Unreal](unreal-baking.md). Vissa för bakade-nivåer ingår i exempel paketet.

1. Skapa en akustiskt utrymmes aktör i din scen. Skapa bara en av dessa aktörer på en nivå, eftersom den representerar de akustiska objekten för hela nivån.

    ![Skapa ett akustiskt utrymmes skådespelare i Unreal-redigeraren](media/create-acoustics-space.png)

1. Tilldela den bakade akustiska data till gång till den akustiska data platsen på den akustiska ytans aktör. Nu har din scen akustiska ljud!

    ![Till gångs tilldelning för akustiskt objekt i Unreal-redigeraren](media/acoustics-asset-assign.png)

1. Lägg till en tom aktör. Konfigurera den på följande sätt.

    ![Unreal-redigeraren visar akustisk komponent användning i en tom aktör](media/acoustics-component-usage.png)

       
    <sup>1</sup> Lägg till ljud komponenten akustiskt i aktören. Den här komponenten ger till gång till projekt akustiska funktioner till ljud komponenten Wwise.
        
    <sup>2</sup> rutan **spela upp i Start** är markerad som standard. Den här inställningen utlöser en associerad Wwise-händelse på nivån start.</li>
         
    <sup>3</sup> Använd kryss rutan **Visa akustiska parametrar** för att skriva ut felsöknings information om källan på skärmen.

    ![Unreal redigerarens akustiska panel på ljud källa med aktiverade fel söknings värden](media/debug-values.png)

    <sup>4</sup> tilldela en Wwise-händelse per vanligt Wwise-arbetsflöde.
       
    <sup>5</sup> se till att **Använd spatial ljud** är inaktiverat. Om du använder ett projekt akustiskt objekt för en viss ljud komponent kan du inte samtidigt använda Wwises avstånds ljud motor för akustiska ljud.</li>
       
Då var allt klart. Flytta runt scenen och utforska akustiska effekter!

## <a name="next-steps"></a>Nästa steg
* Prova [själv studie kursen om projekt akustiska Unreal/Wwise-design](unreal-workflow.md).
* Lär dig [hur du kan göra bagerier](unreal-baking.md) i spel bakgrunden.
