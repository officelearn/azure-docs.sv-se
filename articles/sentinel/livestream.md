---
title: Använd jakt liveström i Azure Sentinel för att identifiera hot | Microsoft Docs
description: Den här artikeln beskriver hur du använder jakt liveström i Azure Sentinel för att hålla reda på data.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77582134"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Använd jakt liveström i Azure Sentinel för att identifiera hot

> [!IMPORTANT]
> Jakt liveström i Azure Sentinel är för närvarande en offentlig för hands version och kan gradvis lanseras till klienter.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Använd jakt liveström för att skapa interaktiva sessioner som gör det möjligt att testa nyligen skapade frågor när händelser inträffar, få meddelanden från sessionerna när en matchning hittas och starta undersökningar vid behov. Du kan snabbt skapa en liveström-session med valfri Log Analytics fråga.

- **Testa nyligen skapade frågor när händelser inträffar**
    
    Du kan testa och justera frågor utan att det finns några konflikter i de aktuella reglerna som aktive ras för händelser. När du har bekräftat att dessa nya frågor fungerar som förväntat, är det enkelt att befordra dem till anpassade aviserings regler genom att välja ett alternativ som höjer sessionen till en avisering.

- **Få ett meddelande när hot uppstår**
    
    Du kan jämföra hot data flöden med sammanställda loggdata och bli meddelad när en matchning inträffar. Hot data matningar är pågående data strömmar som är relaterade till potentiella eller aktuella hot, så att meddelandet kan tyda på ett potentiellt hot mot din organisation. Skapa en liveström-session i stället för en anpassad aviserings regel om du vill få ett meddelande om ett potentiellt problem utan att de omkostnader som gäller för att underhålla en anpassad aviserings regel.

- **Starta utredningar**
    
    Om det finns en aktiv undersökning som omfattar en till gång, till exempel en värd eller användare, kan du visa vissa (eller alla) aktiviteter i loggdata som de inträffar på den till gången. Du kan få ett meddelande när aktiviteten sker.


## <a name="create-a-livestream-session"></a>Skapa en liveström-session

Du kan skapa en liveström-session från en befintlig jakt fråga eller skapa din session från grunden.

1. I Azure Portal går du till **kontroll** > **Threat Management** > **jakt**.

2. Så här skapar du en liveström-session från en jakt fråga:
    
    1. På fliken **frågor** letar du reda på den jakt fråga som du vill använda.
    2. Högerklicka på frågan och välj **Lägg till i liveström**. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Skapa liveström-session från Azure Sentinel jakt-fråga](./media/livestream/livestream-from-query.png)

3. Så här skapar du en liveström-session från grunden: 
    
    1. Välj fliken **liveström**
    2. Välj **gå till liveström**.
    
4. I fönstret **liveström** :
    
    - Om du har startat liveström från en fråga granskar du frågan och gör de ändringar du vill göra.
    - Om du startade liveström från grunden skapar du din fråga. 

5. Välj **spela upp** från kommando fältet.
    
    Statusfältet under kommando fältet visar om din liveström-session körs eller har pausats. I följande exempel körs sessionen:
    
    > [!div class="mx-imgBorder"]
    > ![Skapa liveström-session från Azure Sentinel-jakt](./media/livestream/livestream-session.png)

6. Välj **Spara** i kommando fältet.
    
    Om du inte väljer **pausa**fortsätter sessionen att köras tills du är utloggad från Azure Portal.

## <a name="view-your-livestream-sessions"></a>Visa dina liveström-sessioner

1. I Azure Portal navigerar du till fliken **Sentinel** > **Threat Management** > **jakt** > **liveström** .

2. Välj den liveström-session som du vill visa eller redigera. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Skapa liveström-session från Azure Sentinel jakt-fråga](./media/livestream/livestream-tab.png)
    
    Din valda liveström-session öppnas så att du kan spela upp, pausa, redigera och så vidare.

## <a name="receive-notifications-when-new-events-occur"></a>Ta emot meddelanden när nya händelser inträffar

Eftersom liveström-meddelanden för nya händelser använder Azure Portal aviseringar visas dessa meddelanden när du använder Azure Portal. Ett exempel:

![Azure Portal meddelande för liveström](./media/livestream/notification.png)

Välj meddelandet för att öppna fönstret **liveström** .
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Höj en liveström-session till en avisering

Du kan befordra en liveström-session till en ny avisering genom att välja **Höj avisering** från kommando fältet på den relevanta liveström-sessionen:

> [!div class="mx-imgBorder"]
> ![Öka liveström-sessionen till en avisering](./media/livestream/elevate-to-alert.png)

Den här åtgärden öppnar guiden Skapa regel som är förifylld med frågan som är associerad med liveström-sessionen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder jakt liveström i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- [Söker proaktivt efter hot](hunting.md)
- [Använd antecknings böcker för att köra automatiserade jakt kampanjer](notebooks.md)
