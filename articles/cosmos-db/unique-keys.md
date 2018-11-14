---
title: Unika nycklar i Azure Cosmos DB
description: Lär dig att använda unika nycklar i din Azure Cosmos DB-databas
author: aliuy
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 006d0ef28d82a7648a56b3bf871c5a3afd6a55a6
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624428"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Unika nycklar i Azure Cosmos DB

Unika nycklar ger dig möjlighet att lägga till ett lager med dataintegritet till en Cosmos-behållare. Du kan skapa en unik nyckel princip när du skapar en Cosmos-behållare. Se till att en eller flera värden i en logisk partition är unikt med unika nycklar (du kan garantera unikhet per [partitionsnyckel](partition-data.md)). När du skapar en behållare med en unik nyckel princip förhindrar det att skapa nya (eller uppdaterade) dubbletterna i en logisk partition som den anges av det unika nyckelvillkoret. Partitionsnyckeln kombineras med unika nyckeln garanterar unikhet för ett objekt i omfånget för behållaren.

Anta exempelvis att en Cosmos-behållare med e-postadress som unikt nyckelvillkor och `CompanyID` som partitionsnyckel. Genom att konfigurera användarens e-postadressen en unik nyckel, kontrollera att varje objekt har en unik e-postadress i en viss `CompanyID`. Det går inte att skapa två objekt med dubbla e-postadresser och med samma partitionsnyckelvärde.  

Om du vill ge användarna möjlighet att skapa flera objekt med samma e-postadress, men inte samma förnamn, senaste namn och e-postadress, kan du lägga till ytterligare sökvägar till principen för unik nyckel. Istället för att skapa en unik nyckel som baseras på den e-postadressen, kan du också skapa en unik nyckel med en kombination av förnamn, efternamn och e-postadress (en sammansatt Unik nyckel). I det här fallet varje unik kombination av tre värden inom en viss `CompanyID` tillåts. Behållaren kan exempelvis innehålla objekt med följande värden där varje objekt respekterar begränsningen för unik nyckel.

|CompanyID|Förnamn|Efternamn|E-postadress|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Om du försöker infoga ett annat objekt med kombinationer som anges i tabellen ovan, visas ett felmeddelande om att unika nyckelvillkor inte var uppfyllt. Får du antingen ”resursen med angivet ID eller namn redan finns” eller ”resurs med angivet ID, namn eller unikt index finns redan” som en return meddelande.  

## <a name="defining-a-unique-key"></a>Definiera en unik nyckel

Du kan definiera unika nycklar bara när du skapar en Cosmos-behållare. En unik nyckel är begränsad till en logisk partition. I exemplet ovan om du partitionerar behållaren baserat på Postnummer, kommer processen att avslutas med en dubblett objekt i varje logisk partition. Överväg följande egenskaper när du skapar unika nycklar:

* Du kan inte uppdatera en befintlig behållare för att använda en annan Unik nyckel. Med andra ord, när en behållare har skapats med en unik nyckel princip kan principen inte ändras.

* Om du vill ange en unik nyckel för en befintlig behållare kan behöva du skapa en ny behållare med unika nyckelvillkor och Använd lämpliga datamigreringsverktyget för att flytta data från befintliga behållare till den nya behållaren. SQL-behållare, använda den [Datamigreringsverktyget](import-data.md) att flytta data. MongoDB-behållare, använda [mongoimport.exe eller mongorestore.exe](mongodb-migrate.md) att flytta data.

* En unik nyckel princip kan innehålla högst 16 sökväg-värden (till exempel: /firstName, /lastName, / adress/postnummer). Varje unik nyckel princip kan ha högst 10 unika nyckelvillkor eller kombinationer och de kombinerade sökvägarna för varje unikt index begränsning får innehålla högst 60 byte. Förnamn, efternamn och e-postadressen är tillsammans bara en begränsning i exemplet ovan och den använder tre av de 16 möjliga sökvägarna.

* När en behållare har en unik nyckel princip begäran i Units (RU) att skapa, uppdatera och ta bort ett objekt att är något högre.

* Null-optimerade unika nycklar stöds inte. Om vissa unika sökväg värden saknas hanteras de som null-värden som ingå i unikhetsbegränsningen. Därför kan finnas det bara ett enskilt objekt med null-värden uppfylla den här begränsningen.

* Unikt nyckelnamnen är skiftlägeskänsliga. Anta exempelvis att en behållare med den unika nyckelvillkor inställd /address/zipcode. Om dina data har ett fält med namnet postnummer, infogar Cosmos DB ”null” som den unika nyckeln eftersom ”postnummer” inte är samma som ”postnummer”. På grund av den här skiftlägeskänslighet, kan det inte infogas eftersom Dubbletten ”null” kommer bryter mot unika nyckelvillkor alla poster med postnummer.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partition-data.md)
