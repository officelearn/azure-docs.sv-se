---
title: Hämta drönare-bilder
description: Beskriver hur du hämtar drönare-bilder från partnerna
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 598248a0efb3322a9c22a5e38e4986f5ba5142ab
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798458"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Hämta drönare-bilder från drönare-partners

Den här artikeln beskriver hur du kan hämta orthomosaic-data från dina drönare bilder-partner i till Azure FarmBeats data Hub. För närvarande stöds följande bilder-partner:  

  ![Taktslag i projekt grupp](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Genom att integrera drönare bilder-data med Azure FarmBeats kan du få orthomosaic-data från drönare-flygningar som du utför i Server gruppen i data hubben. När data är tillgängliga kan du visa dem i FarmBeats-acceleratorn och det kan användas för data fusion och AI/ML-modell.

## <a name="before-you-begin"></a>Innan du börjar

  - Kontrol lera att du har distribuerat Azure-FarmBeats. Om du vill distribuera kan du besöka [distribuera FarmBeats](prepare-for-deployment.md).
  - Se till att du har den server grupp (för vilken du vill ha drönare bilder) som definierats i FarmBeats-systemet.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Aktivera drönare bilder-integrering med FarmBeats   

Du måste ange följande information för din enhets leverantör för att kunna integrera med FarmBeats:  
 - API-slutpunkt  
 - Klient-ID:t  
 - Klientorganisations-ID  
 - Klient hemlighet  

Använd följande steg:

1. Hämta det här [skriptet](https://aka.ms/farmbeatspartnerscript) och extrahera det i på den lokala enheten. Du hittar två filer i den här ZIP-filen.  
2. Logga in på [Azure Portal](https://portal.azure.com/) och öppna Cloud Shell (det här alternativet finns i det övre högra fältet i portalen).   

    ![Taktslag i projekt grupp](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Se till att miljön är inställd på **PowerShell**

    ![Taktslag i projekt grupp](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Ladda upp de två filerna som du laddade ned (från steg 1 ovan) i din Cloud Shell.  

    ![Taktslag i projekt grupp](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Gå till den katalog där filerna överfördes. (Som standard laddas den upp till arbets katalogen > användar namn.)  
6. Kör följande skript:

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```

7. Följ anvisningarna på skärmen för att avbilda värdena för API-slutpunkt, klient-ID, klient-ID, klient hemlighet och EventHub-anslutningssträng.

    När du har angett de autentiseringsuppgifter som krävs i partnerns drönare-programsystem kommer du att kunna importera alla Server grupper från FarmBeats-systemet och använda Server grupps informationen för att planera och drönare-bildsamlingen i flyg banan.

    När RAW-avbildningarna har bearbetats av drönare-leverantörens program vara laddar drönare-programvaran upp de häftda orthomosaic och andra bearbetade avbildningar till datahubben.

## <a name="view-drone-imagery"></a>Visa drönare bilder

När data har skickats till FarmBeats-datahubben bör du kunna söka i scen lagret med hjälp av API: er för data hubben i FarmBeats.

Alternativt bör du kunna visa den senaste drönare-avbildningen på **Server grupps informations** sidan. Följ stegen nedan om du vill visa:  

1. Välj den server grupp som din bilder har laddats upp till. Sidan **Server grupp** information visas.
2. Rulla ned till det senaste **precision Maps** -avsnittet.
3. Du bör kunna visa bilden i **drönare bilder** -avsnittet.

    ![Taktslag i projekt grupp](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Ladda ned drönare bilder

När du väljer avsnittet drönare bilder öppnas ett popup-fönster för att visa en hög upplöst bild av drönare-orthomosaic.

![Taktslag i projekt grupp](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Visa alla drönare Maps

Filer och avbildningar som laddats upp av drönare-providern visas i avsnittet Maps. Välj avsnittet **Maps** , filtrera efter **Server grupp** och Välj lämpliga filer att visa och ladda ned:

  ![Taktslag i projekt grupp](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder FarmBeats- [API: er](references-for-farmbeats.md#rest-api) för data hubb för att hämta drönare-bilder.
