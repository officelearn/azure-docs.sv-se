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
ms.openlocfilehash: 7e4760990229433b2ea40fadd0d17de0b52fcb36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534143"
---
I följande tabell beskrivs de roller som är tillgängliga i Azure Digital Twins:

| **Roll** | **Beskrivning** | **identifierare** |
| --- | --- | --- |
| Utrymme administratör | *Skapa*, *läsa*, *uppdatering*, och *ta bort* behörighet för det angivna utrymmet och alla noder under. Globala behörigheter. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Användaradministratör| *Skapa*, *läsa*, *uppdatering*, och *ta bort* behörighet för användare och användare-relaterade objekt. *LÄSA* behörighet för blanksteg. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Enhetsadministratör | *Skapa*, *läsa*, *uppdatering*, och *ta bort* behörighet för enheter och enheter-relaterade objekt. *LÄSA* behörighet för blanksteg. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Nyckel-administratör | *Skapa*, *läsa*, *uppdatering*, och *ta bort* behörighet för åtkomstnycklar. *LÄSA* behörighet för blanksteg. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Token-administratör |  *LÄSA* och *uppdatering* behörighet för åtkomstnycklar. *LÄSA* behörighet för blanksteg. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Användare |  *LÄSA* behörigheten för blanksteg, sensorer och användare, som innehåller de tillhörande relaterade objekt. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Support-expert |  *LÄSA* behörighet för allt utom åtkomstnycklar. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Installationsprogram för enhet | *LÄSA* och *uppdatering* behörighet för enheter och sensorer, som innehåller de tillhörande relaterade objekt. *LÄSA* behörighet för blanksteg. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Gateway-enhet | *Skapa* behörighet för sensorer. *LÄSA* behörighet för enheter och sensorer, som innehåller de tillhörande relaterade objekt. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |