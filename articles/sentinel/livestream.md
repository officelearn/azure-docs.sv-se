---
title: Använd jakt på Livestream i Azure Sentinel för att upptäcka hot| Microsoft-dokument
description: I den här artikeln beskrivs hur du använder jakt på Livestream i Azure Sentinel för att hålla reda på data.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582134"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Använd jakt livestream i Azure Sentinel för att upptäcka hot

> [!IMPORTANT]
> Jakt livestream i Azure Sentinel är för närvarande i offentlig förhandsversion och gradvis utrullning till klienter.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Använd jakt livestream för att skapa interaktiva sessioner som gör att du kan testa nyskapade frågor när händelser inträffar, få meddelanden från sessionerna när en matchning hittas och starta undersökningar om det behövs. Du kan snabbt skapa en livestream-session med valfri Log Analytics-fråga.

- **Testa nyskapade frågor när händelser inträffar**
    
    Du kan testa och justera frågor utan konflikter med aktuella regler som tillämpas aktivt på händelser. När du har bekräftat att dessa nya frågor fungerar som förväntat är det enkelt att befordra dem till anpassade varningsregler genom att välja ett alternativ som höjer sessionen till en avisering.

- **Få ett meddelande när hot uppstår**
    
    Du kan jämföra hotdataflöden med aggregerade loggdata och meddelas när en matchning inträffar. Hotdataflöden är pågående dataströmmar som är relaterade till potentiella eller aktuella hot, så meddelandet kan tyda på ett potentiellt hot mot din organisation. Skapa en livestream-session i stället för en anpassad varningsregel när du vill bli meddelad om ett potentiellt problem utan omkostnader för att upprätthålla en anpassad varningsregel.

- **Inleda utredningar**
    
    Om det finns en aktiv undersökning som involverar en tillgång, till exempel en värd eller användare, kan du visa specifik (eller valfri) aktivitet i loggdata som den inträffar på den tillgången. Du kan meddelas när aktiviteten inträffar.


## <a name="create-a-livestream-session"></a>Skapa en livestream-session

Du kan skapa en livestream-session från en befintlig jaktfråga eller skapa sessionen från grunden.

1. I Azure-portalen navigerar du till **Sentinel** > **Threat management** > **Hunting**.

2. Så här skapar du en livestream-session från en jaktfråga:
    
    1. Leta reda på den jaktfråga som ska användas på fliken **Frågor.**
    2. Högerklicka på frågan och välj **Lägg till i livestream**. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![skapa Livestream-session från Azure Sentinel-jaktfråga](./media/livestream/livestream-from-query.png)

3. Så här skapar du en livestream-session från grunden: 
    
    1. Välj fliken **Livestream**
    2. Välj **Gå till livestream**.
    
4. I **livestream-fönstret:**
    
    - Om du startade livestream från en fråga granskar du frågan och gör de ändringar du vill göra.
    - Om du startade livestream från grunden skapar du frågan. 

5. Välj **Spela upp** i kommandofältet.
    
    Statusfältet under kommandofältet anger om livestream-sessionen körs eller pausas. I följande exempel körs sessionen:
    
    > [!div class="mx-imgBorder"]
    > ![skapa livestream-session från Azure Sentinel-jakt](./media/livestream/livestream-session.png)

6. Välj **Spara** i kommandofältet.
    
    Om du inte väljer **Paus**fortsätter sessionen att köras tills du loggas ut från Azure-portalen.

## <a name="view-your-livestream-sessions"></a>Visa dina livestreamsessioner

1. Navigera till **Sentinel** > **Threat management** > **Hunting** > **Livestream** på fliken Azure-portalen.

2. Markera den livestream-session som du vill visa eller redigera. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![skapa livestream-session från Azure Sentinel-jaktfråga](./media/livestream/livestream-tab.png)
    
    Den valda livestream-sessionen öppnas så att du kan spela upp, pausa, redigera och så vidare.

## <a name="receive-notifications-when-new-events-occur"></a>Ta emot meddelanden när nya händelser inträffar

Eftersom livestream-meddelanden för nya händelser använder Azure-portalmeddelanden visas dessa meddelanden när du använder Azure-portalen. Ett exempel:

![Azure portal-avisering för livestream](./media/livestream/notification.png)

Välj meddelandet om du vill öppna **livestream-fönstret.**
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Höja en livestream-session till en avisering

Du kan befordra en livestream-session till en ny avisering genom att välja **Upphöja för att avisera** från kommandofältet i den relevanta livestream-sessionen:

> [!div class="mx-imgBorder"]
> ![Höja livestream-sessionen till en avisering](./media/livestream/elevate-to-alert.png)

Den här åtgärden öppnar guiden skapa regel, som är förifylld med frågan som är associerad med livestream-sessionen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder jakt livestream i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- [Proaktivt jakt efter hot](hunting.md)
- [Använda anteckningsböcker för att köra automatiserade jaktkampanjer](notebooks.md)
