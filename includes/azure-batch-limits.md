---
title: inkludera fil
description: inkludera fil
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 8d23da1c3a45ed12193dfd4c1fc2ede453fc6ac7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84323898"
---
| **Resurs** | **Standardgräns** | **Maximal gräns** |
| --- | --- | --- |
| Azure Batch konton per region per prenumeration | 1-3 |50 |
| Dedikerade kärnor per batch-konto | 90-900 | Kontakta supporten |
| Låg prioritets kärnor per batch-konto | 10-100 | Kontakta supporten |
| **[Aktiva](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** jobb och jobb scheman per batch-konto (**slutförda** jobb har ingen gräns) | 100-300 | 1 000<sup>1</sup> |
| Pooler per Batch-konto | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Kontakta Azure-supporten om du vill begära en ökning utöver den här gränsen.

> [!IMPORTANT]
> Vi ändrar hur du begär och hanterar dedikerad kvot.  Det totala dedikerade virtuella processorer är det påtvingade värdet, men snart kommer vi att genomdriva dedikerad kvot per VM-serie. Kvoten med låg prioritet fortsätter att gälla baserat på den totala gränsen. den kommer inte att tillämpas av VM-serien.

> [!NOTE]
> Standard gränserna varierar beroende på vilken typ av prenumeration du använder för att skapa ett batch-konto. Kärnor som visas är för batch-konton i batch-tjänstens läge. [Visa kvoterna i batch-kontot](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> För att hjälpa oss att hantera kapaciteten under den globala hälso Pandemic är standard kvoterna för nya batch-konton i vissa regioner och för vissa typer av prenumeration sänkta från ovanstående värde intervall, i vissa fall till noll kärnor. När du skapar ett nytt batch-konto [kontrollerar du kärn kvoten](../articles/batch/batch-quota-limit.md#view-batch-quotas) och [begär en ökad kärn kvot](../articles/batch/batch-quota-limit.md#increase-a-quota), om det behövs. 
