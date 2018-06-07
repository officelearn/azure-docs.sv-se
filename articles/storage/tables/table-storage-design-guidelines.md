---
title: Riktlinjer för Azure storage tabelldesign | Microsoft Docs
description: Utforma din Azure table-tjänsten för att stödja läsåtgärder effektivt.
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 5329d33aee1bb1a55e9982b1ba9e3e8329246980
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661043"
---
# <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign

Skapar tabeller för användning med tjänsten Azure storage tabell skiljer sig mycket från beaktanden vid utformning av en relationsdatabas. Den här artikeln beskriver riktlinjer för att utforma din lösning för tabellen att läsas effektivt och skriva effektiva.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Utforma din lösning för tabellen ska läsa effektivt

* ***Design för frågor i Läs-aktiverat program.*** När du utformar dina tabeller tänka frågor (särskilt svarstid känsliga de) som du ska köra innan du tycker om hur du ska uppdatera-enheterna. Detta ger vanligtvis en effektiv och performant lösning.  
* ***Ange både PartitionKey och RowKey i dina frågor.*** *Peka frågor* som dessa är de mest effektiva tabellen service frågorna.  
* ***Överväg att lagra dubbletter av enheter.*** Table storage är billiga så du överväga att spara samma entitet flera gånger (med olika nycklar) om du vill aktivera effektivare frågor.  
* ***Överväg att denormalizing dina data.*** Table storage är billiga så Överväg denormalizing dina data. Lagra exempelvis sammanfattning enheter så att frågor för att samla in data bara behöver åtkomst till en enda entitet.  
* ***Använd sammansatt nyckelvärden.*** Är bara nycklar som du har **PartitionKey** och **RowKey**. Till exempel använda sammansatta nyckelvärden för att aktivera alternativ nycklad åtkomst sökvägar till enheter.  
* ***Använd fråga projektion.*** Du kan minska mängden data som du överför via nätverket med hjälp av frågor som väljer de fält du behöver.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Utforma din lösning för tabellen ska skriva effektiva  

* ***Skapa inte varm partitioner.*** Välj nycklar att sprida dina begäranden över flera partitioner vid någon tidpunkt.  
* ***Undvika toppar i trafiken.*** Utjämna trafiken över en rimlig tid och undvika toppar i trafiken.
* ***Inte nödvändigtvis att skapa en separat tabell för varje typ av enhet.*** När du behöver atomiska transaktioner över typer av enheter kan lagra du dessa flera typer av enheter i samma partition i samma tabell.
* ***Överväg att maximalt dataflöde du måste uppnå.*** Du måste vara medveten om skalbarhetsmål för tabelltjänsten och se till att designen inte ska orsaka du överskrida dem.  

När du läser handboken visas exempel placera alla dessa principer i praktiken. 

## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabellen](table-storage-design-patterns.md)
- [Design för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Design för dataändring](table-storage-design-for-modification.md)