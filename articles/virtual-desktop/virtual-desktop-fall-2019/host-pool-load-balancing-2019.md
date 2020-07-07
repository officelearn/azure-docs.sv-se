---
title: Belastnings utjämning för Windows-adresspoolen för virtuella skriv bord – Azure
description: Belastnings Utjämnings metoder för värdbaserade pooler för en Windows-miljö med virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2bfbd580d0e114cf4a135879340745107183b6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614816"
---
# <a name="host-pool-load-balancing-methods"></a>Belastningsutjämningsmetoder för värdpool

>[!IMPORTANT]
>Det här innehållet gäller för hösten 2019-versionen som inte stöder Azure Resource Manager virtuella Windows Desktop-objekt. Om du försöker hantera Azure Resource Manager virtuella Windows-skrivbordet som introduceras i våren 2020-uppdateringen, se [den här artikeln](../host-pool-load-balancing.md).

Windows Virtual Desktop stöder två metoder för belastnings utjämning. Varje metod bestämmer vilken värddator som ska vara värd för en användares session när de ansluter till en resurs i en adresspool.

Följande belastnings Utjämnings metoder är tillgängliga i Windows Virtual Desktop:

- Med bredd-första belastnings utjämning kan du jämnt distribuera användarsessioner över sessionens värdar i en adresspool.
- Djup – med belastnings utjämning kan du fylla en session-värd med användarsessioner i en adresspool. När den första sessionen når gränsen för antalet sessioner dirigerar belastningsutjämnaren alla nya användar anslutningar till nästa session-värd i adresspoolen tills den når sin gräns, och så vidare.

Varje adresspool kan bara konfigurera en typ av belastnings utjämning som är speciell för den. Både belastnings Utjämnings metoder delar däremot följande beteenden oavsett vilken pool de befinner sig i:

- Om en användare redan har en session i värddatorn och återansluter till den sessionen, kommer belastningsutjämnaren att omdirigera dem till sessionens värd med sin befintliga session. Detta gäller även om den aktuella AllowNewConnections-egenskapen har angetts till false.
- Om en användare inte redan har en session i poolen, kommer belastningsutjämnaren inte att anse ras-värdar vars AllowNewConnections-egenskap är inställd på falskt under belastnings utjämning.

## <a name="breadth-first-load-balancing-method"></a>Bredd – första belastnings Utjämnings metod

Med den bredd-första belastnings Utjämnings metoden kan du distribuera användar anslutningar så att de optimeras för det här scenariot. Den här metoden är idealisk för organisationer som vill ge den bästa upplevelsen av användare som ansluter till sin poolbaserade virtuella Skriv bords miljö.

Metoden vidd-First frågar först efter de sessioner som tillåter nya anslutningar. Metoden väljer sedan värd för sessionen med minst antal sessioner. Om det finns en förbindelse väljer metoden den första sessionens värd i frågan.

## <a name="depth-first-load-balancing-method"></a>Djup-första belastnings Utjämnings metod

Med den första belastnings Utjämnings metoden kan du fylla en session-värd i taget för att optimera för det här scenariot. Den här metoden är idealisk för kostnadsmedvetna organisationer som vill ha mer detaljerad kontroll över antalet virtuella datorer som de har allokerat för en värd pool.

Den första metoden frågar efter de sessioner som är värdar som tillåter nya anslutningar och som inte har varit över gränsen för den högsta tillåtna sessionen. Metoden väljer sedan Session Host med det högsta antalet sessioner. Om det finns en förbindelse väljer metoden den första sessionens värd i frågan.