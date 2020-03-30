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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780256"
---
Om du vill läsa data i arkivlagring måste du först ändra nivå för din blob till frekvent eller lågfrekvent. Denna process kallas rehydrering och kan ta timmar att slutföra. Vi rekommenderar stora blobstorlekar för optimal rehydreringsprestanda. Återställning av flera små blobbar samtidigt kan ta ännu längre tid. Det finns för närvarande två rehydriska prioriteter, Hög (förhandsversion) och Standard, som kan ställas in via egenskapen *x-ms-rehydrate-priority* på en [ange blob-nivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) eller [kopiera blob-åtgärd.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Standardprioritet:** Begäran om rehydrering kommer att behandlas i den ordning den togs emot och kan ta upp till 15 timmar.
* **Hög prioritet (förhandsversion)**: Rehydreringsbegäran prioriteras framför standardbegäranden och kan avslutas på under 1 timme. Hög prioritet kan ta längre tid än 1 timme, beroende på blob storlek och aktuell efterfrågan. Begäranden med hög prioritet garanteras att prioriteras framför standardprioritetbegäranden.

> [!NOTE]
> Standardprioritet är standardalternativet rehydrering för arkiv. Hög prioritet är ett snabbare alternativ som kommer att kosta mer än standardprioritet rehydrering och är vanligtvis reserverad för användning i nödsituationer data restaurering situationer.

När en rehydreringsbegäran har initierats kan den inte avbrytas. Under rehydrering kan du kontrollera egenskapen *Archive Status* blob för att bekräfta om nivån har ändrats. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När den har slutförts tas egenskapen arkivstatus bort och *blob-egenskapen Access Tier* återspeglar den nya aktiva eller svala nivån.
