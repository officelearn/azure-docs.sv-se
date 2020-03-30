---
title: Hämta drönarbilder
description: I den här artikeln beskrivs hur du hämtar drönarbilder från partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132056"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Få drönarbilder från drönarpartner

I den här artikeln beskrivs hur du kan ta in ortoosaiska data från dina drönarbilder till Azure FarmBeats Datahub. En ortoasisk är en flygillustration eller bild som är geometriskt korrigerad och sydd från data som samlats in av en drönare.

För närvarande stöds följande bildspråkspartner.

  ![FarmBeats drone bilder partner](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Genom att integrera drönarbilder med Azure FarmBeats kan du få ortoosaiska data från drönarflygningarna som du utför på din servergrupp i datahub. När data är tillgängliga kan du visa dem i FarmBeats Accelerator. Data kan användas för datafusion och artificiell intelligens och maskininlärningsmodellbyggnad.

## <a name="before-you-begin"></a>Innan du börjar

  - Se till att du har installerat Azure FarmBeats. Information om hur du installerar FarmBeats finns i [Installera Azure FarmBeats](install-azure-farmbeats.md).
  - Se till att du har den gård som du vill ha drönarbilder definierade i ditt FarmBeats-system.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Aktivera integrering av drönarbilder med FarmBeats

Ange följande information till din enhetsleverantör för att aktivera integrering med FarmBeats:
 - API-slutpunkt
 - Klient-ID:t
 - Klientorganisations-ID
 - Klienthemlighet

Följ de här stegen.

1. Ladda ner [skriptet](https://aka.ms/farmbeatspartnerscript)och extrahera det till din lokala enhet. Två filer finns i zip-filen.
2. Logga in i [Azure-portalen](https://portal.azure.com/) och öppna Azure Cloud Shell. Det här alternativet är tillgängligt i verktygsfältet i det övre högra hörnet på portalen.

    ![Öppna Azure Cloud Shell i portalens övre högra fält](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Kontrollera att miljön är inställd på **PowerShell**.

    ![PowerShell-inställning](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Ladda upp de två filer som du hämtade från steg 1 i Cloud Shell-instansen.

    ![Överföra filer](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Gå till katalogen där filerna laddades upp. Som standard överförs de till arbetskatalogen under användarnamnet.
6. Kör följande skript:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Följ anvisningarna på skärmen för att samla in värdena för API-slutpunkt, klient-ID, klient-ID, Klienthemlighet och EventHub-anslutningssträng.

    När du har angett de autentiseringsuppgifter som krävs i partnerns drönarprogramsystem kan du importera alla gårdar från FarmBeats-systemet. Sedan kan du använda gårdsinformationen för att planera flygbanan och drönarbildsamlingen.

    Efter att de råa bilderna bearbetats av drönarleverantörernas programvara laddar drönarprogramvaran upp de sydda ortoosaiska och andra bearbetade bilderna till datahub.

## <a name="view-drone-imagery"></a>Visa drönarbilder

När data har skickats till FarmBeats datahub kan du fråga scene store med hjälp av FarmBeats Datahub API:er.

Du kan också visa den senaste drönarbilden på sidan **Servergruppsinformation.** Följ stegen om du vill visa bilden.

1. Välj den servergrupp som bilderna laddades upp till. Sidan **Servergruppsinformation** visas.
2. Bläddra ned till det senaste avsnittet **Precision Maps.**
3. Visa bilden i avsnittet **Drone Imagery.**

    ![Avsnittet Drone Bilder](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Ladda ner drönarbilder

När du väljer avsnittet Drone Imagery öppnas ett popup-fönster för att visa en högupplöst bild av drönarortoosaic.

![Högupplöst ortoosaisk](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Visa alla drönarkartor

Filer och bilder som laddats upp av drönarleverantören visas i avsnittet **Kartor.** Markera avsnittet **Kartor,** filtrera efter **servergrupp**och välj lämpliga filer som ska visas och hämtas.

  ![Avsnittet Kartor](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder FarmBeats Datahub [API:er](rest-api-in-azure-farmbeats.md) för att hämta dina drönarbilder.
