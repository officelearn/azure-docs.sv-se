---
title: Project Acoustics Unreal och Wwise integration
titlesuffix: Azure Cognitive Services
description: I den här artikeln beskrivs integreringen av projektakustiken Unreal och Wwise-plugin-program i projektet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243050"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project Acoustics Unreal och Wwise integration
I den här artikeln beskrivs hur du integrerar plug-in-paketet Project Acoustics i ditt befintliga Unreal- och Wwise-spelprojekt.

Programvarukrav:
* [Overklig motor](https://www.unrealengine.com/) 4,20+
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Wwise plug-in för Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Om du använder en direkt integrering av Wwise SDK i stället för Wwise Unreal plug-in, konsultera Project Acoustics Unreal plug-in och justera Wwise API-anrop.

Om du vill använda Project Acoustics med en annan ljudmotor än Wwise gör du en [förbättringsbegäran i diskussionsforumet Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues). Du kan använda plugin-programmet Project Acoustics Unreal för att fråga akustikdata och ringa API-anrop till din motor.

## <a name="download-project-acoustics"></a>Ladda ner Project Acoustics
Ladda ner [plugin-paketet Project Acoustics Unreal och Wwise](https://www.microsoft.com/download/details.aspx?id=58090) om du inte redan har gjort det.

Ett Overkligt motorplugg och ett Wwise mixer plug-in ingår i förpackningen. Det overkliga plugin-programmet ger redigering och körningsintegrering. Under spelets gång beräknar project acoustics unreal plug-in parametrar som ocklusion för varje spelobjekt för varje bildruta. Dessa parametrar översätts till Wwise API-anrop.

## <a name="integration-steps"></a>Integrationssteg

Följ dessa steg för att installera paketet och distribuera det i ditt spel.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Installera plug-in-programmet Project Acoustics mixer
1. Öppna Wwise-bärraketen. Välj **Lägg till från katalog**under Installera nya plugin-program på fliken **Plugin-program** . **Install New Plug-ins**

    ![Installera ett plugin-program i Wwise-bärraketen](media/wwise-install-new-plugin.png)

1. Välj katalogen *AcousticsWwisePlugin\ProjectAcoustics* som finns i nedladdningspaketet. Den innehåller Wwise mixer plug-in bunt.

   Wwise installerar plugin-programmet. Projektakustik ska visas på den installerade plugin-listan i Wwise.  
![Wwise-listan installerade plugin-program efter installationen av Project Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Dedeploy Wwise i ditt spel
Distribuera om Wwise till ditt spel även om du redan har integrerat Wwise. Det här steget integrerar plug-in-programmet Project Acoustics Wwise.

   > [!NOTE]
   > **Plugin-program för motorn:** Om du har Wwise installerat som ett spel plug-in i ett Unreal C++-projekt hoppar du över det här steget. Om det installeras i stället som ett plugin-program för motorn, till exempel på grund av att ditt Unreal-projekt endast är Blueprint, är Wwise-distributionen med vårt plugin-program för mixer mer komplex. Skapa ett tomt Unreal C++-projekt. Stäng Unreal editor om den öppnas och följ den återstående proceduren för att distribuera Wwise i dummy-projektet. Kopiera sedan ut det distribuerade Wwise-plugin-programmet.
 
1. Välj fliken **Unreal Engine** på Wwise-startprogrammet. **Recent Unreal Engine Projects** **Browse for project** Öppna spelets Unreal-projekt *.project-fil.*

    ![Fliken Wwise launcher Unreal](media/wwise-unreal-tab.png)

1. Välj **Integrera Wwise i Project** eller Ändra **Wwise i Project**. Det här steget integrerar Wwise binärer i ditt projekt, inklusive Project Acoustics mixer plug-in.

   > [!NOTE]
   > **Plugin-program för motorn:** Om du använder Wwise som ett plugin-program för motorn och du har skapat dummy-projektet enligt beskrivningen tidigare kopierar du mappen som Wwise distribuerade: *[DummyUProject]\Plugins\Wwise*. Klistra in den över *[UESource]\Engine\Plugins\Wwise*. *[DummyUProject]* är den tomma Unreal C++-projektsökvägen och *[UESource]* är där unreal engine-källorna är installerade. När du har kopierat mappen kan du ta bort dummy-projektet.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Lägg till plugin-programmet Project Acoustics Unreal i spelet
 
1. Kopiera mappen *Unreal\ProjectAcoustics* i plugin-programmet. Skapa en ny mapp *[UProjectDir]\Plugins\ProjectAcoustics*, där *[UProjectDir]* är spelets projektmapp som innehåller *.uproject-filen.*

   > [!NOTE]
   > **Plug-in för motorn**: Om du använder Wwise som ett plugin-program för motorn bör du också använda Project Acoustics som ett overkligt motorplugg-in. I stället för målkatalogen som tidigare nämnts använder du *[UESource]\Engine\Plugins\ProjectAcoustics*.

1. Bekräfta att du ser en *Wwise-mapp* bredvid mappen *ProjectAcoustics.* Den innehåller Wwise plug-in tillsammans med binärfiler för mixer plug-in som du distribueras tidigare.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Utöka Wwise Unreal plug-in-funktioner
Projektet Akustik Unreal plug-in kräver ytterligare beteende exponeras från Wwise Unreal plug-in API enligt [dessa riktlinjer](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Vi har inkluderat en kommandofil för att automatisera korrigeringsproceduren.

* Inuti *Plugins\ProjectAcoustics\Resources*, kör *PatchWwise.bat*. Följande exempelbild använder vårt Exempelprojekt AcousticsGame.

    ![Ett Utforskarfönster med skriptet för att korrigera Wwise markerat](media/patch-wwise-script.png)

* Om du inte har DirectX SDK installerat: Beroende på vilken version av Wwise du använder, kan `DXSDK_DIR` du behöva kommentera den linje som finns i *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Koden redaktör som visar "DXSDK" kommenterade ut](media/directx-sdk-comment.png)

* Om du kompilerar med Visual Studio 2019: För att komma runt `VSVersion` ett länkfel med Wwise ändrar du standardvärdet manuellt i *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* till **vc150**:

    ![Kodredigeraren som visar "VSVersion" har ändrats till "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Bygg spelet och kontrollera att Python är aktiverat

1. Kompilera ditt spel och se till att det bygger på rätt sätt. Om den inte byggs kontrollerar du föregående steg noggrant innan du fortsätter.

1. Öppna projektet i Unreal Editor.

    > [!NOTE]
    > **Plugin-program för motorn:** Om du använder ProjectAcoustics som ett plugin-program för motorn ska du också se till att det är aktiverat under plugin-program för "inbyggt".

    Du bör se ett nytt läge som anger att Project Acoustics har integrerats.

    ![Akustikläget fullt i Unreal](media/acoustics-mode-full.png)

1. Bekräfta att Python-plugin-programmet för Unreal är aktiverat så att redigeringsintegrationen fungerar korrekt.

    ![Python-tilläggen i Unreal-redigeraren aktiverade](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Konfigurera Wwise-projektet så att projektakustiken används

Ett exempel Wwise projekt ingår i exemplen nedladdning. Vi rekommenderar att du visar den tillsammans med dessa instruktioner. Skärmbilderna senare i den här artikeln kommer från det här projektet.

#### <a name="bus-setup"></a>Bussinställningar
Project Acoustics Unreal plug-in kommer att leta efter tillhörande mixer plug-in på en buss som har det exakta namnet `Project Acoustics Bus`. Skapa en ny ljudbuss med samma namn. Plugin-programmet för mixern kan fungera i olika konfigurationer. Men för nu antar vi att det kommer att användas endast för reverb bearbetning. Denna buss kommer att bära den blandade reverb signalen för alla källor som använder Akustik. Det kan blanda uppströms i någon buss-blandning struktur. Ett exempel visas här från Wwise-exempelprojektet som ingår i exempelhämtningen.

![Wwise bussar som visar Project Acoustics Bus](media/acoustics-bus.png)

1. Ställ in kanalkonfigurationen på bussen på *1.0,* *2.0,* *4.0,* *5.1*eller *7.1*. Alla andra inställningar resulterar inte i någon utgång på bussen.

    ![Kanalkonfigurationsalternativ för Project Acoustics Bus](media/acoustics-bus-channel-config.png)

1. Gå in på information om projektakustikbuss och se till att du kan se **fliken Plugin-program** för mixer.

    ![Wwise med mixerplug-in-fliken för Project Acoustics Bus aktiverat](media/mixer-tab-enable.png)

1. Gå till **mixerkontaktfliken** och lägg till plugin-programmet för akustikmixer i bussen.

    ![Diagram över hur du lägger till Plug-in-program för Project Acoustics Mixer i Wwise-bussen](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Hierarkiinställning för aktörsmixer
För bästa prestanda tillämpar Project Acoustics ljuddiger digital signalbehandling på alla källor samtidigt. Så måste plug-in fungera som en mixer plug-in. Wwise kräver mixer plug-ins att vara på utgående bussen, även om utgående bussen vanligtvis bär den torra utsignalen. Projektet Akustik kräver att den torra signalen dras genom aux `Project Acoustics Bus`bussar, medan den våta signalen bärs på . Följande process stöder gradvis migrering till detta signalflöde.

Anta att du har ett befintligt projekt med en hierarki för aktörsblandare som innehåller *fotsteg,* *vapen*och andra på den översta nivån. Var och en har en motsvarande utgångsbuss för sin torra blandning. Anta att du vill migrera fotsteg för att använda akustik. Skapa först en motsvarande auxbuss för att bära den torra submixen som är ett barn i fotstegsutmatningsbussen. Till exempel använde vi ett "Torr" prefix i följande bild för att organisera bussarna, även om det exakta namnet inte är viktigt. Alla meter eller effekter som du hade på fotsteg bussen kommer fortfarande att fungera som tidigare.

![Rekommenderad Wwise torr mix setup](media/wwise-dry-mix-setup.png)

Ändra sedan bussutdatastrukturen för aktörsmixern Fotsteg enligt följande, med *Project Acoustics Bus* inställd som **utdatabuss**och *Dry_Footsteps* som en användardefinierad aux-buss.

![Rekommenderad Wwise skådespelare mixer buss setup](media/actor-mixer-bus-settings.png)

Nu får alla fotsteg akustikbehandling och matar ut sin reverb på Project Acoustics Bus. Den torra signalen dras genom Dry_Footsteps och spatialiseras som vanligt.

Projektakustik gäller endast ljud som har en 3D-plats i världen. Efter [Wwise-dokumentation](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)måste positioneringsegenskaperna ställas in enligt bilden. **Inställningen 3D Spatialization** kan vara antingen *Position* eller Position *+ Orientering* efter behov.

![Rekommenderade positioneringsinställningar för Wwise-skådespelare](media/wwise-positioning.png)

Du kan inte ställa in **Output Bus** på någon annan buss som blandar uppströms till *Project Acoustics Bus*. Wwise ställer detta krav på mixer plug-ins.

Om du vill att ett barn i fotspåren aktör-mixer hierarki att inte använda akustik, kan du använda "åsidosätta överordnad" på den för att välja bort det.

Om du använder speldefinierade eller användardefinierade skickar efterklang på någon aktör-mixer i spelet, stänga av dem på den aktör-mixer för att undvika att tillämpa reverb två gånger.

#### <a name="spatialization"></a>Spatialization
Projektet Akustik Wwise mixer plug-in gäller faltning reverb som standard, lämnar Wwise att göra panorering spatialization. När du använder Project Acoustics i den här standardkonfigurationen för endast efterklang kan du använda valfri kanalkonfiguration och spatialiseringsmetod på din torrmix. Så kan du blanda och matcha nästan alla spatializer med Project Acoustics reverb. Alternativen inkluderar [Ambisonics-baserade binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) och [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Project Acoustics innehåller en valfri spatializer som stöder både objektbaserad HRTF-rendering och panorering med hög upplösning. Markera kryssrutan **Utför spatialisering** i plugin-inställningarna för mixern och välj mellan *HRTF* eller *Panorering*. Inaktivera också användardefinierade aux-meddelanden till alla torra bussar för att undvika att spatialisera två gånger av Plug-in-programmet Project Acoustics mixer och Wwise. Spatialiseringsläget kan inte ändras i realtid eftersom det kräver en ljudbanksregenerering. Starta om Unreal och återskapa sedan soundbanks innan du markerar play för att integrera plugin-konfigurationsändringar för blandare, till exempel kryssrutan **Utför spatialisering.**

![Inställningar för Wwise Mixer Plug-in Spatialization](media/mixer-spatial-settings.png)

Tyvärr stöds inte andra objektbaserade spatializer-plugin-program för tillfället. De är implementerade som mixer plug-ins, och Wwise tillåter inte flera mixer plug-ins som skall tilldelas en enda aktör-mixer.  

### <a name="audio-setup-in-unreal"></a>Ljudinställningar i Unreal
1. Först måste du baka din spelnivå för att producera en akustik tillgång, som kommer att placeras i *Content \Akustik*. Konsultera [Unreal Bake Tutorial](unreal-baking.md). Vissa förbakade nivåer ingår i provförpackningen.

1. Skapa en akustik utrymme skådespelare i din scen. Skapa bara en av dessa aktörer på en nivå, eftersom den representerar akustiken för hela nivån.

    ![Skapande av en Akustik utrymme skådespelare i Unreal redaktör](media/create-acoustics-space.png)

1. Tilldela den bakade akustiska datatillgången till akustikdataplatsen på rymdaktören Akustik. Din scen har nu akustik!

    ![Tilldelning av Akustiktillgång i Unreal-redigeraren](media/acoustics-asset-assign.png)

1. Lägg till en tom aktör. Konfigurera den enligt följande.

    ![Den overkliga redigeraren visar Akustikkomponentanvändning i en tom aktör](media/acoustics-component-usage.png)

       
    <sup>1</sup> Lägg till en ljudkomponent i Akustik till aktören. Den här komponenten lägger till Project Acoustics-funktionen i Wwise-ljudkomponenten.
        
    <sup>2</sup> Rutan **Spela upp på Start** är markerad som standard. Den här inställningen utlöser en associerad Wwise-händelse vid start på nivå.</li>
         
    <sup>3</sup> Använd kryssrutan **Visa akustikparametrar** för att skriva ut felsökningsinformation på skärmen om källan.

    ![Panelen Overklig editor Akustik på ljudkälla med felsökningsvärden aktiverade](media/debug-values.png)

    <sup>4</sup> Tilldela en Wwise-händelse enligt det vanliga Wwise-arbetsflödet.
       
    <sup>5</sup> Kontrollera att **Använd rumsligt ljud** är inaktiverat. Om du använder Projektakustik för en viss ljudkomponent kan du inte samtidigt använda Wwises Spatial Audio-motor för akustik.</li>
       
Då var allt klart. Flytta runt scenen och utforska de akustiska effekterna!

## <a name="next-steps"></a>Nästa steg
* Prova [projektet Akustik Unreal / Wwise Design Tutorial](unreal-workflow.md).
* Lär dig [hur man gör bakar](unreal-baking.md) för ditt spel scener.
