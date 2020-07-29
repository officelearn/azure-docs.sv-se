---
title: Funktions-baserad jämförelse av Azure API Management-nivåerna | Microsoft Docs
description: I den här artikeln jämförs API Management nivåer baserat på de funktioner som de erbjuder.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: ef16339d9a584eb12c611a25d636a9287265953a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284344"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Funktions-baserad jämförelse av Azure API Management-nivåer

Varje API Management [pris nivå](https://aka.ms/apimpricing) erbjuder en distinkt uppsättning funktioner och per enhets [kapacitet](api-management-capacity.md). I följande tabell sammanfattas viktiga funktioner som är tillgängliga på varje nivå. Vissa funktioner kan fungera annorlunda eller ha olika funktioner beroende på nivån. I sådana fall kallas skillnaderna i dokumentations artiklarna som beskriver dessa enskilda funktioner.

> [!IMPORTANT]
> Observera att Developer-nivån är för användnings fall och utvärderings versioner som inte är i produktion. Den erbjuder inte service avtal.

| Funktion                                                                                      | Förbrukning | Utvecklare | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integrering<sup>1</sup>                                                             | Nej          | Ja       | Nej    | Ja      | Ja     |
| Stöd för Virtual Network (VNet)                                                               | Nej          | Ja       | Nej    | Nej       | Ja     |
| Distribution i flera regioner                                                                      | Nej          | Nej        | Nej    | Nej       | Ja     |
| Flera anpassade domän namn                                                                 | Nej          | Ja        | Nej    | Nej       | Ja     |
| Utvecklings Portal<sup>2</sup>                                                                 | Nej          | Ja       | Ja   | Ja      | Ja     |
| Inbyggd cache                                                                               | Nej          | Ja       | Ja   | Ja      | Ja     |
| Inbyggd analys                                                                           | Nej          | Ja       | Ja   | Ja      | Ja     |
| [Egen värd-Gateway](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nej          | Ja       | Nej    | Nej       | Ja     |
| [TLS-inställningar](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Extern cache](https://aka.ms/apimbyoc)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Autentisering av klient certifikat](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Säkerhetskopiering och återställning](api-management-howto-disaster-recovery-backup-restore.md)               | Nej          | Ja       | Ja   | Ja      | Ja     |
| [Hantering över git](api-management-configuration-repository-git.md)                        | Nej          | Ja       | Ja   | Ja      | Ja     |
| API för direkt hantering                                                                        | Nej          | Ja       | Ja   | Ja      | Ja     |
| Azure Monitor loggar och mått                                                               | Ja         | Ja       | Ja   | Ja      | Ja     |
| Statisk IP                                                                                    | Nej          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> gör det möjligt att använda Azure AD (och Azure AD B2C) som identitets leverantör för användare som loggar in på Developer-portalen.<br/>
<sup>2</sup> inklusive relaterade funktioner, t. ex. användare, grupper, problem, program och e-postmallar och meddelanden.<br/>
<sup>3</sup> i dina egen värdbaserade gatewayer för utvecklings nivån är begränsade till en enda Gateway-nod.<br/>
