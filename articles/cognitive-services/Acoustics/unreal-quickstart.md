---
title: Snabb start för projekt med Unreal
titlesuffix: Azure Cognitive Services
description: Använd exempel innehåll för att experimentera med projekts akustiska design kontroller i Unreal och Wwise och distribuera till Windows-skrivbordet.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242922"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Snabb start för projekt Akustisker Unreal/Wwise
I den här snabb starten ska du experimentera med Project-akustiska design kontroller genom att använda exempel innehåll för Unreal-motorn och Wwise.

Program varu krav för att använda exempel innehållet:
* [Unreal-motor](https://www.unrealengine.com/) 4,22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) -2019.1.2

## <a name="download-the-sample-package"></a>Ladda ned exempel paketet
Hämta [exempel paketet Project Akustiske Unreal och Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Exempel paketet innehåller:
- Unreal-motorns projekt
- Wwise-projekt för Unreal-projektet
- Plugin-programmet för Project Akustisker Wwise

## <a name="set-up-the-project-acoustics-sample-project"></a>Konfigurera projektets akustiska exempel projekt
Först installerar du plugin-programmet för Project-akustiskt i Wwise. Distribuera sedan Wwise-binärfilerna till Unreal-projektet. Justera sedan Wwise Unreal-plugin-programmet så att det stöder projekt Akustisker.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Installera plugin-programmet för Project Akustisker Wwise
Öppna Start programmet för Wwise. Välj **Lägg till från katalog**under **installera nya plugin-program**på fliken **plugin** -program. Välj den *AcousticsWwisePlugin\ProjectAcoustics* -katalog som ingår i paketet som du laddade ned.

![Alternativet för att installera plugin-programmet Wwise i Wwise Launcher](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Lägga till Wwise-binärfiler i projekt Akustiske Unreal-exempelprojektet
1. I Wwise Launcher väljer du fliken **Unreal motor** . 
1. Välj menyn "Hamburger" (ikon) bredvid de **senaste Unreal-motorns projekt**och välj sedan **Bläddra efter projekt**. Öppna filen sample Unreal Project *. uproject* i paketet *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Fliken Unreal i Wwise Launcher](media/wwise-unreal-tab.png)

3. Bredvid projektets akustiska exempel projekt väljer du **integrera Wwise i Project**.

   ![Wwise-Unreal visar projektet med akustiskt spel med alternativet integrera markerat.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Utöka funktioner för Wwise Unreal-plugin
Plugin-programmet för Project Akustiske Unreal behöver ytterligare beteende som exponeras från plugin-API: et för Wwise Unreal. Kör batch-filen som medföljde plugin-programmet för Project Akustiske Unreal för att automatisera dessa ändringar.
* I *AcousticsGame\Plugins\ProjectAcoustics\Resources*kör du *PatchWwise. bat*.

    ![Skriptet för att korrigera det Wwise-projekt som är markerat i ett fönster i Utforskaren](media/patch-wwise-script.png)

* Om du inte har installerat DirectX SDK: beroende på vilken version av Wwise du använder kan du behöva kommentera ut raden som innehåller `DXSDK_DIR` i *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Kod redigeraren visar "DXSDK"-kommentaren](media/directx-sdk-comment.png)

* Om du kompilerar med hjälp av Visual Studio 2019: för att undvika ett länknings fel med Wwise, ändra standardvärdet *VSVersion* i *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* till **vc150**manuellt:

    ![Kod redigeraren med VSVersion ändrades till "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Öppna Unreal-projektet 
När du öppnar Unreal-projektet kommer du att uppmanas att återskapa moduler. Välj **Ja**.

Om det inte går att öppna projektet på grund av build-fel, kontrol lera att du har installerat plugin-programmet för Project Akustiske Wwise till samma version av Wwise som användes i projektets akustiska exempel projekt.

Om du använder en version av [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) tidigare än version 2019,1 kan du inte skapa ljud banker med hjälp av projektets akustiska exempel projekt. Du måste integrera Wwise version 2019,1 i exempelprojektet.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimentera med design kontroller för projekt akustiskt
Lyssna på hur scenen låter genom att välja knappen Spela upp i Unreal-redigeraren. Använd W-, A-, S-och D-nycklarna och musen för att flytta runt. Om du vill se kortkommandon för ytterligare kontroller väljer du F1.

Följande information beskriver några design aktiviteter att testa.

### <a name="modify-occlusion-and-transmission"></a>Ändra ocklusion och överföring
Det finns projekt med projekt akustiskt per käll projekt på varje Unreal ljud aktör.

![Unreal-redigeraren akustiskr design kontroller](media/demo-scene-sound-source-design-controls.png)

Om **ocklusion** multiplikator är större än 1 (Standardvärdet är 1) är ocklusion exaggerated. En inställning på mindre än 1 gör ocklusion-effekterna mer diskreta.

Om du vill aktivera överföring via vägg flyttar du skjutreglaget för **överföring (dB)** bort från den lägsta nivån.

### <a name="modify-wetness-for-a-source"></a>Ändra wetness för en källa
Om du vill ändra hur snabbt wetness ändringar med avstånd använder du **perceptuellens avstånds tänjning**. Projekt akustiskt beräknar våta nivåer i hela utrymmet genom simulering. Nivåerna varierar smidigt med avstånd och ger en ikon för perceptuell avstånd. Om du vill exaggerate den här åtgärden ökar du avstånds nivåns våta nivåer för att öka avstånds förvrängningen. Om du tänjer värden på mindre än 1 blir den avstånds Reverberation mer diskret. Du kan också göra bättre justeringar av den här funktionen genom inställningen **wetness (dB)** .

Om du vill öka minsknings tiden i hela utrymmet justerar du **tids skalan för minskning**. Tänk dig ett fall där simulerings resultatet är en minsknings tid på 1,5 sekunder. Att ställa in **minsknings tids skalan** till 2 resulterar i en minsknings tid på 3 sekunder som tillämpas på källan.

### <a name="modify-distance-based-attenuation"></a>Ändra avstånds beroende dämpning
Plugin-programmet för Project akustisker Wwises mixer respekterar den avstånds dämpning per källa som är inbyggd i Wwise. Om du ändrar den här kurvan ändras den torra Sök vägs nivån. Plugin-programmet för projekt akustiskt justerar den våta nivån för att bibehålla den våta/torra mix som anges av kontrollerna för simulering och design.

![Den Wwise-dämpande kurv panelen visar att den är dämpad till 0 före simuleringens gränser](media/demo-sounds-attenuation.png)

Projekt akustiskt beräknas i en "simulerings region"-ruta som är centrerad runt varje simulerad Player-plats. De akustiska till gångarna i exempel paketet var bakade med en radie på 45 meter i en simulerings region. Dämpningar har utformats för att falla 0 före 45 meter. Även om den här utfallet inte är ett strikt krav, innebär det att bara geometrin inom 45 meter från lyssnaren kommer att occlude ljud.

## <a name="next-steps"></a>Nästa steg
* Integrera plugin-programmet [för projekt akustisker](unreal-integration.md) i Unreal-projektet.
* [Skapa ett Azure-konto](create-azure-account.md) för dina egna bagerier.
