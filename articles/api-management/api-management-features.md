---
title: Funktionsbaserad jämförelse av Azure API Management-nivåer | Microsoft Docs
description: Den här artikeln jämför API Management-nivåer beroende på vilka funktioner som de erbjuder.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: a57f8e44d19432f82abe4fa5e7bafce900db3394
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448013"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Funktionsbaserad jämförelse av Azure API Management-nivåer

Varje API Management [prisnivån](https://aka.ms/apimpricing) erbjuder en distinkt uppsättning funktioner och per enhet [kapacitet](api-management-capacity.md). I följande tabell sammanfattas de viktigaste funktionerna som är tillgängliga i var och en av nivåerna. Vissa funktioner kan fungera på olika sätt eller har olika funktioner beroende på vilken nivå. I sådana fall framhävs skillnaderna i för dokumentationsartiklar som beskriver dessa enskilda funktioner.

| Funktion                                                                                      | Förbrukning | Developer      | Basic          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Azure AD-integrering<sup>1</sup>                                                             | Nej                            | Ja            | Nej             | Ja            | Ja            |
| Stöd för Virtual Network (VNet)                                                               | Nej                            | Ja            | Nej             | Nej             | Ja            |
| Distribution i flera regioner                                                                      | Nej                            | Nej             | Nej             | Nej             | Ja            |
| Flera anpassade domännamn                                                                 | Nej                            | Nej             | Nej             | Nej             | Ja            |
| Developer-portalen<sup>2</sup>                                                                 | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Inbyggd cache                                                                               | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Inbyggda analyser                                                                           | Nej                            | Ja            | Ja            | Ja            | Ja            |
| [SSL-inställningar](api-management-howto-manage-protocols-ciphers.md)                             | Ja                            | Ja            | Ja            | Ja            | Ja            |
| [Externa cache](https://aka.ms/apimbyoc)                                                    | Ja                           | Ja            | Ja            | Ja            | Ja            |
| [Autentisering av klientcertifikat](api-management-howto-mutual-certificates-for-clients.md) | Ja                | Ja            | Ja            | Ja            | Ja            |
| [Säkerhetskopiering och återställning](api-management-howto-disaster-recovery-backup-restore.md)               | Nej                            | Ja            | Ja            | Ja            | Ja            |
| [Management via Git](api-management-configuration-repository-git.md)                        | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Direkthantering API                                                                        | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Azure Monitor-loggar och mått                                                               | Nej                | Ja            | Ja            | Ja            | Ja            |
| Statisk IP-adress                                                               | Nej                | Ja            | Ja            | Ja            | Ja            |

<sup>1</sup> kan du använda Azure AD (och Azure AD B2C) som en identitet provider för användaren logga in på utvecklarportalen.<br/>
<sup>2</sup> , inklusive relaterade funktioner t.ex. användare, grupper, problem, program och e-postmallar och meddelanden.<br/>
