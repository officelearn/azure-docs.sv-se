---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114823"
---
| Resource | Mål        |
|----------|---------------|
| Maximal storlek på enskild blob-behållare | Samma som-konto för maximal lagringskapacitet |
| Maximalt antal block i ett block blob eller tilläggsblobb | 50 000 block |
| Maximal storlek på ett block i en blockblob | 100 MiB |
| Maximal storlek på en blockblob | 50 000 x 100 MiB (cirka 4,75 TiB) |
| Maximal storlek på ett block i en tilläggsblobb | 4 MiB |
| Maximal storlek på en tilläggsblobb | 50 000 x 4 MiB (cirka 195 GiB) |
| Maximal storlek på en sidblobb | 8 TiB |
| Maximalt antal lagrade åtkomstprinciper per blob-behållare | 5 |
|Måldataflöde för enskild blob |Upp till ingående/utgående trafik för lagringskontogränser<sup>1</sup> |

<sup>1</sup> enskilt objekt dataflödet beror på flera faktorer, inklusive men inte begränsat till: samtidighet, begärandestorlek, prestandanivån, hastigheten på källan för överföringar och mål för nedladdningar. Att dra nytta av [högt dataflöde blockblob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) prestandaförbättringar, använda Put Blob eller placera Block storlek på > 4 MiB (> 256 KiB för premium-prestanda block blob-lagring eller Data Lake Storage Gen2).
