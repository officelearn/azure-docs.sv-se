---
title: Anslut Symantec ICDx data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Symantec ICDx data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 74169b4bd2654fb0ff7ec4cdb2f2b02c0f4cc6e8
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673756"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Ansluta din Symantec ICDx-installation 

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Symantec ICDx-anslutningsappen kan du enkelt ansluta alla dina säkerhetslösningen för Symantec med din Azure Sentinel, att visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Detta ger dig en överblick över nätverket och förbättrar din säkerhetsfunktioner för åtgärden. Integreringen mellan Symantec ICDx och Azure Sentinel gör användning av REST API.


> [!NOTE]
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurera och ansluta Symantec ICDx 

Symantec ICDx kan integrera och exportloggar direkt till Azure Sentinel.

1. Öppna hanteringskonsolen för ICDx för att lägga till Microsoft Azure Sentinel (Log Analytics) vidarebefordrare.
2. Klicka på navigeringsfältet ICDx **Configuration**. 
3. Överst på den **Configuration** klickar du på **vidarebefordrare**.
4. Under **vidarebefordrare**, bredvid Microsoft Azure Sentinel (Log Analytics), klickar du på **Lägg till**. 
4. I den **Microsoft Azure Sentinel (Log Analytics)** fönstret klickar du på **visa avancerade**. 
5. Överst på den utökade Microsoft Azure Sentinel (Log Analytics) kommer att göra följande:
    -   **Namn på**: Skriv ett namn för vidarebefordrare som har mer än 30 tecken. Välj ett unikt namn. Det här namnet visas i listan över vidarebefordrare på den **Configuration** skärmen och i instrumentpaneler på den **instrumentpanelen** skärmen. Exempel: Microsoft Azure Log Analytics östra. Det här fältet är obligatoriskt.
    -   **Beskrivning**: Ange en beskrivning för vidarebefordraren. Den här beskrivningen visas också i listan över vidarebefordrare på den **Configuration** skärmen. Innehåller information om vilken typ av händelse som vidarebefordras och gruppen som behöver granska data.
    -   **Starttyp för**: Välj metod för start för forwarder-konfiguration. Alternativen är manuell och automatisk.<br>Standardvärdet är automatisk. 
6. Under **händelser**, gör du följande: 
    - **Källa**: Välj en eller flera Arkiv som ska vidarebefordra händelser. Du kan välja active insamlaren Arkiv (inklusive vanliga arkivet), frånkopplade insamlaren Arkiv (det vill säga Arkiv för insamlare som du har tagit bort), ICDx mottagare Arkiv eller System-arkivet. <br>Standardvärdet är vanliga Arkiv.
      > [!NOTE]
      > ICDx mottagare Arkiv visas separat, efter namn. 
 
    - **Filtret**: Lägg till ett filter som anger delmängd av händelser som ska vidarebefordra. Gör något av följande:
        - För att välja ett filtervillkor, klickar du på en typ, attribut, Operator och värde. 
        - Granska dina filtervillkoret i fältet Filter. Du kan redigera direkt i fältet eller ta bort den efter behov.
        - Klicka på och eller eller lägga till i din filtervillkoret.
        - Du kan också klicka på Spara frågor för att tillämpa en sparad fråga.
    - **Inkluderade attribut**: Ange en kommaavgränsad lista med attribut som ska ingå i vidarebefordrade data. Inkluderade attribut har företräde framför exkluderade attribut.
    - **Exkluderade attribut**: Ange en kommaavgränsad lista med attribut som ska undantas från vidarebefordrade data.
    - **Batchstorlek**: Välj hur många händelser som ska skickas per batch. Alternativen är 10, 50, 100, 500 och 1000.<br>Standardvärdet är 100. 
    - **Hastighetsbegränsning**: Välj den hastighet med vilken händelser vidarebefordras, uttryckt i form av händelser per sekund. Alternativen är Unlimited, 500, 1000, 5000, 10000. <br> Standardvärdet är 5 000. 
7. Under **Azure mål**, gör du följande: 
    - **Arbetsyte-ID**: Klistra in arbetsyte-ID nedan. Det här fältet är obligatoriskt.
    - **Primär nyckel**: Klistra in den primära nyckeln nedan. Det här fältet är obligatoriskt.
    - **Anpassat loggnamn**: Ange den anpassade Loggnamnet i Microsoft Azure portal Log Analytics-arbetsytan som du ska vidarebefordra händelser. Standardvärdet är SymantecICDx. Det här fältet är obligatoriskt.
8. Klicka på *spara* Slutför forwarder-konfiguration. 
9. Starta vidarebefordrare, under **alternativ**, klickar du på **mer** och sedan **starta**.
10. Om du vill använda relevanta schemat i Log Analytics för Symantec ICDx-händelser, söka efter **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Symantec ICDx till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

