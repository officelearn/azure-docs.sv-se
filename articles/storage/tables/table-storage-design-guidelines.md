---
title: Riktlinjer för Azure storage tabelldesign | Microsoft Docs
description: Utforma din Azure-tabelltjänst för läsåtgärder effektivt.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454529"
---
# <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign

Utforma tabeller för användning med tjänsten Azure storage-tabell skiljer sig mycket från designöverväganden för en relationsdatabas. Den här artikeln beskriver riktlinjer för att utforma din tabell tjänstlösning för att läsa effektiv och skriva effektiv.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Utforma din tabell tjänstlösning för Läs-effektivt

* ***Design för frågor i Läs-aktiverat program.*** När du utformar dina tabeller, tänka frågor (särskilt svarstiden känsliga som) som du ska köra innan du tänka på hur du uppdaterar dina entiteter. Detta innebär vanligtvis en effektiv och högpresterande lösning.  
* ***Ange både PartitionKey och RowKey i dina frågor.*** *Peka frågor* som dessa är de mest effektiva frågorna i table service.  
* ***Överväg att lagra dubbletter av entiteter.*** Table storage är billiga så fundera över lagra samma entitet flera gånger (med olika nycklar) om du vill aktivera mer effektiva frågor.  
* ***Överväg att avnormalisera data.*** Table storage är billiga så fundera över avnormalisera data. Till exempel lagra sammanfattning entiteter så att frågor för aggregerade data bara behöver åtkomst till en enda entitet.  
* ***Använd sammansatt nyckelvärden.*** De enda nycklarna som du har är **PartitionKey** och **RowKey**. Till exempel använda sammansatt nyckelvärden för att aktivera alternativa knappade åtkomstsökvägar entiteter.  
* ***Använd fråga projektion.*** Du kan minska mängden data som du överför över nätverket med hjälp av frågor som väljer enbart de fält som du behöver.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Utforma din tabell tjänstlösning för att skriva effektiv  

* ***Skapa inte heta partitioner.*** Välj nycklar som gör det möjligt att sprida dina förfrågningar över flera partitioner vid någon tidpunkt.  
* ***Undvik trafiktoppar.*** Utjämna trafiken över en rimlig tidsperiod och undvika toppar i trafiken.
* ***Inte nödvändigtvis att skapa en separat tabell för varje typ av entiteten.*** När du behöver atomiska transaktioner över entitetstyper kan lagra du dessa flera typer av enheter i samma partition i samma tabell.
* ***Överväg att det maximala dataflöde som du måste uppnå.*** Du måste känna till det för skalbarhetsmål för tabelltjänsten och säkerställa att din design kommer du att överskrida dem.  

När du läser den här handboken visas exempel som placerar alla dessa principer i praktiken. 

## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabell](table-storage-design-patterns.md)
- [Design för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Design för dataändringar](table-storage-design-for-modification.md)
