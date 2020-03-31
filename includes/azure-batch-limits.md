---
title: ta med fil
description: ta med fil
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080910"
---
| **Resurs** | **Standardgräns** | **Högsta gräns** |
| --- | --- | --- |
| Azure Batch-konton per region per prenumeration | 1-3 |50 |
| Dedikerade kärnor per batch-konto | 90-900 | Kontakta supporten |
| Lågprioriterade kärnor per batch-konto | 10-100 | Kontakta supporten |
| **[Aktiva](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** jobb och jobbuppställningar per batchkonto **(slutförda** jobb har ingen gräns) | 100-300 | 1 000<sup>1</sup> |
| Pooler per Batch-konto | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Standardgränserna varierar beroende på vilken typ av prenumeration du använder för att skapa ett batchkonto. Kärnkvoter som visas gäller för Batch-konton i batchtjänstläge. [Visa kvoterna i ditt batchkonto](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1.</sup> Om du vill begära en ökning utöver den här gränsen kontaktar du Azure Support.
