---
title: ta med fil
description: ta med fil
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b48dbfc33890df12209c3a242d812ad2103e07a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553797"
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
