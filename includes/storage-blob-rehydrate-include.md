---
title: ta med fil
description: ta med fil
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684081"
---
Om du vill läsa data i arkivlagring måste du först ändra nivå för din blob till frekvent eller lågfrekvent. Denna process kallas rehydrering och kan ta timmar att slutföra. Vi rekommenderar stora blobstorlekar för optimal rehydreringsprestanda. Återställning av flera små blobbar samtidigt kan ta ännu längre tid. Det finns för närvarande två rehydriska prioriteter, Hög och Standard, som kan ställas in via egenskapen *x-ms-rehydrate-priority* för en [ange Blob-nivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) eller [kopiera blob-åtgärd.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Standardprioritet:** Begäran om rehydrering kommer att behandlas i den ordning den togs emot och kan ta upp till 15 timmar.
* **Hög prioritet:** Rehydreringsbegäran prioriteras framför standardbegäranden och kan avslutas på under 1 timme. Hög prioritet kan ta längre tid än 1 timme, beroende på blob storlek och aktuell efterfrågan. Begäranden med hög prioritet garanteras att prioriteras framför standardprioritetbegäranden.

> [!NOTE]
> Standardprioritet är standardalternativet rehydrering för arkiv. Hög prioritet är ett snabbare alternativ som kommer att kosta mer än standardprioritet rehydrering och är vanligtvis reserverad för användning i nödsituationer data restaurering situationer.

När en rehydreringsbegäran har initierats kan den inte avbrytas. Under rehydreringsprocessen fortsätter *blob-egenskapen x-ms-access-tier* att visas som arkiv tills återfuktning har slutförts till en onlinenivå. För att bekräfta rehydreringsstatus och förlopp kan du anropa [Get Blob-egenskaper](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) för att kontrollera *x-ms-archive-status* och *x-ms-rehydrate-priority* blob-egenskaperna. Arkivstatusen kan läsa "rehydrate-pending-to-hot" eller "rehydrate-pending-to-cool" beroende på rehydrera målnivån. Den rehydriska prioriteten anger hastigheten på "Hög" eller "Standard". När du är klar tas arkivstatus och rehydrera prioritetsegenskaper bort, och åtkomstnivå blob-egenskapen uppdateras för att återspegla den valda aktiva eller svala nivån.
