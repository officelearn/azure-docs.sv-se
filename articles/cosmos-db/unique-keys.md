---
title: Använda unika nycklar i Azure Cosmos DB
description: Lär dig hur du definierar och använder unika nycklar för en Azure Cosmos-databas. I den här artikeln beskrivs också hur unika nycklar lägger till ett lager av dataintegritet.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: f234579c6fb2b6f1bc0cd518b87ea69fae30093a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869841"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Unika nyckelbegränsningar i Azure Cosmos DB

Unika nycklar lägger till ett lager av dataintegritet i en Azure Cosmos-behållare. Du skapar en unik nyckelprincip när du skapar en Azure Cosmos-behållare. Med unika nycklar kontrollerar du att ett eller flera värden i en logisk partition är unikt. Du kan också garantera unikhet per [partitionsnyckel](partition-data.md).

När du har skapat en behållare med en unik nyckelprincip förhindras skapandet av en ny eller en uppdatering av ett befintligt objekt som resulterar i en dubblett inom en logisk partition, enligt den unika nyckelbegränsningen. Partitionsnyckeln i kombination med den unika nyckeln garanterar det unika för ett objekt i behållarens omfattning.

Tänk dig till exempel en Azure Cosmos-behållare med `CompanyID` e-postadress som den unika nyckelbegränsningen och som partitionsnyckel. När du konfigurerar användarens e-postadress med en unik nyckel har `CompanyID`varje objekt en unik e-postadress inom en viss . Det går inte att skapa två objekt med dubbla e-postadresser och med samma partitionsnyckelvärde. 

Om du vill skapa objekt med samma e-postadress, men inte samma förnamn, efternamn och e-postadress, lägger du till fler sökvägar i den unika nyckelprincipen. I stället för att skapa en unik nyckel som endast baseras på e-postadressen kan du också skapa en unik nyckel med en kombination av förnamn, efternamn och e-postadress. Den här nyckeln kallas en sammansatt unik nyckel. I det här fallet tillåts varje unik `CompanyID` kombination av de tre värdena inom en given. 

Behållaren kan till exempel innehålla objekt med följande värden, där varje objekt uppfyller det unika nyckelvillkoret.

|FöretagID|Förnamn|Efternamn|E-postadress|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam (|   |Duperre|gaby@fabraikam.com|
|Fabrkam (|   |   |gaby@fabraikam.com|

Om du försöker infoga ett annat objekt med kombinationerna i föregående tabell visas ett felmeddelande. Felet anger att den unika nyckelbegränsningen inte uppfylldes. Du får `Resource with specified ID or name already exists` `Resource with specified ID, name, or unique index already exists` antingen eller som ett returmeddelande. 

## <a name="define-a-unique-key"></a>Definiera en unik nyckel

Du kan bara definiera unika nycklar när du skapar en Azure Cosmos-behållare. En unik nyckel begränsas till en logisk partition. I föregående exempel, om du partitionerar behållaren baserat på postnumret, hamnar du med dubblettobjekt i varje logisk partition. Tänk på följande egenskaper när du skapar unika nycklar:

* Du kan inte uppdatera en befintlig behållare för att använda en annan unik nyckel. Med andra ord, när en behållare har skapats med en unik nyckelprincip kan principen inte ändras.

* Om du vill ange en unik nyckel för en befintlig behållare skapar du en ny behållare med det unika nyckelbegränsningen. Använd lämpligt datamigreringsverktyg för att flytta data från den befintliga behållaren till den nya behållaren. För SQL-behållare använder du [verktyget Datamigrering](import-data.md) för att flytta data. För MongoDB-behållare använder du [mongoimport.exe eller mongorestore.exe](mongodb-migrate.md) för att flytta data.

* En unik nyckelprincip kan ha högst 16 sökvägsvärden. Värdena kan till `/firstName`exempel `/lastName`vara `/address/zipCode`, och . Varje unik nyckelprincip kan ha högst 10 unika tangentbegränsningar eller kombinationer. De kombinerade sökvägarna för varje unikt indexvillkor får inte överstiga 60 byte. I föregående exempel är förnamn, efternamn och e-postadress tillsammans ett villkor. Det här villkoret använder 3 av de 16 möjliga sökvägarna.

* När en behållare har en unik nyckelprincip är [ru-avgifter (Request Unit)](request-units.md) för att skapa, uppdatera och ta bort ett objekt något högre.

* Glesa unika nycklar stöds inte. Om några unika sökvägsvärden saknas behandlas de som null-värden som deltar i unikhetsbegränsningen. Därför kan det bara finnas ett enda objekt med ett null-värde för att uppfylla det här villkoret.

* Unika nyckelnamn är skiftlägeskänsliga. Tänk dig till exempel en behållare med `/address/zipcode`det unika nyckelvillkoret inställt på . Om dina data har `ZipCode`ett fält med namnet infogar Azure Cosmos DB "null" som den unika nyckeln eftersom `zipcode` den inte är samma som `ZipCode`. På grund av den här fallkänsligheten kan alla andra poster med postnummer inte infogas eftersom dubbletten "null" bryter mot den unika nyckelbegränsningen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partition-data.md)
* Utforska [hur du definierar unika nycklar](how-to-define-unique-keys.md) när du skapar en behållare
