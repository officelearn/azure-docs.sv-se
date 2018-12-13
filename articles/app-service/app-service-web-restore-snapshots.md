---
title: Återställ från säkerhetskopia - Azure App Service
description: Lär dig mer om att återställa din app från en ögonblicksbild.
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
ms.custom: seodec18
ms.openlocfilehash: 174fbd8f3e4c652c25a78911185ef8a98a80bb50
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260046"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Återställ en app i Azure från en ögonblicksbild
Den här artikeln visar hur du återställer en app i [Azure App Service](../app-service/app-service-web-overview.md) från en ögonblicksbild. Du kan återställa din app till ett tidigare tillstånd, baserat på en av ögonblicksbilder av din app. Du behöver inte aktivera säkerhetskopiering av ögonblicksbilder, plattformen sparas automatiskt en ögonblicksbild av alla appar för dataåterställningsändamål.

Ögonblicksbilder är inkrementell skuggkopior och de erbjuder flera fördelar jämfört med vanliga [säkerhetskopior](web-sites-backup.md):
- Filkopieringsfel på grund av fillås.
- Ingen begränsning för storage storlek.
- Ingen konfiguration krävs.

Återställning från ögonblicksbilder är tillgänglig för appar som körs i **Premium** nivå eller högre. Information om att skala upp appar finns i [skala upp en app i Azure](web-sites-scale.md).

## <a name="limitations"></a>Begränsningar

- Funktionen är för närvarande i förhandsversion.
- Du kan bara återställa till samma app eller till en plats som hör till appen.
- App Service stoppar målappen eller målplatsen när du gör återställningen.
- App Service håller tre månaders ögonblicksbilder för plattformen för dataåterställningsändamål.
- Du kan bara återställa ögonblicksbilder för de senaste 30 dagarna.
 

## <a name="restore-an-app-from-a-snapshot"></a>Återställa en app från en ögonblicksbild

1. På den **inställningar** sidan för din app i den [Azure-portalen](https://portal.azure.com), klickar du på **säkerhetskopior** att visa den **säkerhetskopior** sidan. Klicka sedan på **återställa** under den **Snapshot(Preview)** avsnittet.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. I den **återställa** väljer att återställa ögonblicksbilden.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Ange mål för app-återställning i **databasfilens Återställningsmål**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Om du väljer **överskrivning**, alla befintliga data i din app aktuella filsystemet raderas och skrivas över. Innan du klickar på **OK**, se till att det är vad du vill göra.
   > 
   > 
      
   > [!Note]
   > På grund av nuvarande tekniska begränsningar kan återställa du bara till appar i samma skalningsenheten. Den här begränsningen tas bort i kommande versioner.
   > 
   > 
   
    Du kan välja **befintlig App** att återställa till en plats. Innan du använder det här alternativet kan bör du redan har skapat en plats i din app.

4. Du kan välja att återställa din plats-konfiguration.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klicka på **OK**.
