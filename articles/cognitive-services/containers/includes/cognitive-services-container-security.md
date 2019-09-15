---
title: Containersäkerhet
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skyddar din behållare
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994925"
---
## <a name="azure-cognitive-services-container-security"></a>Säkerhet för Azure Cognitive Services-behållare

Säkerheten bör vara primärt fokuserad när du utvecklar program. Vikten av säkerhet är ett mått för framgång. När du skapar en program varu lösning som innehåller Cognitive Services behållare är det viktigt att du förstår de begränsningar och möjligheter som är tillgängliga för dig. Mer information finns i [Azure-säkerhet][az-security].

> [!IMPORTANT]
> Som standard finns det *Ingen säkerhet* för API: et för Cognitive Services container. Orsaken till detta är att behållaren ofta körs som en del av en pod som skyddas från utsidan av en nätverks brygga. Det är dock möjligt att aktivera autentisering som fungerar identiskt med autentiseringen som används vid åtkomst till den [molnbaserade Cognitive Services][request-authentication].

Diagrammet nedan visar standard och **osäker** Metod:

![Containersäkerhet](../media/container-security.svg)

Som ett alternativ och *säkert* tillvägagångs sätt kan förbrukare av Cognitive Services behållare utöka en behållare med en klient som är klient del, så att behållar slut punkten är privat. Vi ska ta en titt på ett scenario där vi använder [Istio][istio] som en ingress-Gateway. Istio stöder HTTPS/SSL och autentisering av klient certifikat. I det här scenariot exponeras behållar åtkomsten i Istio-frontend-filen, som presenterar klient certifikatet som vit listas i förväg med Istio.

[Nginx][nginx] är ett annat populärt val i samma kategori. Både Istio och Nginx fungerar som ett tjänst nät och erbjuder ytterligare funktioner, inklusive t. ex. belastnings utjämning, Routning och Rate-kontroll.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
