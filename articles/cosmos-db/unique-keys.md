---
title: Använd unika nycklar i Azure Cosmos DB
description: Lär dig att använda unika nycklar i din Azure Cosmos-databas
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 3c5e8a2c85898175772dc353258e77fc8e0a74f2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799124"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Unika viktiga begränsningar i Azure Cosmos DB

Unika nycklar används för att lägga till ett lager med dataintegritet till en Azure Cosmos-behållare. Du kan skapa en unik nyckel princip när du skapar en Azure Cosmos-behållare. Med unika nycklar Se du till att en eller flera värden i en logisk partition är unikt. Du kan också garanterar unikhet per [partitionsnyckel](partition-data.md). 

När du har skapat en behållare med en unik nyckel princip förhindras skapandet av en ny eller en uppdatering av ett befintligt objekt, vilket resulterar i en dubblett i en logisk partition, som angetts av den unika key-begränsningen. Partitionsnyckeln kombineras med den unika nyckeln garanterar unikhet för ett objekt i omfånget för behållaren.

Anta exempelvis att en Azure Cosmos-behållare med e-postadress som unikt nyckelvillkor och `CompanyID` som partitionsnyckel. När du konfigurerar användarens e-postadress med en unik nyckel varje objekt har en unik e-postadress i en viss `CompanyID`. Det går inte att skapa två objekt med dubbla e-postadresser och med samma partitionsnyckelvärde. 

För att skapa objekt med samma e-postmeddelandet adress, men inte i samma förnamn, efternamn och e-postadress, lägga till flera sökvägar i Unik nyckel principen. Istället för att skapa en unik nyckel som baseras på den e-postadressen, du kan också skapa en unik nyckel med en kombination av förnamn, efternamn och e-postadress. Den här nyckeln är känt som en sammansatt Unik nyckel. I det här fallet varje unik kombination av tre värden inom en viss `CompanyID` tillåts. 

Behållaren kan exempelvis innehålla objekt med följande värden, där varje objekt godkänner unika nyckelvillkor.

|CompanyID|Förnamn|Efternamn|E-postadress|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Om du försöker infoga ett annat objekt med kombinationer som anges i föregående tabell visas felmeddelande ett. Felet indikerar att unika nyckelvillkor inte uppfylldes. Du får något `Resource with specified ID or name already exists` eller `Resource with specified ID, name, or unique index already exists` som ett returnerade meddelande. 

## <a name="define-a-unique-key"></a>Definiera en unik nyckel

Du kan definiera unika nycklar endast när du skapar en Azure Cosmos-behållare. En unik nyckel är begränsad till en logisk partition. I exemplet ovan om du partitionerar behållaren baserat på Postnummer, avslutas att med duplicerade objekt i varje logisk partition. Överväg följande egenskaper när du skapar unika nycklar:

* Du kan inte uppdatera en befintlig behållare för att använda en annan Unik nyckel. Med andra ord, när du har skapat en behållare med en unik nyckel princip kan principen inte ändras.

* Ange en unik nyckel för en befintlig behållare genom att skapa en ny behållare med unika nyckelvillkor. Använd lämplig datamigreringsverktyget för att flytta data från befintliga behållaren till den nya behållaren. SQL-behållare, använda den [datamigreringsverktyget](import-data.md) att flytta data. MongoDB-behållare, använda [mongoimport.exe eller mongorestore.exe](mongodb-migrate.md) att flytta data.

* En unik nyckel princip kan innehålla högst 16 sökväg-värden. Till exempel värdena kan vara `/firstName`, `/lastName`, och `/address/zipCode`. Varje unik nyckel princip kan ha högst 10 unika nyckelvillkor eller kombinationer. Kombinerade sökvägarna för varje unikt index begränsning får inte överskrida 60 byte. I föregående exempel är förnamn, efternamn och e-postadressen tillsammans en begränsning. Den här begränsningen används 3 bort 16 möjliga sökvägar.

* När en behållare har en unik nyckel princip [enhet för programbegäran (RU)](request-units.md) avgifter för att skapa, uppdatera och ta bort ett objekt är något högre.

* Null-optimerade unika nycklar stöds inte. Om vissa unika sökväg värden saknas, är de behandlas som null-värden som ingå i unikhetsbegränsningen. Därför kan det vara ett enskilt objekt med ett null-värde att uppfylla den här begränsningen.

* Unikt nyckelnamnen är skiftlägeskänsliga. Anta exempelvis att en behållare med den unika nyckelvillkor inställd `/address/zipcode`. Om dina data har ett fält med namnet `ZipCode`, Azure Cosmos DB infogar ”null” som den unika nyckeln eftersom `zipcode` är inte densamma som `ZipCode`. På grund av den här skiftlägeskänslighet, kan alla poster med postnummer inte infogas eftersom Dubbletten ”null” bryter Unik nyckel.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partition-data.md).
