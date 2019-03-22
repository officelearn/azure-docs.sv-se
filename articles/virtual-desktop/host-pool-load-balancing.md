---
title: Värden pool belastningsutjämningsmetoder (förhandsversion), Azure
description: Värden pool belastningsutjämningsmetoder för en miljö för virtuella Windows-skrivbordet.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0a07406c8bad4ad0bef2949103d1f2c78e7dd8af
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318516"
---
# <a name="host-pool-load-balancing-methods"></a>Vara värd för poolen belastningsutjämningsmetoder

Virtuella Windows-skrivbordet (förhandsversion) stöder två metoder för belastningsutjämning. Varje metod avgör vilken värd för fjärrskrivbordssession ska vara värd för en användares session när de ansluter till en resurs i en pool för värden.

Följande metoder för belastningsutjämning är tillgängliga i virtuella Windows-skrivbordet:

- Bredden första belastningsutjämning kan du fördela användarsessioner på sessionen värdar i en pool med värden.
- Djup första belastningsutjämning kan du fylla en värd för fjärrskrivbordssession med användarsessioner i en pool med värden. När den första sessionen når sin session gränsen tröskeln, dirigerar belastningsutjämnaren alla nya användaranslutningar till nästa värd för fjärrskrivbordssession i värden poolen tills den når sin gräns och så vidare.

Varje värd-pool kan endast konfigurera en typ av belastningsutjämning specifika till den. Båda belastningsutjämning metoder dela följande beteenden oavsett vilken värd pool de kommer dock i:

- Om en användare redan har en session i poolen för värden och återansluter till den aktuella sessionen, omdirigerar belastningsutjämnaren har dem till värd för fjärrskrivbordssession med sina befintliga sessionen. Detta gäller även om den fjärrskrivbordssessioner AllowNewConnections egenskap är inställd på False.
- Om en användare inte redan har en session i poolen för värden, du inte belastningsutjämnaren session värdar vars AllowNewConnections-egenskap är inställd på False under Utjämning av nätverksbelastning.

## <a name="breadth-first-load-balancing-method"></a>Bredden första belastningsutjämningsmetod

Metoden bredden första belastningsutjämning kan du distribuera användaranslutningar att optimera för det här scenariot. Den här metoden är idealiskt för organisationer som vill ge bästa möjliga upplevelse för användare som ansluter till sin med virtuella skrivbord miljö.

Metoden bredden första frågar först session värdar som gör att nya anslutningar. Metoden väljer sedan värden för fjärrskrivbordssession med minst antal sessioner. Om det finns ett oavgjort resultat, väljer metoden den första värden för fjärrskrivbordssession i frågan.

## <a name="depth-first-load-balancing-method"></a>Djup första belastningsutjämningsmetod

Metoden djup första belastningsutjämning kan du fylla en värd åt gången för att optimera för det här scenariot. Den här metoden är perfekt för kostnadsmedvetna organisationer som vill ha mer detaljerad kontroll på antalet virtuella datorer som de har tilldelat för en värd-pool.

Metoden djup första frågar först session värdar med nya anslutningar och inte har gått över den längsta session. Metoden väljer sedan värden för fjärrskrivbordssession med högsta antal sessioner. Om det finns ett oavgjort resultat, väljer metoden den första värden för fjärrskrivbordssession i frågan.