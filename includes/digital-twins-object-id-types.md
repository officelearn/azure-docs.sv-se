---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 94baeb3d459b700cc95d88fb82f995957640aab6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012299"
---
(Eller **objekt identifierare-typen**) avser den typ av identitet som har tilldelats en roll. `objectIdType` Förutom- `UserDefinedFunctionId` och-typerna motsvarar objekt identifierare typer egenskaper för Azure Active Directory objekt. `DeviceId`

Följande tabell innehåller de typer av objekt identifierare som stöds i Azure Digitals:

| type | Beskrivning |
| --- | --- |
| UserId | Tilldelar en användare en roll. |
| DeviceId | Tilldelar en enhet en roll. |
| Namn | Tilldelar en roll till ett domän namn. Varje användare med det angivna domän namnet har åtkomst behörighet för motsvarande roll. |
| TenantId | Tilldelar en klient organisation en roll. Varje användare som tillhör det angivna Azure AD-klient-ID: t har åtkomst behörighet för motsvarande roll. |
| ServicePrincipalId | Tilldelar en roll till ett objekt-ID för tjänstens huvud namn. |
| UserDefinedFunctionId | Tilldelar en roll till en användardefinierad funktion (UDF). |