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
ms.openlocfilehash: eae36aa6e60e3da03c59952a1d9e035e6a773d2d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156705"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Funktionsbaserad jämförelse av Azure API Management-nivåer

Varje API Management [prisnivån](https://aka.ms/apimpricing) erbjuder en distinkt uppsättning funktioner och per enhet [kapacitet](api-management-capacity.md). I följande tabell sammanfattas de viktigaste funktionerna som är tillgängliga i var och en av nivåerna. Vissa funktioner kan fungera på olika sätt eller har olika funktioner beroende på vilken nivå. I sådana fall framhävs skillnaderna i för dokumentationsartiklar som beskriver dessa enskilda funktioner.

| Funktion                                                                                      | Förbrukning<sup>FÖRHANDSVERSION</sup> | Developer      | Basic          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Azure AD-integrering<sup>1</sup>                                                             | Nej                            | Ja            | Nej             | Ja            | Ja            |
| Stöd för Virtual Network (VNet)                                                               | Nej                            | Ja            | Nej             | Nej             | Ja            |
| Distribution i flera regioner                                                                      | Nej                            | Nej             | Nej             | Nej             | Ja            |
| Flera anpassade domännamn                                                                 | Nej                            | Nej             | Nej             | Nej             | Ja            |
| Developer-portalen<sup>2</sup>                                                                 | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Inbyggd cache                                                                               | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Inbyggda analyser                                                                           | Nej                            | Ja            | Ja            | Ja            | Ja            |
| [SSL-inställningar](api-management-howto-manage-protocols-ciphers.md)                             | Nej                            | Ja            | Ja            | Ja            | Ja            |
| [Externa cache](https://aka.ms/apimbyoc)                                                    | Ja                           | Inte<sup>3</sup> | Inte<sup>3</sup> | Inte<sup>3</sup> | Inte<sup>3</sup> |
| [Autentisering av klientcertifikat](api-management-howto-mutual-certificates-for-clients.md) | Inte<sup>4</sup>                | Ja            | Ja            | Ja            | Ja            |
| [Säkerhetskopiering och återställning](api-management-howto-disaster-recovery-backup-restore.md)               | Nej                            | Ja            | Ja            | Ja            | Ja            |
| [Management via Git](api-management-configuration-repository-git.md)                        | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Direkthantering API                                                                        | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Azure Monitor-loggar och mått                                                               | Inte<sup>5</sup>                | Ja            | Ja            | Ja            | Ja            |

<sup>1</sup> kan du använda Azure AD (och Azure AD B2C) som en identitetsleverantör för användarinloggning på developer-portalen.<br/>
<sup>2</sup> , inklusive relaterade funktioner t.ex. användare, grupper, problem, program och e-postmallar och meddelanden.<br/>
<sup>3</sup> externa cache-stöd för den här nivån kommer snart.<br/>
<sup>4</sup> klientcertifikatautentisering kommer att läggas till på nivån för användning före den allmänt tillgängliga.<br/>
<sup>5</sup> fullständig Azure Monitor-support kommer att läggas till på nivån för förbrukning.
