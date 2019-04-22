---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737303"
---
| Resurs | Mål        |
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

<sup>1</sup> enskilt objekt dataflödet beror på flera faktorer, inklusive men inte begränsat till: samtidighet, åtgärden storlek, prestandanivån, hastigheten på källan för överföringar och mål för nedladdningar.