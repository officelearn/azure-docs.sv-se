---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307258"
---
`objectIdType` (eller **objekt identifierarens typ**) avser den typ av identitet som har tilldelats en roll. Förutom `DeviceId`-och `UserDefinedFunctionId` typer motsvarar objekt identifierare typer egenskaper för Azure Active Directory objekt.

Följande tabell innehåller de typer av objekt identifierare som stöds i Azure Digitals:

| Typ | Beskrivning |
| --- | --- |
| UserId | Tilldelar en användare en roll. |
| DeviceId | Tilldelar en enhet en roll. |
| Namn | Tilldelar en roll till ett domän namn. Varje användare med det angivna domän namnet har åtkomst behörighet för motsvarande roll. |
| TenantId | Tilldelar en klient organisation en roll. Varje användare som tillhör det angivna Azure AD-klient-ID: t har åtkomst behörighet för motsvarande roll. |
| ServicePrincipalId | Tilldelar en roll till ett objekt-ID för tjänstens huvud namn. |
| UserDefinedFunctionId | Tilldelar en roll till en användardefinierad funktion (UDF). |