---
title: "Bästa praxis i registret för Azure-behållare"
description: "Lär dig hur du använder Azure-behållaren registret effektivt genom att följa dessa bästa praxis."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 9ec5573082dbf9de1288e1511f5041f8c20b416e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Metodtips för Azure-behållare registret

Genom att följa dessa säkerhetsmetoder kan du maximera prestanda och kostnadseffektiv användning av privat Docker registret i Azure.

## <a name="network-close-deployment"></a>Stäng nätverk distribution

Skapa behållaren registret i samma Azure-region där du distribuerar behållare. Placera registret i en region som är nätverket Stäng till din behållaren värdar kan sänka både svarstid och kostnader.

Nätverket Stäng distribution är en av de främsta orsakerna till för att använda ett privat behållaren register. Docker bilder har utmärkt [lager konstruktion](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) som gör det möjligt för stegvis distribution. Nya noder måste dock hämtar alla skikt som krävs för en viss bild. Den här första `docker pull` snabbt lägga till upp till flera gigabyte. Med ett privat register nära distributionen minimerar nätverkslatensen.
Dessutom implementerar alla offentliga moln, Azure ingår, nätverket utgång avgifter. Dra bilder från ett datacenter till en annan lägger till avgifter för utgående trafik, förutom svarstiden.

## <a name="geo-replicate-multi-region-deployments"></a>GEO-replikering flera regioner distributioner

Använda Azure Container registret [georeplikering](container-registry-geo-replication.md) funktion om du distribuerar behållare till flera regioner. Om du betjänar globala kunder från lokala Datacenter eller Utvecklingsteamet finns på olika platser, kan du förenkla hanteringen av registret och minimera fördröjning av geo-replikering i registret. För närvarande i förhandsgranskning för den här funktionen är tillgänglig med [Premium](container-registry-skus.md#premium) register.

Information om hur du använder geo-replikering, se självstudierna tre delar [Geo-replikering i Azure Container registret](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Databasen namnområden

Genom att använda databasen namnområden, kan du dela ett enda register över flera grupper i din organisation. Register kan delas mellan distributioner och team. Azure Container registret stöder kapslade namnområden, aktivera gruppisolering.

Tänk dig följande behållaren bildtaggen. Bilder som används företagsomfattande, som `aspnetcore`, placeras i rotnamnområdet, medan behållaren avbildningar som ägs av grupper för produktion och marknadsföring varje användning sina egna namnområden.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Dedikerad resursgruppen.

Eftersom behållare register är resurser som används på flera värdar i behållare, finnas ett register sin egen resursgruppen.

Även om du kan experimentera med en specifik värd-typ, till exempel Azure Behållarinstanser vill du förmodligen ta bort instansen av behållare när du är klar. Du kan dock också hålla mängd bilder som du pushas till registret för Azure-behållare. Genom att placera registret i sin egen resursgruppen kan minska du risken för oavsiktlig borttagning av mängd bilder i registret när du tar bort resursgruppen behållaren instans.

## <a name="authentication"></a>Autentisering

När autentisering med en Azure-behållaren registret, det finns två primära scenarier: enskilda autentisering och autentisering för tjänsten (eller ”fjärradministrerade”). Följande tabell innehåller en kort översikt över dessa scenarier och den rekommenderade metoden för autentisering för varje.

| Typ | Exempelscenario | Rekommenderad metod |
|---|---|---|
| Enskilda identitet | En utvecklare dra bilder till eller skicka bilder från sina utvecklingsdator. | [AZ acr inloggning](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Fjärradministrerade/tjänstidentitet | Bygg- och distributionsprocessen pipelines där användaren inte är direkt inblandade. | [Tjänstens huvudnamn](container-registry-authentication.md#service-principal) |

För detaljerad information om Azure-behållare registret autentisering Se [autentisera med en Azure-behållaren registret](container-registry-authentication.md).

## <a name="next-steps"></a>Nästa steg

Azure Container registret är tillgänglig i flera nivåer, kallas SKU: er, var och en innehåller olika funktioner. Mer information om tillgängliga SKU: er finns [Azure Container registret SKU: er](container-registry-skus.md).