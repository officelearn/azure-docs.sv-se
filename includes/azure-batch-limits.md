---
title: ta med fil
description: ta med fil
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 9ffb02fce41e8805dfccf1dfd6e982cf107039ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127551"
---
| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Azure Batch-konton per region per prenumeration | 1-3 |50 |
| Reserverade kärnor per Batch-konto | 10-100 | N/A<sup>1</sup> |
| Lågprioritetskärnor per Batch-konto | 10-100 | EJ TILLÄMPLIGT<sup>2</sup> |
| Aktiva jobb och jobbscheman<sup>3</sup> per Batch-konto | 100-300 | 1,000<sup>4</sup> |
| Pooler per Batch-konto | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> Standardgränserna varierar beroende på vilken typ av prenumeration som du använder för att skapa ett Batch-konto. Kärnkvoter som visas är för Batch-konton i Batch-tjänstläge. [Visa kvoter i Batch-kontot](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>kan du öka antalet reserverade kärnor per Batch-konto, men det maximala antalet är okänt. Kontakta Azure-supporten om du vill diskutera alternativ.

<sup>2</sup>kan du öka antalet lågprioritetskärnor per Batch-konto, men det maximala antalet är okänt. Kontakta Azure-supporten om du vill diskutera alternativ.

<sup>3</sup>slutförda jobb och jobbscheman begränsas inte.

<sup>4</sup>kontakta Azure-supporten om du vill begära en ökning utöver denna gräns.
