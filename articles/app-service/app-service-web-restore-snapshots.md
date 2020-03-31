---
title: Återställa appen från en ögonblicksbild
description: Läs om hur du återställer appen från en ögonblicksbild. Återställ från oväntad dataförlust på Premium-nivå med automatiska skuggkopior.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: b17a49535541c8f75f65cdbe9986a895427f3877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255137"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Återställa en app i Azure från en ögonblicksbild
Den här artikeln visar hur du återställer en app i [Azure App Service](../app-service/overview.md) från en ögonblicksbild. Du kan återställa appen till ett tidigare tillstånd, baserat på en av appens ögonblicksbilder. Du behöver inte aktivera säkerhetskopiering av ögonblicksbilder, plattformen sparar automatiskt en ögonblicksbild av alla appar för dataåterställning.

Ögonblicksbilder är inkrementella skuggkopior, och de erbjuder flera fördelar jämfört med vanliga [säkerhetskopior:](manage-backup.md)
- Inga filkopieringsfel på grund av fillås.
- Ingen begränsning av lagringsstorleken.
- Ingen konfiguration krävs.

Återställning från ögonblicksbilder är tillgängligt för appar som körs på **Premium-nivå** eller högre. Information om hur du skalar upp din app finns [i Skala upp en app i Azure](manage-scale-up.md).

## <a name="limitations"></a>Begränsningar

- Funktionen är för närvarande i förhandsgranskning.
- Du kan bara återställa till samma app eller till en plats som tillhör den appen.
- AppTjänsten stoppar målappen eller målplatsen medan återställningen.
- App Service håller tre månader värde av ögonblicksbilder för plattform dataåterställning.
- Du kan bara återställa ögonblicksbilder under de senaste 30 dagarna.
- App Services som körs på en App Service-miljö stöder inte ögonblicksbilder.
 

## <a name="restore-an-app-from-a-snapshot"></a>Återställa en app från en ögonblicksbild

1. På sidan **Inställningar** i din app i [Azure-portalen](https://portal.azure.com)klickar du på **Säkerhetskopior** för att visa sidan **Säkerhetskopior.** Klicka sedan på **Återställ** under avsnittet **Ögonblicksbild(Förhandsgranska).**
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Markera ögonblicksbilden som ska återställas på sidan **Återställ.**
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Ange målet för appåterställning i **Återställ mål**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Om du väljer **Skriv över**raderas alla befintliga data i appens aktuella filsystem och skrivs över. Innan du klickar på **OK**kontrollerar du att det är det du vill göra.
   > 
   > 
      
   > [!Note]
   > På grund av aktuella tekniska begränsningar kan du bara återställa till appar i samma skalningsenhet. Den här begränsningen kommer att tas bort i en framtida version.
   > 
   > 
   
    Du kan välja **Befintlig app** som du vill återställa till en plats. Innan du använder det här alternativet bör du redan ha skapat en plats i appen.

4. Du kan välja att återställa webbplatskonfigurationen.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klicka på **OK**.
