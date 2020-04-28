---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76268303"
---
(Eller **objekt identifierare-typen**) avser den typ av identitet som har tilldelats en roll. `objectIdType` Förutom- `DeviceId` och `UserDefinedFunctionId` -typerna motsvarar objekt identifierare typer egenskaper för Azure Active Directory objekt.

Följande tabell innehåller de typer av objekt identifierare som stöds i Azure Digitals:

| Typ | Beskrivning |
| --- | --- |
| UserId | Tilldelar en användare en roll. |
| DeviceId | Tilldelar en enhet en roll. |
| DomainName | Tilldelar en roll till ett domän namn. Varje användare med det angivna domän namnet har åtkomst behörighet för motsvarande roll. |
| TenantId | Tilldelar en klient organisation en roll. Varje användare som tillhör det angivna Azure AD-klient-ID: t har åtkomst behörighet för motsvarande roll. |
| ServicePrincipalId | Tilldelar en roll till ett objekt-ID för tjänstens huvud namn. |
| UserDefinedFunctionId | Tilldelar en roll till en användardefinierad funktion (UDF). |