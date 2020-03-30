---
title: Anslut Symantec ICDx-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Symantec ICDx-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588101"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Anslut din Symantec ICDx-apparat 



Med Symantec ICDx-anslutning kan du enkelt ansluta alla Symantec-säkerhetslösningsloggar med din Azure Sentinel, för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig mer insikt i organisationens nätverk och förbättrar dina funktioner för säkerhetsdrift. Integrering mellan Symantec ICDx och Azure Sentinel använder REST API.


> [!NOTE]
> Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurera och ansluta Symantec ICDx 

Symantec ICDx kan integrera och exportera loggar direkt till Azure Sentinel.

1. Öppna ICDx Management Console för att lägga till Microsoft Azure Sentinel (Log Analytics) vidarebefordrare.
2. Klicka på **Konfiguration**i ICDx-navigeringsfältet . 
3. Klicka på **Vidarebefordrare**högst upp på **konfigurationsskärmen** .
4. Klicka **Forwarders**på **Lägg**till bredvid Microsoft Azure Sentinel (Log Analytics) bredvid Microsoft Azure Sentinel (Log Analytics). 
4. Klicka på **Visa avancerat**i fönstret **Microsoft Azure Sentinel (Log Analytics).** 
5. Gör följande i fönstret Expanderat till Microsoft Azure Sentinel (Log Analytics):
    -   **Namn**: Skriv ett namn på vidarebefordraren som inte har fler än 30 tecken. Välj ett unikt, meningsfullt namn. Det här namnet visas i listan över vidarebefordrare på **konfigurationsskärmen** och i instrumentpanelerna på **instrumentpanelsskärmen.** Till exempel: Microsoft Azure Log Analytics East. Det här fältet är obligatoriskt.
    -   **Beskrivning**: Skriv en beskrivning för vidarebefordraren. Den här beskrivningen visas också i listan över vidarebefordrare på **konfigurationsskärmen.** Inkludera information som händelsetypen som vidarebefordras och den grupp som behöver granska data.
    -   **Starttyp:** Välj startmetod för vidarebefordrarens konfiguration. Alternativen är manuella och automatiska.<br>Standard är Automatisk. 
6. Gör följande under **Händelser:** 
    - **Källa**: Välj ett eller flera arkiv som du vill vidarebefordra händelser från. Du kan välja aktiva samlararkiv (inklusive det gemensamma arkivet), överblivna samlararkiv (det vill säga arkiv för de samlare som du har tagit bort), ICDx-arkiv eller systemarkivet. <br>Standardär Common Archive.
      > [!NOTE]
      > ICDx mottagare arkiv är listade separat, efter namn. 
 
    - **Filter**: Lägg till ett filter som anger den delmängd av händelser som ska vidarebefordras. Gör något av följande:
        - Om du vill välja ett filtervillkor klickar du på ett typ,attribut, operator och värde. 
        - Granska filtervillkoret i fältet Filter. Du kan redigera den direkt i fältet eller ta bort den efter behov.
        - Klicka på OCH eller ELLER om du vill lägga till filtervillkoret.
        - Du kan också klicka på Sparade frågor om du vill använda en sparad fråga.
    - **Inkluderade attribut:** Skriv den kommaavgränsade listan över attribut som ska inkluderas i vidarebefordrade data. De inkluderade attributen har företräde framför uteslutna attribut.
    - **Uteslutna attribut**: Skriv den kommaavgränsade listan över attribut som ska uteslutas från vidarebefordrade data.
    - **Batchstorlek:** Välj antalet händelser som ska skickas per batch. Alternativen är 10, 50, 100, 500 och 1000.<br>Standardvärdet är 100. 
    - **Hastighetsgräns**: Välj den hastighet med vilken händelser vidarebefordras, uttryckt som händelser per sekund. Dina alternativ är Obegränsad, 500, 1000, 5000, 10000. <br> Standardvärdet är 5000. 
7. Gör följande under **Azure Destination:** 
    - **Arbetsområdes-ID:** Klistra in arbetsyte-ID underifrån. Det här fältet är obligatoriskt.
    - **Primärnyckel:** Klistra in primärnyckeln underifrån. Det här fältet är obligatoriskt.
    - **Anpassat loggnamn:** Skriv det anpassade loggnamnet på arbetsytan Microsoft Azure Portal Log Analytics som du ska vidarebefordra händelser till. Standard är SymantecICDx. Det här fältet är obligatoriskt.
8. Klicka på *Spara* för att slutföra vidarebefordrarkonfigurationen. 
9. Om du vill starta vidarebefordraren klickar du på **Mer** under **Alternativ**och sedan **på Start**.
10. Om du vill använda det relevanta schemat i Log Analytics för Symantec ICDx-händelserna söker du efter **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta uppemot 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Symantec ICDx till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.


