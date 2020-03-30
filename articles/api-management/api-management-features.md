---
title: Funktionsbaserad jämförelse av Azure API Management-nivåerna | Microsoft-dokument
description: I den här artikeln jämförs API Management-nivåer baserat på de funktioner de erbjuder.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2019
ms.author: apimpm
ms.openlocfilehash: 2e84138419986ef1033ab076b3745187812e91b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335890"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Funktionsbaserad jämförelse av Azure API Management-nivåerna

Varje [API Management-prisnivå](https://aka.ms/apimpricing) erbjuder en separat uppsättning funktioner och per [enhetskapacitet](api-management-capacity.md). I följande tabell sammanfattas de nyckelfunktioner som är tillgängliga på var och en av nivåerna. Vissa funktioner kan fungera annorlunda eller ha olika funktioner beroende på nivån. I sådana fall anges skillnaderna i dokumentationsartiklarna som beskriver dessa enskilda egenskaper.

> [!IMPORTANT]
> Observera att utvecklarnivån är för icke-produktionsanvändningsfall och utvärderingar. Det erbjuder inte SLA.

| Funktion                                                                                      | Förbrukning | Developer | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integrering<sup>1</sup>                                                             | Inga          | Ja       | Inga    | Ja      | Ja     |
| Stöd för Virtuellt nätverk (VNet)                                                               | Inga          | Ja       | Inga    | Inga       | Ja     |
| Distribution i flera regioner                                                                      | Inga          | Inga        | Inga    | Inga       | Ja     |
| Flera anpassade domännamn                                                                 | Inga          | Inga        | Inga    | Inga       | Ja     |
| Utvecklarportal<sup>2</sup>                                                                 | Inga          | Ja       | Ja   | Ja      | Ja     |
| Inbyggd cache                                                                               | Inga          | Ja       | Ja   | Ja      | Ja     |
| Inbyggd analys                                                                           | Inga          | Ja       | Ja   | Ja      | Ja     |
| [Självvärd gateway](self-hosted-gateway-overview.md)<sup>3</sup>                           | Inga          | Ja       | Inga    | Inga       | Ja     |
| [TLS-inställningar](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Extern cache](https://aka.ms/apimbyoc)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Autentisering av klientcertifikat](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Säkerhetskopiera och återställa](api-management-howto-disaster-recovery-backup-restore.md)               | Inga          | Ja       | Ja   | Ja      | Ja     |
| [Hantering över Git](api-management-configuration-repository-git.md)                        | Inga          | Ja       | Ja   | Ja      | Ja     |
| API för direkt hantering                                                                        | Inga          | Ja       | Ja   | Ja      | Ja     |
| Azure Monitor loggar och mått                                                               | Inga          | Ja       | Ja   | Ja      | Ja     |
| Statisk IP                                                                                    | Inga          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> Möjliggör användning av Azure AD (och Azure AD B2C) som identitetsprovider för användarloggning på utvecklarportalen.<br/>
<sup>2</sup> Inklusive relaterade funktioner, t.ex.<br/>
<sup>3</sup> Begränsad till en enda självvärd gateway-distribution med en enda gateway-nod.<br/>
