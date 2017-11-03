---
title: "Verklig användare mått i Azure Traffic Manager | Microsoft Docs"
description: "Introduktion till användare mått i Traffic Manager"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: a7e8ae605b6234341d9ab8b790f4c54d8627f29f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager verkliga användaren mätningar översikt

>[!NOTE]
>Funktionen verkliga användaren mått i Traffic Manager finns i Public Preview och kanske inte har samma nivå av tillgänglighet och tillförlitlighet som viktiga funktioner som är i allmänhet tillgänglighet. Funktionen stöds inte, kan ha begränsad kapacitet och kanske inte tillgänglig på alla platser i Azure. Den senaste meddelanden på tillgänglighet och status för den här funktionen, kontrollera den [Azure Traffic Manager uppdaterar](https://azure.microsoft.com/updates/?product=traffic-manager) sidan.

När du ställer in en Traffic Manager att använda routningsmetoden för prestanda tittar tjänsten på där DNS-frågor kommer från och fattas beslut om routning att dirigera dessa beställare till Azure-region som ger dem den lägsta fördröjningen. Detta åstadkoms genom att använda nätverket latens intelligence som Traffic Manager underhåller för olika slutanvändaren nätverk.

Verkliga användaren mått kan du mäta nätverket latens mått till Azure-regioner, från klientprogram som dina slutanvändare använder och har Traffic Manager Överväg att information samt när de fattar beslut om routning. Genom att välja att använda de verkliga mått som användare kan öka du noggrannheten för routning för begäranden som kommer från dessa nätverk där användarna finns. 

## <a name="how-real-user-measurements-work"></a>Så här fungerar verkliga användaren mått

Verklig användare mätningar fungerar genom att låta klienten program mått fördröjningen till Azure-regioner som visas från slutanvändaren-nätverk där de används. Exempelvis om du har en webbsida som kan nås av användare olika platser (till exempel i Nordamerika regioner), kan du utnyttja kraften i verkliga användaren mått när du använder routningsmetoden för prestanda för att få dem till den bästa Azure-regionen i som är värd för serverprogrammet.

Startar det genom att bädda in en Azure angivna JavaScript (med en unik nyckel på den) på webbsidorna. När det är klart, när en användare besöker webbsidan, frågar Traffic Manager för att få information om Azure-regioner som den ska mäta JavaScript. Tjänsten returnerar en uppsättning slutpunkter till skriptet som sedan mått för dessa områden i följd genom att hämta en bildpunkt avbildning finns i de Azure-regionerna och notera fördröjningar mellan tiden begäran skickades och den tid då den första byten togs emot . Dessa mått rapporteras sedan tillbaka till Traffic Manager-tjänsten.

Det händer flera gånger över tid, och i många nätverk som leder till Traffic Manager komma exaktare information om svarstid för nätverk som dina slutanvändare finns. Den här informationen startar komma ska ingå i routningsbeslut som gjorts av Traffic Manager. Därför leder till att bättre noggrannhet i dessa beslut baserat på verkliga användaren mätningar skickas.

När du använder verkliga användaren mätningar debiteras baserat på antalet mått som skickas till Traffic Manager. Mer information om prissättning, finns det [Traffic Manager sida med priser](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du använder [verkliga användaren mått med webbsidor](traffic-manager-create-rum-web-pages.md)
- Läs [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Lär dig mer om [Center Mobile](https://docs.microsoft.com/mobile-center/)
- Lär dig mer om den [routning av nätverkstrafik metoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

