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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76268303"
---
Den `objectIdType` (eller **objektidentifierartypen)** refererar till den typ av identitet som ges till en roll. Förutom de `DeviceId` `UserDefinedFunctionId` och typerna motsvarar objektidentifierartyper egenskaperna för Azure Active Directory-objekt.

Följande tabell innehåller objektidentifieringstyperna som stöds i Azure Digital Twins:

| Typ | Beskrivning |
| --- | --- |
| UserId | Tilldelar en roll till en användare. |
| DeviceId | Tilldelar en roll till en enhet. |
| DomainName | Tilldelar en roll till ett domännamn. Varje användare med det angivna domännamnet har åtkomsträttigheterna för motsvarande roll. |
| TenantId | Tilldelar en roll till en klient. Varje användare som tillhör det angivna Azure AD-klient-ID:et har åtkomsträttigheterna för motsvarande roll. |
| ServicePrincipalId | Tilldelar en roll till ett tjänsthuvudobjekt-ID. |
| UserDefinedFunctionId | Tilldelar en roll till en användardefinierad funktion (UDF). |