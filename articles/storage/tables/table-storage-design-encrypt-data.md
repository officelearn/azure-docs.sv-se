---
title: Kryptera data i Azure storage tabellen | Microsoft Docs
description: Läs mer om tabellen datakryptering i Azure-lagring.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/11/2018
ms.author: sngun
ms.openlocfilehash: 082e8a54cc8625a4bbdea2157f73874dbc86fde2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661040"
---
# <a name="encrypt-table-data"></a>Kryptera tabelldata
.NET Azure Storage-klientbibliotek har stöd för kryptering av strängen Entitetsegenskaper för insert och ersätt-åtgärder. Krypterade strängar lagras på tjänsten som binära egenskaper och de konverteras till strängar efter dekrypteringen.    

För tabeller, förutom krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Detta kan göras genom att antingen att ange attributet [EncryptProperty] (för POCO entiteter som är härledda från TableEntity) eller en kryptering matcharen begäran alternativ. En kryptering Konfliktlösaren är en delegat som tar en partitionsnyckel, radnyckel och egenskapsnamn och returnerar ett booleskt värde som anger om egenskapen ska krypteras. Under krypteringen använder klientbiblioteket informationen för att bestämma om du vill kryptera en egenskap under skrivning till kabeln. Delegaten ger också möjlighet att logik runt hur egenskaperna är krypterade. (Till exempel om X, sedan kryptera egenskapen A; annars kryptera egenskaper A och b) Det är inte nödvändigt att ange den här informationen vid läsning eller fråga entiteter.

## <a name="merge-support"></a>Sammanfoga stöd

Koppling stöds inte för närvarande. Eftersom en delmängd av egenskaper kan har krypterats tidigare med hjälp av en annan nyckel resulterar bara sammanslagning nya egenskaper och uppdaterar metadata i förlust av data. Sammanslagning av antingen kräver extra service-anrop för att läsa den befintliga enheten från tjänsten eller med en ny nyckel för varje egenskap som lämpar sig inte av prestandaskäl.     

Information om hur du krypterar tabelldata finns [kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabellen](table-storage-design-patterns.md)
- [Modeling relationer](table-storage-design-modeling.md)
- [Modeling relationer](table-storage-design-modeling.md)
- [Design för dataändring](table-storage-design-for-modification.md)