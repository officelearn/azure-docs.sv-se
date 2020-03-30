---
title: Peering-tjänst – vanliga frågor och svar
titleSuffix: Azure
description: Peering-tjänst – vanliga frågor och svar
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775477"
---
# <a name="peering-service---faqs"></a>Peering-tjänst – vanliga frågor

Du kan granska informationen nedan för allmänna frågor.

**Kan en operatör som använder sin befintliga Direct-peering med Microsoft för att stödja peering-tjänsten?**

Ja, en operatör kan utnyttja sin befintliga PNI för att stödja Peering Service. En peering service PNI kräver mångfald för att stödja HA. Om befintlig PNI redan har mångfald krävs ingen ny infrastruktur. Om befintliga PNI behöver mångfald, då det kan utökas.

**Kan en operatör använda ny direkt peering med Microsoft för att stödja peering-tjänsten?**

Ja, det är också möjligt. Microsoft kommer att arbeta med Carrier för att skapa ny direkt peering för att stödja peering-tjänsten.  

**Varför är Direct peering ett krav för att stödja peering-tjänsten?**

En av de främsta drivkrafterna bakom Peering Service är att tillhandahålla anslutning till Microsofts onlinetjänster via ett välanslutet SP. PNI är alltid i Gbps sortiment och därmed en grundläggande byggsten för hög genomströmning anslutning mellan bärare och Microsoft.

**Vilka är mångfaldskraven på en direkt peering för att stödja peering-tjänsten?**

En PNI måste stödja lokal redundans och geo-redundans. Lokal redundans definieras som två olika sökvägar på en viss peering-plats. Geo-redundans kräver att Carrier har ytterligare anslutning på en annan Microsoft-kantplats om den primära platsen skulle misslyckas. För den korta felvaraktigheten kan transportören dirigera trafik genom säkerhetskopieringsplatsen.

**Transportören erbjuder redan SLA och enterprise grade Internet, hur är detta erbjudande annorlunda?**

Vissa operatörer erbjuder SLA och enterprise grade Internet på sin del av nätverket. I Peering Service erbjuder Microsoft SLA-erbjudandetrafik på Microsofts del av nätverket. Genom att välja Peering Service-kund får slut-till-slutSla. SLA från deras webbplats till Microsoft edge på ISP-nätverk kan omfattas av Isp. SLA i Microsoft Global Network från Microsoft edge till slutanvändare programmet omfattas nu av Microsoft.

**Om en tjänsteleverantör redan har med Microsoft med PNI, vilken typ av ändringar krävs för att stödja peering-tjänsten?**

* Programvara ändras för att identifiera en peering-tjänstanvändare och dess trafik. Kan kräva routningsprincipändringar för att utbyta en användares trafik vid närmaste Microsoft-kant via Peering Service-anslutning.
* Se till att anslutningen har lokal redundans och geo-redundans.
