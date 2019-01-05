---
title: Faktiska Användarmätningar i Azure Traffic Manager
description: Introduktion till mätning i Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: fd37ef739522955ae8227db39a41aecf199d65c3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052827"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager Real User Measurements översikt

När du ställer in en Traffic Manager-profil att använda routningsmetod för prestanda tittar tjänsten på där DNS-frågor kommer från och gör beslut om Routning och dirigera dessa begärande till Azure-region som ger dem den lägsta svarstiden. Detta åstadkoms genom att använda nätverket svarstid-intelligens som Traffic Manager har för olika slutanvändarens nätverk.

Real User Measurements kan du mäta svarstiden nätverksmätningar till Azure-regioner, från klientprogram som dina slutanvändare använder och har Traffic Manager övervga den informationen också när de fattar beslut om routning. Genom att välja att använda Real User Measurements, kan du öka noggrannheten för routning för begäranden som kommer från dessa nätverk där användarna finns. 

## <a name="how-real-user-measurements-work"></a>Hur Real User Measurements fungerar

Real User Measurements fungerar genom att låta klienten program mått fördröjningen till Azure-regioner som observeras från slutanvändarens nätverk där de används. Till exempel om du har en webbsida som används av användarna mellan olika platser (till exempel i nordamerikanska regioner), du kan använda Real User Measurements med routningsmetod för prestanda för att göra dem till den bästa Azure-regionen där din server är värd för programmet.

Den startar genom att bädda in en Azure angivna JavaScript (med en unik nyckel i den) på dina webbsidor. När det är klart, när en användare besöker webbsidan, frågar Traffic Manager för att få information om Azure-regioner den värdera i JavaScript. Tjänsten returnerar en uppsättning slutpunkter till skriptet som sedan måttet dessa regioner flera gånger i följd genom att ladda ned en bildpunkt avbildning finns i dessa Azure-regioner och kontrollera fördröjning mellan tiden begäran har skickats och den tid då den första byten togs emot . De här mätningarna rapporteras sedan tillbaka till Traffic Manager-tjänsten.

Detta sker ofta med tiden, och över många nätverk som leder till Traffic Manager få exaktare information om egenskaperna svarstid för nätverk som dina slutanvändare finns. Den här informationen startar komma som ska ingå i dirigeringsbesluten som fattas i Traffic Manager. Därför kan leder det till större noggrannhet i dessa beslut baserat på faktisk Slutanvändarmätning skickas.

När du använder Real User Measurements, debiteras du baserat på mätningarna skickas till Traffic Manager. Mer information om priserna på den [Traffic Manager-sidan med priser](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du använder [Real User Measurements med webbsidor](traffic-manager-create-rum-web-pages.md)
- Lär dig [så här fungerar Traffic Manager](traffic-manager-overview.md)
- Läs mer om [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Läs mer om den [trafikroutningsmetoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

