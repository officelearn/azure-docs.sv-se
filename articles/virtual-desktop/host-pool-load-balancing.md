---
title: Belastningsutjämning för Windows Virtual Desktop-värdpool – Azure
description: Belastningsutjämningsmetoder för pool för en Windows Virtual Desktop-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127871"
---
# <a name="host-pool-load-balancing-methods"></a>Belastningsutjämningsmetoder för värdpool

Windows Virtual Desktop stöder två belastningsutjämningsmetoder. Varje metod avgör vilken sessionsvärd som ska vara värd för en användares session när de ansluter till en resurs i en värdpool.

Följande belastningsutjämningsmetoder är tillgängliga i Windows Virtual Desktop:

- Med bredd-första belastningsutjämning kan du fördela användarsessioner jämnt över sessionsvärdarna i en värdpool.
- Djup-första belastningsutjämning kan du mätta en sessionsvärd med användarsessioner i en värdpool. När den första sessionen når sitt tröskelvärde för sessionsgräns dirigerar belastningsutjämnaren alla nya användaranslutningar till nästa sessionsvärd i värdpoolen tills den når sin gräns och så vidare.

Varje värdpool kan bara konfigurera en typ av belastningsutjämning som är specifik för den. Båda belastningsutjämningsmetoderna delar dock följande beteenden oavsett vilken värdpool de befinner sig i:

- Om en användare redan har en session i värdpoolen och återansluter till den sessionen omdirigerar belastningsutjämnren dem till sessionsvärden med sin befintliga session. Det här problemet gäller även om sessionsvärdens egenskap AllowNewConnections är inställd på False.
- Om en användare inte redan har en session i värdpoolen kommer belastningsutjämnaren inte att ta hänsyn till sessionsvärdar vars egenskap AllowNewConnections är inställd på False under belastningsutjämning.

## <a name="breadth-first-load-balancing-method"></a>Bred-första belastningsutjämningsmetod

Med den breda-första belastningsutjämningsmetoden kan du distribuera användaranslutningar för att optimera för det här scenariot. Den här metoden är idealisk för organisationer som vill ge den bästa upplevelsen för användare som ansluter till sin poolade virtuella skrivbordsmiljö.

Den breda första metoden frågar först sessionsvärdar som tillåter nya anslutningar. Metoden väljer sedan sessionsvärden med minst antal sessioner. Om det finns en slips väljer metoden den första sessionsvärden i frågan.

## <a name="depth-first-load-balancing-method"></a>Djup-första belastningsutjämningsmetod

Med metoden djup-först load-balancing kan du mätta en sessionsvärd i taget för att optimera för det här scenariot. Den här metoden är idealisk för kostnadsmedvetna organisationer som vill ha mer detaljerad kontroll över antalet virtuella datorer som de har allokerat för en värdpool.

Metoden depth-first frågar först sessionsvärdar som tillåter nya anslutningar och inte har överskridit sin maximala sessionsgräns. Metoden väljer sedan den sessionsvärd som har det högsta antalet sessioner. Om det finns en slips väljer metoden den första sessionsvärden i frågan.