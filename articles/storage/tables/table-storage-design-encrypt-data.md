---
title: Kryptera Azure-lagringstabelldata | Microsoft-dokument
description: Lär dig mer om kryptering av tabelldata i Azure-lagring.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60326011"
---
# <a name="encrypt-table-data"></a>Kryptera tabelldata
.NET Azure Storage Client Library stöder kryptering av strängentitetsegenskaper för att infoga och ersätta åtgärder. De krypterade strängarna lagras på tjänsten som binära egenskaper och de konverteras tillbaka till strängar efter dekryptering.    

För tabeller måste användarna, utöver krypteringsprincipen, ange vilka egenskaper som ska krypteras. Detta kan göras genom att antingen ange ett [EncryptProperty]-attribut (för POCO-entiteter som härleds från TableEntity) eller en krypteringsmatchningsverktyget i begäransalternativ. En krypteringsmatchningsordnare är ett ombud som tar en partitionsnyckel, radnyckel och egenskapsnamn och returnerar en boolesk som anger om egenskapen ska krypteras. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras när du skriver till kabeln. Ombudet ger också möjlighet till logik kring hur egenskaper krypteras. (Om du till exempel X krypterar du egenskap A; krypterar annars egenskaperna A och B.) Det är inte nödvändigt att ange den här informationen när du läser eller frågar entiteter.

## <a name="merge-support"></a>Stöd för koppla samman

Sammanfogning stöds för närvarande inte. Eftersom en delmängd av egenskaper kan ha krypterats tidigare med en annan nyckel, helt enkelt slå samman de nya egenskaperna och uppdatera metadata resulterar i dataförlust. Sammanslagningen måste antingen göra extra serviceanrop för att läsa den befintliga entiteten från tjänsten, eller använda en ny nyckel per egenskap, som båda inte är lämpliga av prestandaskäl.     

Information om hur du krypterar tabelldata finns i [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Nästa steg

- [Mönster för tabelldesign](table-storage-design-patterns.md)
- [Modellera relationer](table-storage-design-modeling.md)
- [Modellera relationer](table-storage-design-modeling.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)
