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
ms.openlocfilehash: 864d693f2919922b5035f963103b2ce98600a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044932"
---
I följande tabell beskrivs de roller som är tillgängliga i Azure Digital Twins:

| **Roll** | **Beskrivning** | **Identifierare** |
| --- | --- | --- |
| Utrymmesadministratör | *SKAPA,* *LÄS,* *UPPDATERA*och *TA BORT* behörighet för det angivna utrymmet och alla noder under. Globalt tillstånd. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Användaradministratör| *SKAPA,* *LÄS,* *UPPDATERA*och *TA BORT* behörighet för användare och användarrelaterade objekt. *LÄS* tillstånd för mellanslag. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Enhetsadministratör | *SKAPA,* *LÄS,* *UPPDATERA*och *TA BORT* behörighet för enheter och enhetsrelaterade objekt. *LÄS* tillstånd för mellanslag. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Nyckeladministratör | *SKAPA,* *LÄS,* *UPPDATERA*och *TA BORT* behörighet för åtkomstnycklar. *LÄS* tillstånd för mellanslag. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Token administratör |  *LÄS-* och *UPPDATERINGSbehörighet* för åtkomstnycklar. *LÄS* tillstånd för mellanslag. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Användare |  *LÄS* tillstånd för mellanslag, sensorer och användare, vilket inkluderar deras motsvarande relaterade objekt. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Support Specialist |  *LÄS* tillstånd för allt utom åtkomstnycklar. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Installationsprogrammet för enheten | *LÄS-* och *UPDATE-behörighet* för enheter och sensorer, vilket inkluderar motsvarande relaterade objekt. *LÄS* tillstånd för mellanslag. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Gateway-enhet | *SKAPA* behörighet för sensorer. *LÄS* tillstånd för enheter och sensorer, som inkluderar motsvarande relaterade objekt. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |