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
ms.openlocfilehash: d881c8de7ecc32be0ca0cc2c5a82e0d2d51a7054
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780325"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Funktionsbaserad jämförelse av Azure API Management-nivåer

Varje API Management [prisnivån](https://aka.ms/apimpricing) erbjuder en distinkt uppsättning funktioner och per enhet [kapacitet](api-management-capacity.md). I följande tabell sammanfattas de viktigaste funktionerna som är tillgängliga i var och en av nivåerna. Vissa funktioner kan fungera på olika sätt eller har olika funktioner beroende på vilken nivå. I sådana fall framhävs skillnaderna i för dokumentationsartiklar som beskriver dessa enskilda funktioner.

| Funktion                                                                                      | Förbrukning<sup>FÖRHANDSVERSION</sup> | Utvecklare      | Grundläggande          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Azure AD-integrering<sup>1</sup>                                                             | Nej                            | Ja            | Nej             | Ja            | Ja            |
| Stöd för Virtual Network (VNet)                                                               | Nej                            | Ja            | Nej             | Nej             | Ja            |
| Distribution i flera regioner                                                                      | Nej                            | Nej             | Nej             | Nej             | Ja            |
| Flera anpassade domännamn                                                                 | Nej                            | Nej             | Nej             | Nej             | Ja            |
| Developer-portalen<sup>2</sup>                                                                 | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Inbyggd cache                                                                               | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Inbyggda analyser                                                                           | Nej                            | Ja            | Ja            | Ja            | Ja            |
| [SSL-inställningar](api-management-howto-manage-protocols-ciphers.md)                             | Nej                            | Ja            | Ja            | Ja            | Ja            |
| [Externa cache](https://aka.ms/apimbyoc)                                                    | Ja                           | Ja            | Ja            | Ja            | Ja            |
| [Autentisering av klientcertifikat](api-management-howto-mutual-certificates-for-clients.md) | Inte<sup>3</sup>                | Ja            | Ja            | Ja            | Ja            |
| [Säkerhetskopiering och återställning](api-management-howto-disaster-recovery-backup-restore.md)               | Nej                            | Ja            | Ja            | Ja            | Ja            |
| [Management via Git](api-management-configuration-repository-git.md)                        | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Direkthantering API                                                                        | Nej                            | Ja            | Ja            | Ja            | Ja            |
| Azure Monitor-loggar och mått                                                               | Inte<sup>4</sup>                | Ja            | Ja            | Ja            | Ja            |

<sup>1</sup> kan du använda Azure AD (och Azure AD B2C) som en identitet provider för användaren logga in på utvecklarportalen.<br/>
<sup>2</sup> , inklusive relaterade funktioner t.ex. användare, grupper, problem, program och e-postmallar och meddelanden.<br/>
<sup>3</sup> klientcertifikatautentisering kommer att läggas till på nivån för användning före den allmänt tillgängliga.<br/>
<sup>4</sup> fullständig Azure Monitor-support kommer att läggas till på nivån för förbrukning.
