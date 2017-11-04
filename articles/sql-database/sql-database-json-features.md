---
title: Azure SQL Database JSON-funktioner | Microsoft Docs
description: "Azure SQL-databas kan du parse-, fråge- och formatera data i JavaScript Object Notation (JSON) notation."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 55860105-2f5f-4b10-87a0-99faa32b5653
ms.service: sql-database
ms.custom: develop databases
ms.devlang: NA
ms.date: 11/15/2016
ms.author: jovanpop
ms.workload: On Demand
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 8877b0bb779501df003ce11d66d9625c2f99e9ce
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Komma igång med JSON-funktioner i Azure SQL Database
Azure SQL-databas, kan du analysera och frågar efter data som representeras i JavaScript Object Notation [(JSON)](http://www.json.org/) formatera och exportera din relationsdata som JSON-texten.

JSON är ett populärt dataformat som används för att utbyta data i moderna webb- och mobilprogram. JSON används också för att lagra halvstrukturerade data i loggfiler eller i NoSQL-databaser som [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). Många REST-webbtjänsterna sökresultaten formateras som JSON-text eller acceptera data formateras som JSON. De flesta Azure services som [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/), och [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) har REST-slutpunkter som returnerar eller använda JSON.

Azure SQL-databas kan du enkelt arbeta med JSON-data och integrera din databas med moderna tjänster.

## <a name="overview"></a>Översikt
Azure SQL Database tillhandahåller följande funktioner för att arbeta med JSON-data:

![JSON-funktioner](./media/sql-database-json-features/image_1.png)

Om du har JSON-text, du kan extrahera data från JSON eller kontrollera att JSON korrekt formaterad med inbyggda funktioner [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), och [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Den [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) funktionen kan du uppdatera värdet i JSON-texten. Mer avancerade frågor och analys, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funktion kan omvandla en matris av JSON-objekt till en uppsättning rader. SQL-frågan kan köras på den returnerade resultatuppsättningen. Slutligen är det en [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) -sats som gör att du kan formatera data som lagras i relationella tabellerna som JSON-text.

## <a name="formatting-relational-data-in-json-format"></a>Formatera relationella data i JSON-format
Om du har en webbtjänst som tar data från databasen för lager och ger ett svar i JSON-format eller klientens JavaScript-ramverk och bibliotek som accepterar data formateras som JSON kan formatera du innehållet i databasen som JSON direkt i en SQL-fråga. Du inte längre behöver skriva kod för programmet som resultat från Azure SQL Database som JSON-format eller vissa JSON-serialisering biblioteket för att konvertera tabellfråga resultat och serialisera objekt till JSON-format. Använd istället FOR JSON-satsen för att formatera resultatet för SQL-frågan som JSON i Azure SQL Database och använda det direkt i ditt program.

I följande exempel formateras rader från tabellen Sales.Customer som JSON med FOR JSON-sats:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Satsen FOR JSON PATH formaterar resultatet av frågan som JSON-texten. Kolumnnamn används som nycklar, även om cellvärden genereras som JSON-värden:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Resultatmängden formateras som en JSON-matris där varje rad formateras som en separat JSON-objekt.

SÖKVÄGEN anger att du kan anpassa utdataformatet för JSON-resultat med hjälp av punktnotation i kolumnalias. Följande fråga ändrar namnet på nyckeln ”CustomerName” i JSON-utdataformat och placerar telefon-och faxnummer i det underordnade objektet ”kontakta”:

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Utdata från den här frågan ser ut så här:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

I det här exemplet returneras vi ett JSON-objekt i stället för en matris genom att ange den [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) alternativet. Du kan använda det här alternativet om du vet att du returnerar ett objekt på grund av frågan.

Största värdet för FOR JSON-satsen är att du kan returnera komplexa hierarkiska data från databasen formaterade som kapslade JSON-objekt eller matriser. I följande exempel visas hur du lägger till order som hör till kunden som en kapslad order-matris:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

I stället för att skicka separata frågor för att hämta kunddata och sedan för att hämta en lista över relaterade order, kan du alla nödvändiga data med en enskild fråga som visas i följande exempel på utdata:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Arbeta med JSON-data
Om du inte har strikt strukturerade data om du har komplexa underordnade objekt, matriser eller hierarkiska data, eller om din datastrukturer utvecklas över tid, kan JSON-format hjälpa dig att representera alla komplexa datastruktur.

JSON är en textrepresentation format som kan användas som en annan strängtyp i Azure SQL Database. Du kan skicka eller lagra JSON-data som standard NVARCHAR:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

JSON-data som används i det här exemplet representeras med hjälp av typen NVARCHAR(MAX). JSON kan infogas i den här tabellen eller tillhandahålls som ett argument av den lagrade proceduren med standard Transact-SQL-syntax som visas i följande exempel:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Alla språk för klientsidan eller som fungerar med strängdata i Azure SQL Database-biblioteket fungerar även med JSON-data. JSON kan lagras i en tabell som har stöd för NVARCHAR-typ, till exempel en minnesoptimerad tabell eller en systemversionstabell. JSON införs inte någon begränsning i koden för klientsidan eller i databas-lagret.

## <a name="querying-json-data"></a>Hämtning av JSON-data
Om du har data som är formaterade som JSON som lagras i Azure SQL-tabeller, kan du använda dessa data i SQL-frågan i JSON-funktioner.

JSON-funktioner som är tillgängliga i Azure SQL-databasen kan du hantera data som är formaterade som JSON som alla andra SQL-datatyper. Du kan enkelt extrahera värden från JSON-texten och använda JSON-data i en fråga:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funktionen JSON_VALUE extraherar ett värde från JSON-texten som lagras i kolumnen. Den här funktionen använder en JavaScript-liknande sökväg för att referera till ett värde i JSON-texten ska extraheras. Det extraherade värdet kan användas i valfri del av SQL-frågan.

Funktionen JSON_QUERY liknar JSON_VALUE. Till skillnad från JSON_VALUE extraherar komplexa underordnade objekt, till exempel matriser eller objekt som är placerade i JSON-texten i den här funktionen.

Funktionen JSON_MODIFY kan du ange sökvägen till värdet i JSON-texten som ska uppdateras, samt ett nytt värde som ska skriva över den gamla servern. Det här sättet kan du enkelt uppdatera JSON-texten utan reparsing hela strukturen.

Eftersom JSON finns i vanlig text, finns det inga garantier för att de värden som lagras i textkolumner korrekt formaterad. Du kan kontrollera att texten som lagras i JSON-kolumn har utformats korrekt genom att använda standard Kontrollbegränsningar för Azure SQL Database och funktionen ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Om indatatexten är korrekt formaterad JSON, returnerar funktionen ISJSON värdet 1. Det här villkoret kommer på varje insert eller update för JSON-kolumnen Kontrollera att nya textvärdet inte är felaktiga JSON.

## <a name="transforming-json-into-tabular-format"></a>Omvandla JSON i tabellformat
Azure SQL-databas kan du omvandla JSON-samlingar i tabelldata för JSON-format och Läs in eller frågan.

OPENJSON är en funktion för tabell-värde som Parsar JSON-text, söker efter en matris av JSON-objekt, går igenom element i matrisen och returnerar en rad i det utgående resultatet för varje element i matrisen.

![JSON tabular](./media/sql-database-json-features/image_2.png)

I exemplet ovan kan vi ange var att hitta JSON-matris som ska öppnas (i $. Order sökväg), vilka kolumner som ska returneras som resultat och var du hittar de JSON-värden som ska returneras som celler.

Vi kan omvandla en JSON-matris på den @orders variabel till en uppsättning rader, analysera resultatuppsättningen eller infoga rader i en tabell som standard:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
Insamling av order formateras som en JSON-matris och tillhandahållas som parameter till den lagrade proceduren kan parsas och infogas i tabellen Order.

## <a name="next-steps"></a>Nästa steg
Checka ut dessa resurser om du vill veta hur du integrerar JSON i ditt program:

* [TechNet-blogg](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
* [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn921897.aspx)
* [Channel 9 video](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Mer information om olika scenarier för att integrera JSON i ditt program, se demonstrationer i det här [Channel 9 video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) eller söka efter ett scenario som matchar dina användningsfall i [JSON blogginlägg](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

