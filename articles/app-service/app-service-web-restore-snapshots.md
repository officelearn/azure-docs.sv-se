---
title: Återställ en app i Azure
description: Lär dig hur du återställer din app från en ögonblicksbild.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.openlocfilehash: e1ae8fcc30323c865aa96937f43054515f293394
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Återställa en app i Azure från en ögonblicksbild
Den här artikeln visar hur du återställer en app i [Azure App Service](../app-service/app-service-web-overview.md) från en ögonblicksbild. Du kan återställa din app till ett tidigare tillstånd, baserat på en av appens ögonblicksbilder. Du behöver inte aktivera säkerhetskopiering av ögonblicksbilder, plattformen som sparar automatiskt en ögonblicksbild av alla appar för återställning i data.

Ögonblicksbilder är inkrementell skuggkopior och de erbjuder flera fördelar jämfört med vanlig [säkerhetskopieringar](web-sites-backup.md):
- Filkopieringsfel på grund av fillås.
- Inga storleksbegränsning för lagring.
- Ingen konfiguration krävs.

Återställning från ögonblicksbilder är tillgängliga för appar som körs **Premium** nivå eller högre. Information om skala upp appar finns [skala upp en app i Azure](web-sites-scale.md).

## <a name="limitations"></a>Begränsningar

- Funktionen är för närvarande under förhandsgranskning.
- Du kan bara återställa till appen med samma eller till en plats som hör till appen.
- Apptjänst stoppar mål appen eller plats för mål vid återställningen.
- Apptjänst behåller tre månaders ögonblicksbilder för återställning för plattformen data.
- Du kan bara återställa ögonblicksbilder under de senaste 30 dagarna.
 

## <a name="restore-an-app-from-a-snapshot"></a>Återställa en app från en ögonblicksbild

1. På den **inställningar** sidan i din app i den [Azure-portalen](https://portal.azure.com), klickar du på **säkerhetskopieringar** att visa den **säkerhetskopieringar** sidan. Klicka på **återställa** under den **Snapshot(Preview)** avsnitt.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. I den **återställa** väljer att återställa ögonblicksbilden.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Ange mål för app-återställning i **Återställningsmål**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Om du väljer **Skriv över**, alla befintliga data i din app aktuella filsystemet raderas och skrivas över. Innan du klickar på **OK**, se till att den är vad du vill göra.
   > 
   > 
      
   > [!Note]
   > På grund av nuvarande tekniska begränsningar kan återställa du bara till appar i samma skalningsenheten. Den här begränsningen ska tas bort i framtida versioner.
   > 
   > 
   
    Du kan välja **befintlig App** att återställa till en plats. Innan du använder det här alternativet måste bör du redan har skapat en plats i din app.

4. Du kan välja att återställa platskonfigurationen.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klicka på **OK**.
