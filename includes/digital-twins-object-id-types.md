---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827705"
---
@No__t-0 (eller **objekt identifierare**) avser den typ av identitet som har tilldelats en roll. Förutom `DeviceId`-och `UserDefinedFunctionId`-typerna motsvarar objekt identifierare olika egenskaper för Azure Active Directory objekt.

Följande tabell innehåller de typer av objekt identifierare som stöds i Azure Digitals:

| type | Beskrivning |
| --- | --- |
| userId | Tilldelar en användare en roll. |
| DeviceId | Tilldelar en enhet en roll. |
| Namn | Tilldelar en roll till ett domän namn. Varje användare med det angivna domän namnet har åtkomst behörighet för motsvarande roll. |
| TenantId | Tilldelar en klient organisation en roll. Varje användare som tillhör det angivna Azure AD-klient-ID: t har åtkomst behörighet för motsvarande roll. |
| ServicePrincipalId | Tilldelar en roll till ett objekt-ID för tjänstens huvud namn. |
| UserDefinedFunctionId | Tilldelar en roll till en användardefinierad funktion (UDF). |