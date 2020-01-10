---
title: ta med fil
description: ta med fil
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780256"
---
Om du vill läsa data i arkivlagring måste du först ändra nivå för din blob till frekvent eller lågfrekvent. Den här processen kallas ÅTERUPPVÄCKNING och kan ta timmar att slutföra. Vi rekommenderar stora BLOB-storlekar för optimal ÅTERUPPVÄCKNING-prestanda. Återställning av flera små blobbar samtidigt kan ta ännu längre tid. Det finns för närvarande två återställnings prioriteter, hög (för hands version) och standard, som kan ställas in via den valfria egenskapen *x-MS-rehydratiserat-Priority* på en [set BLOB-nivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) eller [Kopiera BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) -åtgärd.

* **Standard prioritet**: ÅTERUPPVÄCKNING-begäran kommer att bearbetas i den ordning som den togs emot och kan ta upp till 15 timmar.
* **Hög prioritet (förhands granskning)** : ÅTERUPPVÄCKNING-begäran prioriteras via Standardbegäran och kan slutföras under en timme. Hög prioritet kan ta längre tid än en timme, beroende på BLOB-storlek och aktuell efter frågan. Begär Anden med hög prioritet garanteras att prioriteras över förfrågningar om standard prioritet.

> [!NOTE]
> Standard prioritet är standard alternativet ÅTERUPPVÄCKNING för arkiv. Hög prioritet är ett snabbare alternativ som kommer att kosta mer än ÅTERUPPVÄCKNING med standard prioritet och är vanligt vis reserverat för användning i situationer med nöd Data återställning.

När en ÅTERUPPVÄCKNING-begäran har initierats kan den inte avbrytas. Under återuppväckning kan du kontrollera blobegenskapen *Arkivstatus* för att se om nivån har ändrats. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen *Åtkomstnivå* anger den nya nivån: frekvent eller lågfrekvent.
