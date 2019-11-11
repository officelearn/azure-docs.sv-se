---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 0fda881b805eb3a967cf3b05f6c6df8c65d20730
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905800"
---
| Resurs | Mål        |
|----------|---------------|
| Maximal storlek för enskild BLOB-behållare | Samma som högsta kapacitet för lagrings konto |
| Maximalt antal block i en Block-Blob eller Lägg till BLOB | 50 000 block |
| Maximal storlek för ett block i en Block-Blob | 100 MiB |
| Maximal storlek för en Block-Blob | 50 000 X 100 MiB (cirka 4,75 TiB) |
| Maximal storlek för ett block i en tilläggs-BLOB | 4 MiB |
| Maximal storlek för en tilläggs-BLOB | 50 000 x 4 MiB (cirka 195 GiB) |
| Maximal storlek på en sid-BLOB | 8 TiB |
| Maximalt antal lagrade åtkomst principer per BLOB-behållare | 5 |
|Mål begär ande frekvens för en enskild BLOB | Upp till 500 begär Anden per sekund |
|Mål data flöde för en enda sid-BLOB | Upp till 60 MiB per sekund |
|Mål data flöde för en enda Block-Blob |Upp till lagrings kontots inkommande/utgående gränser<sup>1</sup> |

<sup>1</sup> data flöde för enstaka objekt är beroende av flera faktorer, inklusive, men inte begränsat till: samtidighet, begär ande storlek, prestanda nivå, källans hastighet och mål för hämtningar. Om du vill dra nytta av [stora data flöden Block Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) -prestanda förbättringar använder du en Lägg till-BLOB-eller begär ande storlek för > 4 MiB (> 256 KiB för Premium-prestanda Block Blob Storage eller för data Lake Storage Gen2).
