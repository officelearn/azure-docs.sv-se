---
title: ta med fil
description: ta med fil
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81736811"
---
| **Resurs** | **Standardgräns** | **Högsta gräns** |
| --- | --- | --- |
| Azure Batch-konton per region per prenumeration | 1-3 |50 |
| Dedikerade kärnor per batch-konto | 90-900 | Kontakta supporten |
| Lågprioriterade kärnor per batch-konto | 10-100 | Kontakta supporten |
| **[Aktiva](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** jobb och jobbuppställningar per batchkonto **(slutförda** jobb har ingen gräns) | 100-300 | 1 000<sup>1</sup> |
| Pooler per Batch-konto | 20-100 | 500<sup>1</sup> |

<sup>1.</sup> Om du vill begära en ökning utöver den här gränsen kontaktar du Azure Support.

> [!NOTE]
> Standardgränserna varierar beroende på vilken typ av prenumeration du använder för att skapa ett batchkonto. Kärnkvoter som visas gäller för Batch-konton i batchtjänstläge. [Visa kvoterna i ditt batchkonto](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> För att hjälpa oss att bättre hantera kapaciteten under den globala hälsopärandet har standardkärnkvoterna för nya Batch-konton i vissa regioner och för vissa typer av abonnemang reducerats från ovanstående värdeintervall, i vissa fall till noll kärnor. När du skapar ett nytt batchkonto [kontrollerar du kärnkvoten](../articles/batch/batch-quota-limit.md#view-batch-quotas) och [begär en kärnkvotsökning](../articles/batch/batch-quota-limit.md#increase-a-quota)om det behövs. 
