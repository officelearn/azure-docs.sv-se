---
title: Project Acoustics Snabbstart med Unity
titlesuffix: Azure Cognitive Services
description: Använd exempelinnehåll för att experimentera med designkontroller av Projektakustik i Unity och distribuera till Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243021"
---
# <a name="project-acoustics-unity-quickstart"></a>Snabbstart för Projektakustik Unity
Använd exempelinnehåll för Project Acoustics för Unity för att experimentera med simuleringsstödda designkontroller.

Programvarukrav:
* [Unity 2018.2+](https://unity3d.com) för Windows
* [Exempelinnehållspaket för projektakustik](https://www.microsoft.com/download/details.aspx?id=57346)

Vad ingår i provpaketet?
* Unity scen med geometri, ljudkällor och gameplay kontroller
* Plug-in-program för projekt akustik
* Bakade akustik tillgångar för scenen

## <a name="import-the-sample-package"></a>Importera exempelpaketet
Importera exempelpaketet till ett nytt Unity-projekt.
1. I Unity går du till**Anpassat paket**för**importpaket** >  **för tillgångar** > .

    ![Alternativen för enhetsimportpaket](media/import-package.png)  

1. Välj **ProjectAcoustics.unitypackage**.

1. Välj knappen **Importera** om du vill integrera Unity-paketet i projektet.  
  
    ![Dialogrutan Enhetsimportpaket](media/import-dialog.png)  

Information om hur du importerar paketet till ett befintligt projekt finns i [Unity-integrering](unity-integration.md) för ytterligare steg och anteckningar.

>[!NOTE]
>Flera felmeddelanden visas i konsolloggen när importen är klar. Fortsätt till nästa steg och starta om Unity.

## <a name="restart-unity"></a>Starta om Unity
Den baka delen av akustik verktygslåda kräver .NET 4. *x* skriptkörningsversion. Paketimporten uppdaterar inställningarna för Unity-spelare. Starta om Unity för att den här inställningen ska börja gälla. Om du vill kontrollera att inställningen trädde i kraft öppnar du **inställningarna för Player:**

![Menyn Inställningar för Enhetsprojekt](media/player-settings.png)  

![Panelen Inställningar för Enhetsspelare med .NET 4.x markerat](media/net45.png)  

>[!NOTE]
>Den här skärmdumpen togs från Unity 2018. *x*. Bilden kan skilja sig åt i nyare versioner av Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Öppna fönstret Projektakustik baka
Välj **Akustik** på **fönstermenyn** i Unity.

![Unity-redigeraren med alternativet Akustik markerat på fönstermenyn](media/window-acoustics.png)

Ett flytande **akustikfönster** öppnas. Det här fönstret är där du ställer in egenskaperna för den akustiska simuleringen.

![Unity-redaktören med Akustikfönstret öppet](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Experimentera med designkontrollerna
Öppna exempelscenen i mappen *ProjectAcousticsSample* och välj uppspelningsknappen i Unity-redigeraren. Använd knapparna W, A, S och D och musen för att flytta runt. Om du vill jämföra hur scenen låter med och utan akustik markerar du R-tangenten tills övertäckningstexten blir röd och visar "Akustik: Inaktiverad". Om du vill visa kortkommandon för fler kontroller väljer du F1. Du kan också högerklicka för att välja en åtgärd och sedan vänsterklicka för att göra den åtgärden.

*AcousticsAdjust-skriptet* är kopplat till ljudkällorna i exempelscenen. Det aktiverar designparametrarna per källa.

![Unity AcousticsAdjust-manuset](media/acoustics-adjust.png)

I följande avsnitt beskrivs några av de effekter som du kan skapa med hjälp av tillgängliga kontroller. Detaljerad information om varje kontroll finns i [Project Acoustics Unity Design Tutorial](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Ändra avståndsbaserad dämpning
Den digitala ljudsignalbehandlingen i **Project Acoustics** Unity-spatializer-plugin-programmet respekterar den avståndsbaserade dämpning per källa som är inbyggd i Unity-redigeraren. Kontrollerna för avståndsbaserad dämpning finns i komponenten **Ljudkälla,** som finns på **panelen Inspektör** med ljudkällor under **3D-ljudinställningar:**

![Panelen Alternativ för enhetsavstämningsalternativ](media/distance-attenuation.png)

Project Acoustics beräknar i en "simuleringsregion" som är centrerad kring spelarens plats. Akustiktillgångarna i provpaketet bakades vid en simuleringsregion på 45 meter runt spelaren. Så bör ljuddämpningen utformas så att den sjunker till 0 vid ca 45 m.

### <a name="modify-occlusion-and-transmission"></a>Ändra ocklusion och överföring
* Om **Ocklusionsmultiplikatorn** är större än 1 (standardvärdet är 1) överdrivs ocklusionen. Om du vill göra ocklusionseffekten mer subtil ställer du in den på mindre än 1.

* Om du vill aktivera växellåda flyttar du skjutreglaget **Transmission (dB)** bort från den lägsta inställningen.

### <a name="modify-wetness-for-a-source"></a>Ändra väta för en källa
* Om du vill ändra hur snabbt vätan förändras med avstånd använder du **Perceptuell distansförskjutning**. Genom simulering beräknar Project Acoustics våta nivåer, som ger perceptuella avståndssignaler och varierar smidigt med avstånd. Öka avståndet varp överdriver denna effekt genom att öka avstånd-relaterade våtnivåer. Warping värden på mindre än 1 gör avstånd-baserade efterklang ändras mer subtila.

   Ändra inställningen **Wetness (dB)** om du vill göra finare justeringar av den här effekten.

* Om du vill öka förfallstiden i hela utrymmet justerar du **förfallningstidsskalan**. Om simuleringsresultatet för ett visst källavlyssnare platspar är en sönderfallstid på 1,5 sekunder och **förfallningstidsskalan** är inställd på 2, är förfalltiden som tillämpas på källan 3 sekunder.

## <a name="next-steps"></a>Nästa steg
* Läs information om [unity-baserade designkontroller för projektakustik](unity-workflow.md).
* Ytterligare utforska begreppen bakom [designprocessen](design-process.md).
* [Skapa ett Azure-konto](create-azure-account.md) för att utforska förbaka och baka processer.
