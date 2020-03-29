---
title: Riktlinjer för Azure-lagringstabelldesign | Microsoft-dokument
description: Utforma din Azure-tabelltjänst så att den stöder läsåtgärder på ett effektivt sätt.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61269855"
---
# <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign

Att utforma tabeller för användning med Azure-lagringstabelltjänsten skiljer sig mycket från designöverväganden för en relationsdatabas. I den här artikeln beskrivs riktlinjer för hur du utformar tabelltjänstlösningen så att den ska vara effektiv och skriveffektiv.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Utforma din tabellservicelösning så att den blir läseffektiv

* ***Design för frågor i lästunga program.*** När du utformar tabellerna bör du tänka på de frågor (särskilt de latenskänsliga) som du ska köra innan du tänker på hur du ska uppdatera dina entiteter. Detta resulterar vanligtvis i en effektiv och högpresterande lösning.  
* ***Ange både PartitionKey och RowKey i dina frågor.*** *Punktfrågor* som dessa är de mest effektiva tabelltjänstfrågorna.  
* ***Överväg att lagra dubbletter av entiteter.*** Tabelllagring är billigt så överväg att lagra samma enhet flera gånger (med olika nycklar) för att möjliggöra effektivare frågor.  
* ***Överväg att avnormalisera dina data.*** Tabell lagring är billigt så överväga att avnormalisera dina data. Lagra sammanfattningsenheter så att frågor om aggregerade data bara behöver komma åt en enda entitet.  
* ***Använd sammansatta nyckelvärden.*** De enda tangenter du har är **PartitionKey** och **RowKey**. Använd till exempel sammansatta nyckelvärden för att aktivera alternativa nyckelsökvägar för åtkomst till entiteter.  
* ***Använd frågeprojektion.*** Du kan minska mängden data som du överför över nätverket genom att använda frågor som bara markerar de fält du behöver.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Utforma din tabellservicelösning så att den är skriveffektiv  

* ***Skapa inte heta partitioner.*** Välj nycklar som gör att du kan sprida dina begäranden över flera partitioner när som helst.  
* ***Undvik toppar i trafiken.*** Jämna ut trafiken under en rimlig tidsperiod och undvik toppar i trafiken.
* ***Skapa inte nödvändigtvis en separat tabell för varje typ av entitet.*** När du behöver atomtransaktioner mellan entitetstyper kan du lagra dessa flera entitetstyper i samma partition i samma tabell.
* ***Tänk på det maximala dataflödet du måste uppnå.*** Du måste vara medveten om skalbarhetsmålen för tabelltjänsten och se till att designen inte gör att du överskrider dem.  

När du läser den här guiden kommer du att se exempel som omsätter alla dessa principer i praktiken. 

## <a name="next-steps"></a>Nästa steg

- [Mönster för tabelldesign](table-storage-design-patterns.md)
- [Utforma för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)
