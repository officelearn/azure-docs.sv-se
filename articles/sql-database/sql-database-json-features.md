---
title: Arbeta med JSON-data
description: Med Azure SQL Database kan du tolka, fråga och formatera data i JSON-notation (JavaScript Object Notation).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 992c981d49e7c6fbf8b6156570f6554a05caab5d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687762"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Komma igång med JSON-funktioner i Azure SQL Database
Med Azure SQL Database kan du tolka och fråga data som representeras i [JSON-format (JavaScript](https://www.json.org/) Object Notation) och exportera relationsdata som JSON-text. Följande JSON-scenarier är tillgängliga i Azure SQL Database:
- [Formatera relationsdata i JSON-format](#formatting-relational-data-in-json-format) med sats. `FOR JSON`
- [Arbeta med JSON-data](#working-with-json-data)
- [Fråga JSON-data](#querying-json-data) med JSON-skalärfunktioner.
- [Omvandla JSON till tabellformat](#transforming-json-into-tabular-format) med hjälp av `OPENJSON` funktionen.

## <a name="formatting-relational-data-in-json-format"></a>Formatera relationsdata i JSON-format
Om du har en webbtjänst som tar data från databaslagret och ger ett svar i JSON-format, eller JavaScript-ramar eller bibliotek på klientsidan som accepterar data som är formaterade som JSON, kan du formatera databasinnehållet som JSON direkt i en SQL-fråga. Du behöver inte längre skriva programkod som formaterar resultat från Azure SQL Database som JSON, eller inkludera några JSON-serialiseringsbibliotek för att konvertera tabellfrågeresultat och sedan serialisera objekt till JSON-format. I stället kan du använda FOR JSON-satsen för att formatera SQL-frågeresultat som JSON i Azure SQL Database och använda den direkt i ditt program.

I följande exempel formateras rader från tabellen Sales.Customer som JSON med hjälp av FOR JSON-satsen:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

FOR JSON PATH-satsen formaterar resultatet av frågan som JSON-text. Kolumnnamn används som nycklar, medan cellvärdena genereras som JSON-värden:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Resultatuppsättningen är formaterad som en JSON-matris där varje rad är formaterad som ett separat JSON-objekt.

PATH anger att du kan anpassa utdataformatet för JSON-resultatet med hjälp av punktatering i kolumnalias. Följande fråga ändrar namnet på nyckeln "CustomerName" i utdata-JSON-formatet och placerar telefon- och faxnummer i underobjektet "Kontakt":

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Utdata för den här frågan ser ut så här:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

I det här exemplet returnerade vi ett enda JSON-objekt i stället för en matris genom att ange [alternativet WITHOUT_ARRAY_WRAPPER.](https://msdn.microsoft.com/library/mt631354.aspx) Du kan använda det här alternativet om du vet att du returnerar ett enskilt objekt som ett resultat av frågan.

Huvudvärdet i FOR JSON-satsen är att du kan returnera komplexa hierarkiska data från databasen som är formaterade som kapslade JSON-objekt eller matriser. I följande exempel visas hur du `Orders` tar med raderna från tabellen som tillhör den `Customer` kapslade matrisen `Orders`i :

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

I stället för att skicka separata frågor för att hämta kunddata och sedan hämta en lista över relaterade order kan du hämta alla nödvändiga data med en enda fråga, som visas i följande exempelutdata:

```json
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
Om du inte har strikt strukturerade data, om du har komplexa underobjekt, matriser eller hierarkiska data, eller om dina datastrukturer utvecklas med tiden, kan JSON-formatet hjälpa dig att representera en komplex datastruktur.

JSON är ett textformat som kan användas som alla andra strängtyper i Azure SQL Database. Du kan skicka eller lagra JSON-data som en vanlig NVARCHAR:

```sql
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

JSON-data som används i det här exemplet representeras med hjälp av typen NVARCHAR(MAX). JSON kan infogas i den här tabellen eller tillhandahållas som ett argument för den lagrade proceduren med standardtransakt-SQL-syntax som visas i följande exempel:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Alla språk eller bibliotek på klientsidan som fungerar med strängdata i Azure SQL Database fungerar också med JSON-data. JSON kan lagras i alla tabeller som stöder NVARCHAR-typen, till exempel en minnesoptimerad tabell eller en tabell med systemversion. JSON introducerar inte någon begränsning vare sig i klientkoden eller i databaslagret.

## <a name="querying-json-data"></a>Fråga JSON-data
Om du har data formaterad som JSON lagrad i Azure SQL-tabeller kan du använda dessa data i alla SQL-frågor med JSON.

Med JSON-funktioner som är tillgängliga i Azure SQL-databasen kan du behandla data som är formaterade som JSON som alla andra SQL-datatyper. Du kan enkelt extrahera värden från JSON-texten och använda JSON-data i valfri fråga:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funktionen JSON_VALUE extraherar ett värde från JSON-text som lagras i kolumnen Data. Den här funktionen använder en JavaScript-liknande sökväg för att referera till ett värde i JSON-text som ska extraheras. Det extraherade värdet kan användas i alla delar av SQL-frågan.

Funktionen JSON_QUERY liknar JSON_VALUE. Till skillnad från JSON_VALUE extraherar den här funktionen komplexa underobjekt, till exempel matriser eller objekt som placeras i JSON-text.

Med funktionen JSON_MODIFY kan du ange sökvägen till värdet i JSON-texten som ska uppdateras, samt ett nytt värde som ska skriva över det gamla. På så sätt kan du enkelt uppdatera JSON-text utan att ändra hela strukturen.

Eftersom JSON lagras i en standardtext finns det inga garantier för att de värden som lagras i textkolumner är korrekt formaterade. Du kan kontrollera att text som lagras i JSON-kolumnen är korrekt formaterad med hjälp av standardkontrollbegränsningar för Azure SQL Database och funktionen ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Om indatatexten är korrekt formaterad JSON returnerar funktionen ISJSON värdet 1. Vid varje infogning eller uppdatering av JSON-kolumn kontrollerar det här villkoret att det nya textvärdet inte är felaktigt lömgivet JSON.

## <a name="transforming-json-into-tabular-format"></a>Omvandla JSON till tabellformat
Med Azure SQL Database kan du också omvandla JSON-samlingar till tabellformat och läsa in eller fråga JSON-data.

OPENJSON är en tabell-värde funktion som tolkar JSON text, lokaliserar en matris med JSON-objekt, itererar genom elementen i matrisen, och returnerar en rad i utdataresultatet för varje element i matrisen.

![JSON-tabell](./media/sql-database-json-features/image_2.png)

I exemplet ovan kan vi ange var JSON-matrisen ska öppnas (i $. Ordersökväg), vilka kolumner som ska returneras som resultat och var JSON-värdena som ska returneras som celler ska hittas.

Vi kan omvandla en JSON-matris i variabeln @orders till en uppsättning rader, analysera den här resultatuppsättningen eller infoga rader i en standardtabell:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

Samlingen av order som är formaterad som en JSON-matris och som anges som en parameter till den lagrade proceduren kan tolkas och infogas i tabellen Order.

## <a name="next-steps"></a>Nästa steg
Om du vill veta hur du integrerar JSON i ditt program kan du läsa in följande resurser:

* [TechNet Blogg](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [MSDN-dokumentation](https://msdn.microsoft.com/library/dn921897.aspx)
* [Kanal 9-video](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Om du vill veta mer om olika scenarier för att integrera JSON i ditt program, se demos i denna [Kanal 9 video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) eller hitta ett scenario som matchar ditt användningsfall i [JSON blogginlägg](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

