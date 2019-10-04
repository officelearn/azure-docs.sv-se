---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949053"
---
@No__t-0 (eller **objekt identifierare**) avser den typ av identitet som har tilldelats en roll. Förutom `DeviceId`-och `UserDefinedFunctionId`-typerna motsvarar objekt identifierare olika egenskaper för Azure Active Directory objekt.

Följande tabell innehåller de typer av objekt identifierare som stöds i Azure Digitals:

| type | Beskrivning |
| --- | --- |
| UserId | Tilldelar en användare en roll. |
| DeviceId | Tilldelar en enhet en roll. |
| Namn | Tilldelar en roll till ett domän namn. Varje användare med det angivna domän namnet har åtkomst behörighet för motsvarande roll. |
| TenantId | Tilldelar en klient organisation en roll. Varje användare som tillhör det angivna Azure AD-klient-ID: t har åtkomst behörighet för motsvarande roll. |
| ServicePrincipalId | Tilldelar en roll till ett objekt-ID för tjänstens huvud namn. |
| UserDefinedFunctionId | Tilldelar en roll till en användardefinierad funktion (UDF). |