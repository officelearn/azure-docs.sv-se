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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080910"
---
| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Azure Batch-konton per region per prenumeration | 1-3 |50 |
| Reserverade kärnor per Batch-konto | 90-900 | Kontakta supporten |
| Lågprioritetskärnor per Batch-konto | 10-100 | Kontakta supporten |
| **[Aktiva](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)**  jobb och jobbscheman per Batch-konto (**slutförts** jobb har ingen gräns) | 100-300 | 1,000<sup>1</sup> |
| Pooler per Batch-konto | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Standardgränserna varierar beroende på vilken typ av prenumeration som du använder för att skapa ett Batch-konto. Kärnkvoter som visas är för Batch-konton i Batch-tjänstläge. [Visa kvoter i Batch-kontot](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup>kontakta Azure-supporten om du vill begära en ökning utöver denna gräns.
