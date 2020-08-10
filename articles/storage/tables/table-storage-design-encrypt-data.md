---
title: Kryptera data i Azure Storage-tabellen | Microsoft Docs
description: Lär dig mer om tabell data kryptering i Azure Storage. Med klient biblioteket för .NET-Azure Storage kan du kryptera sträng enheter för att infoga och ersätta åtgärder.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: fec213b9d7429714beb948f061445fd37d698624
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037498"
---
# <a name="encrypt-table-data"></a>Kryptera tabell data
Klient biblioteket för .NET-Azure Storage stöder kryptering av sträng egenskaper för enhets egenskaper för INSERT-och replace-åtgärder. De krypterade strängarna lagras i tjänsten som binära egenskaper, och de konverteras tillbaka till strängar efter dekrypteringen.    

För tabeller, förutom krypterings principen, måste användarna ange vilka egenskaper som ska krypteras. Detta kan göras genom att ange ett [EncryptProperty]-attribut (för POCO-entiteter som härleds från TableEntity) eller en krypterings lösare i alternativ för begäran. En krypterings lösare är ett ombud som tar en partitionsnyckel, rad nyckel och egenskaps namn och returnerar ett booleskt värde som anger om den egenskapen ska krypteras. Under krypteringen använder klient biblioteket den här informationen för att avgöra om en egenskap ska krypteras när den skrivs till kabeln. Delegaten ger också möjlighet att logiskt kringgå hur egenskaper krypteras. (Till exempel om X, krypterar sedan egenskap A, annars krypterar egenskaper A och B.) Du behöver inte ange den här informationen när du läser eller frågar entiteter.

## <a name="merge-support"></a>Merge-stöd

Sammanslagning stöds inte för närvarande. Eftersom en del av egenskaperna kan ha krypterats tidigare med en annan nyckel, sammanfogar de nya egenskaperna och uppdaterar metadata i data förlust. Sammanslagning av kräver att du gör extra tjänst anrop för att läsa den befintliga entiteten från tjänsten, eller genom att använda en ny nyckel per egenskap, som båda inte passar av prestanda skäl.     

Information om hur du krypterar tabell data finns i [kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Nästa steg

- [Tabell design mönster](table-storage-design-patterns.md)
- [Modellera relationer](table-storage-design-modeling.md)
- [Modellera relationer](table-storage-design-modeling.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)
