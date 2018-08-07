---
title: Kryptera data för Azure storage-tabell | Microsoft Docs
description: Läs mer om tabellen datakryptering i Azure storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 8a7b61705f6efc5a76212fdd1345a022f58e0686
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526474"
---
# <a name="encrypt-table-data"></a>Kryptera tabelldata
Klientbiblioteket för .NET Azure Storage stöder kryptering av strängen Entitetsegenskaper för insert och ersätt-åtgärder. Krypterade strängar som är lagrade på tjänsten som binära egenskaper och de konverteras till strängar efter dekryptering.    

För tabeller, utöver krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Detta kan göras antingen att ange attributet [EncryptProperty] (för POCO entiteter som härleds från TableEntity) eller en kryptering matcharen i alternativen för certifikatförfrågan. En kryptering Konfliktlösaren är en delegat som tar en partitionsnyckel och radnyckel egenskapsnamn och returnerar ett booleskt värde som anger om egenskapen ska vara krypterad. Under krypteringen använder klientbiblioteket denna information för att bestämma om du vill kryptera en egenskap vid skrivning till ledningen. Delegaten ger också möjlighet att logic kring hur egenskaper som är krypterade. (Till exempel om X, sedan kryptera egenskapen A; annars kryptera egenskaper A och b) Du behöver inte ange den här informationen vid läsning eller fråga entiteter.

## <a name="merge-support"></a>Sammanfoga support

Koppling stöds inte för närvarande. Eftersom en delmängd av egenskaperna kan har krypterats tidigare med hjälp av en annan nyckel leder bara sammanslagning av de nya egenskaperna och uppdatera metadata som till förlust av data. Sammanslagning av antingen kräver extra service anrop till läsa befintliga entiteten från tjänsten, eller med en ny nyckel per egenskap som lämpar sig inte av prestandaskäl.     

Information om hur du krypterar tabelldata finns i [Client Side Encryption och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabell](table-storage-design-patterns.md)
- [Modellering relationer](table-storage-design-modeling.md)
- [Modellering relationer](table-storage-design-modeling.md)
- [Design för dataändringar](table-storage-design-for-modification.md)