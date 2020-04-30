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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81736811"
---
| **Resurs** | **Standardgräns** | **Maximal gräns** |
| --- | --- | --- |
| Azure Batch konton per region per prenumeration | 1-3 |50 |
| Dedikerade kärnor per batch-konto | 90-900 | Kontakta supporten |
| Låg prioritets kärnor per batch-konto | 10-100 | Kontakta supporten |
| **[Aktiva](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** jobb och jobb scheman per batch-konto (**slutförda** jobb har ingen gräns) | 100-300 | 1 000<sup>1</sup> |
| Pooler per Batch-konto | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Kontakta Azure-supporten om du vill begära en ökning utöver den här gränsen.

> [!NOTE]
> Standard gränserna varierar beroende på vilken typ av prenumeration du använder för att skapa ett batch-konto. Kärnor som visas är för batch-konton i batch-tjänstens läge. [Visa kvoterna i batch-kontot](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> För att hjälpa oss att hantera kapaciteten under den globala hälso Pandemic är standard kvoterna för nya batch-konton i vissa regioner och för vissa typer av prenumeration sänkta från ovanstående värde intervall, i vissa fall till noll kärnor. När du skapar ett nytt batch-konto [kontrollerar du kärn kvoten](../articles/batch/batch-quota-limit.md#view-batch-quotas) och [begär en ökad kärn kvot](../articles/batch/batch-quota-limit.md#increase-a-quota), om det behövs. 
