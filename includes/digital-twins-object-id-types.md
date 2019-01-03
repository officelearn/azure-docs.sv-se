---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53795905"
---
Den `objectIdType` (eller **objekt-ID-typ**) refererar till typ av identitet som har tilldelats en roll. Förutom den `DeviceId` och `UserDefinedFunctionId` typer, identifierare objekttyper motsvarar egenskaperna för Azure Active Directory-objekt.

I följande tabell innehåller stöds objekttyper identifierare i Azure Digital Twins:

| Typ | Beskrivning |
| --- | --- |
| UserId | Tilldelar en roll till en användare. |
| DeviceId | Tilldelar en roll till en enhet. |
| Domännamn | Tilldelar en roll till ett domännamn. Varje användare med det angivna domännamnet har rättigheter motsvarande roll. |
| TenantId | Tilldelar en roll till en klient. Varje användare som tillhör den angivna Azure AD-klient-ID har behörighet för rollen motsvarande. |
| servicePrincipalId | Tilldelar en roll till ett objekt-ID. |
| UserDefinedFunctionId | Tilldelar en roll till en användardefinierad funktion (UDF). |