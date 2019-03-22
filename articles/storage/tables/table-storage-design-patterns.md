---
title: Designmönster för Azure storage-table | Microsoft Docs
description: Använda mönster för Azure table service-lösningar.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f2f4fb04ac483f7716c0b7a0fb1f87843d8b817f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995308"
---
# <a name="table-design-patterns"></a>Mönster för tabelldesign
Den här artikeln beskrivs vissa mönster som är lämplig för användning med lösningar för Table service. Dessutom visas hur du praktiskt taget kan lösa vissa problem och kompromisser diskuteras i andra artiklar i Table storage design. Följande diagram sammanfattar relationerna mellan de olika mönster:  

![att söka efter relaterade data](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Mönstret kartan ovan visar relationer mellan (blå) mönster och antimönster (orange) som finns dokumenterade i den här guiden. Det finns för många andra mönster som är värda att hänsyn tagits till. Till exempel ett av scenarierna för Tabelltjänsten är att använda den [mönster för materialiserad vy](https://msdn.microsoft.com/library/azure/dn589782.aspx) från den [kommandot fråga ansvar uppdelning (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) mönster.  

## <a name="intra-partition-secondary-index-pattern"></a>Mönster för Intra-partition sekundärt index
Store flera kopior av varje entitet med hjälp av olika **RowKey** värden (i samma partition) att aktivera snabb och effektiv sökningar och alternativa sorteringsordningar med hjälp av olika **RowKey** värden. Uppdateringar mellan kopior kan vara konsekvent med EGT'S.  

### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av den **PartitionKey** och **RowKey** värden. På så sätt kan ett klientprogram att hämta en entitet som effektivt med hjälp av dessa värden. Till exempel använder tabellstrukturen visas nedan, ett klientprogram kan använda en punkt-fråga för att hämta en enskild medarbetare entitet med hjälp av ID och ett avdelningsnamn (den **PartitionKey** och **RowKey**  värden). En klient kan också hämta entiteter sorterade efter anställnings-ID i varje avdelning.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Om du vill ska kunna hitta en anställd entitet som baseras på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektivt partition genomsökning för att hitta en matchning. Det beror på att table service inte tillhandahåller sekundära index. Dessutom är det inget alternativ för att begära en lista över anställda sorterad i en annan ordning än **RowKey** ordning.  

### <a name="solution"></a>Lösning
Undvik bristen på sekundära index, kan du lagra flera kopior av varje entitet med varje kopia med ett annat **RowKey** värde. Om du lagrar en entitet med strukturer som visas nedan kan kan du enkelt hämta anställdas enheter baserat på e-postadress eller medarbetare-ID. Prefixet värden för den **RowKey**, ”empid_” och ”email_” kan du fråga för en enskild anställd eller en mängd anställda med hjälp av en mängd e-postadresser eller medarbetare-ID: n.  

![Medarbetaren entiteter](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Följande två filtervillkoren (en Leta upp av anställnings-ID och en Leta upp av e-postadress) ange både punktfrågor:  

* $filter = (PartitionKey eq ”försäljning”) och (RowKey eq 'empid_000223 ”)  
* $filter = (PartitionKey eq ”försäljning”) och (RowKey eq 'email_jonesj@contoso.com”)  

Om du frågar efter ett intervall med anställdas enheter, du kan ange ett intervall som är sorterad i anställdas ID ordning eller ett intervall som är sorterad i e-postadress ordning genom att fråga om entiteter med ett prefix i den **RowKey**.  

* Du hittar ID alla anställda på försäljningsavdelningen med en medarbetare i intervallet 000100 000199 användning: $filter = (PartitionKey eq ”försäljning”) och (RowKey ge ”empid_000100”) och (RowKey le 'empid_000199 ”)  
* Du hittar alla anställda på försäljningsavdelningen med en e-postadress som börjar med bokstaven ”a” Använd: $filter = (PartitionKey eq ”försäljning”) och (RowKey ge ”email_a”) och (RowKey ll ”email_b”)  
  
  Observera att syntaxen för filtret som används i exemplen ovan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Table storage är relativt billiga att använda kostnaden arbetet med att lagra duplicerade data bör inte vara en stor utmaning. Du bör dock alltid utvärdera kostnaden för din design utifrån din förväntade lagringsbehov och bara lägga till dubbla entiteter för att stödja de frågor som ska köra klientprogrammet.  
* Eftersom sekundära index entiteterna lagras i samma partition som de ursprungliga entiteterna, bör du kontrollera att du inte överskrider det för skalbarhetsmål för en enskild partition.  
* Du kan behålla dina dubbla entiteter överensstämmer med varandra med hjälp av EGTs för att uppdatera två kopior av entiteten atomiskt. Detta innebär att du ska lagra alla kopior av en entitet i samma partition. Mer information finns i avsnittet [med hjälp av Entitetsgrupptransaktioner](table-storage-design.md#entity-group-transactions).  
* Det värde som används för den **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatt nyckelvärden.  
* Utfyllnad numeriska värden i den **RowKey** (exempelvis anställdas ID 000223), kan korrigera sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis att duplicera alla egenskaperna för entiteten. Till exempel om frågorna som lookup entiteter med hjälp av e-postmeddelandet adressen i den **RowKey** behöver aldrig medarbetarens ålder, dessa entiteter kan ha följande struktur:

   ![Medarbetare entitetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Det är vanligtvis bättre att lagra duplicerade data och se till att du kan hämta alla data som du behöver med en enda fråga, än att använda en fråga för att hitta en entitet och en annan för att hämta nödvändiga data.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när klientprogrammet måste hämta entiteter med hjälp av en mängd olika nycklar när din klient behöver hämta entiteter i olika sorteringsordningar, och där du kan identifiera varje entitet med hjälp av en rad unika värden. Dock bör du se till att du inte överskrider skalbarhetsgränserna för partition när du utför entitet sökningar med hjälp av de olika **RowKey** värden.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för kommunikation mellan sekundära Partitionsindex](#inter-partition-secondary-index-pattern)
* [Sammansatt nyckel mönster](#compound-key-pattern)
* Entitetsgrupptransaktioner
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Mönster för kommunikation mellan sekundära Partitionsindex
Store flera kopior av varje entitet med hjälp av olika **RowKey** värden i separata partitioner eller i separata tabeller för att aktivera snabb och effektiv uppslag och alternativ sorteringsordningar med hjälp av olika **RowKey**värden.  

### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av den **PartitionKey** och **RowKey** värden. På så sätt kan ett klientprogram att hämta en entitet som effektivt med hjälp av dessa värden. Till exempel använder tabellstrukturen visas nedan, ett klientprogram kan använda en punkt-fråga för att hämta en enskild medarbetare entitet med hjälp av ID och ett avdelningsnamn (den **PartitionKey** och **RowKey**  värden). En klient kan också hämta entiteter sorterade efter anställnings-ID i varje avdelning.  

![Anställnings-ID](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Om du vill ska kunna hitta en anställd entitet som baseras på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektivt partition genomsökning för att hitta en matchning. Det beror på att table service inte tillhandahåller sekundära index. Dessutom är det inget alternativ för att begära en lista över anställda sorterad i en annan ordning än **RowKey** ordning.  

Du att ett mycket stort antal transaktioner mot dessa entiteter och vill du minimera risken för tabelltjänsten begränsning din klient.  

### <a name="solution"></a>Lösning
Undvik bristen på sekundära index, kan du lagra flera kopior av varje entitet med varje kopia med hjälp av olika **PartitionKey** och **RowKey** värden. Om du lagrar en entitet med strukturer som visas nedan kan kan du enkelt hämta anställdas enheter baserat på e-postadress eller medarbetare-ID. Prefixet värden för den **PartitionKey**, ”empid_” och ”email_” kan du identifiera det index som du vill använda för en fråga.  

![Primärt index och sekundärt index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Följande två filtervillkoren (en Leta upp av anställnings-ID och en Leta upp av e-postadress) ange både punktfrågor:  

* $filter = (PartitionKey eq ' empid_Sales ”) och (RowKey eq '000223”)
* $filter = (PartitionKey eq ' email_Sales ”) och (RowKey eq 'jonesj@contoso.com”)  

Om du frågar efter ett intervall med anställdas enheter, du kan ange ett intervall som är sorterad i anställdas ID ordning eller ett intervall som är sorterad i e-postadress ordning genom att fråga om entiteter med ett prefix i den **RowKey**.  

* Du hittar alla anställda på försäljningsavdelningen med en anställnings-ID i intervallet **000100** till **000199** sorterade medarbetare ID kunna använda: $filter = (PartitionKey eq ' empid_Sales ”) och (RowKey ge '000100') och (RowKey le '000199 ”)  
* Du hittar alla anställda på försäljningsavdelningen med en e-postadress som börjar med ”a” i e-postadress kunna använda: $filter = (PartitionKey eq ' email_Sales ”) och (RowKey ge” a ”) och (RowKey ll” b ”)  

Observera att syntaxen för filtret som används i exemplen ovan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du kan behålla dina dubbla entiteter konsekvent med varandra med hjälp av den [konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) att underhålla de primära och sekundära index entiteterna.  
* Table storage är relativt billiga att använda kostnaden arbetet med att lagra duplicerade data bör inte vara en stor utmaning. Du bör dock alltid utvärdera kostnaden för din design utifrån din förväntade lagringsbehov och bara lägga till dubbla entiteter för att stödja de frågor som ska köra klientprogrammet.  
* Det värde som används för den **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatt nyckelvärden.  
* Utfyllnad numeriska värden i den **RowKey** (exempelvis anställdas ID 000223), kan korrigera sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis att duplicera alla egenskaperna för entiteten. Till exempel om frågorna som lookup entiteter med hjälp av e-postmeddelandet adressen i den **RowKey** behöver aldrig medarbetarens ålder, dessa entiteter kan ha följande struktur:
  
   ![Medarbetaren entitet (sekundära index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Det är vanligtvis bättre att lagra duplicerade data och se till att du kan hämta alla data som du behöver med en enda fråga att använda en fråga för att hitta en entitet med hjälp av det sekundära indexet och en annan för sökning nödvändiga data i det primära indexet än.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när klientprogrammet måste hämta entiteter med hjälp av en mängd olika nycklar när din klient behöver hämta entiteter i olika sorteringsordningar, och där du kan identifiera varje entitet med hjälp av en rad unika värden. Använd det här mönstret när du vill undvika att du överskrider skalbarhetsgränserna för partition när du utför entitet sökningar med hjälp av de olika **RowKey** värden.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* [Mönster för Intra-partition sekundärt index](#intra-partition-secondary-index-pattern)  
* [Sammansatt nyckel mönster](#compound-key-pattern)  
* Entitetsgrupptransaktioner  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Konsekvent transaktioner mönster
Aktivera konsekvent beteenden över partitionsgränser eller gränser för storage-system med hjälp av Azure-köer.  

### <a name="context-and-problem"></a>Kontext och problem
EGTs aktivera atomiska transaktioner över flera enheter som delar samma partitionsnyckel. För bättre prestanda och skalbarhet, kanske du vill lagra entiteter som har krav på datakonsekvens i separata partitioner eller i ett separat lagringssystem: i ett sådant scenario, du kan inte använda EGTs för att upprätthålla konsekvens. Du kan till exempel ha ett krav att upprätthålla konsekvens mellan:  

* Entiteter som lagras i två olika partitioner i samma tabell, i olika tabeller eller i olika lagringskonton.  
* En entitet som lagras i tabelltjänsten och en blob som lagras i Blob-tjänsten.  
* En entitet som lagras i tabelltjänsten och en fil i ett filsystem.  
* En entitet store i Table service indexerats ännu med hjälp av Azure Search-tjänsten.  

### <a name="solution"></a>Lösning
Du kan implementera en lösning som ger konsekvens mellan två eller flera partitioner eller lagringssystem med hjälp av Azure-köer.
Anta att du har ett krav för att kunna arkivera gamla anställdas enheter för att visa den här metoden kan. Den gamla anställdas enheter efterfrågas sällan och bör undantas från alla aktiviteter som handlar om aktuella anställda. För att implementera det här kravet du lagrar aktiva medarbetare i den **aktuella** tabell och tidigare anställda i den **Arkiv** tabell. Arkivera en anställd måste du ta bort enheten från den **aktuella** tabellen och Lägg till entitet till den **Arkiv** tabell, men du kan inte använda en EGT för att utföra de här två åtgärderna. För att undvika risken för att ett fel gör en entitet som ska visas i båda eller ingen tabeller, måste arkivåtgärden vara konsekvent. Följande sekvensdiagram illustrerar stegen i den här åtgärden. Mer information ges för undantag sökvägar i följande text.  

![Lösningen för Azure-köer](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

En klient initierar Arkiv igen genom att placera ett meddelande på en Azure-kö, i det här exemplet att arkivera medarbetare #456. En arbetsroll söker i kön för nya meddelanden. När den hittar en läser meddelandet och lämnar en dold kopia för kön. Arbetsrollen bredvid hämtar en kopia av entiteten från den **aktuella** tabellen, infogar en kopia i den **Arkiv** tabellen och tar sedan bort ursprungligt från den **aktuella** tabell. Slutligen, om det finns några fel från föregående steg, arbetsrollen tar bort dolda meddelandet från kön.  

I det här exemplet steg 4 infogar medarbetaren till den **Arkiv** tabell. Det kan lägga till medarbetaren till en blob i Blob-tjänsten eller en fil i ett filsystem.  

### <a name="recovering-from-failures"></a>Återställa från fel
Det är viktigt som åtgärder i steg **4** och **5** måste vara *idempotenta* ifall arbetsrollen behöver starta om arkivåtgärden. Om du använder tabelltjänsten för steget **4** bör du använda en ”infoga eller ersätta” åtgärd; för steg **5** bör du använda en ”ta bort om det finns” igen om du använder klientbiblioteket. Om du använder en annan lagringssystemet, måste du använda en lämplig idempotent åtgärd.  

Om arbetsrollen slutförs aldrig steg **6**, och sedan efter en tidsgräns som meddelandet visas igen för kön som är redo för worker-roll att bearbeta den på nytt. Worker-roll kan kontrollera hur många gånger ett meddelande i kön har läsa och, om det behövs, Flagga det är en ”skadligt” meddelande för undersökning genom att skicka den till en separat kö. Läs mer om läsning av Kömeddelanden och kontrollera antalet ta [hämta meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Fel från tabell och kö tjänsterna tillfälliga fel och klientprogrammet bör vara lämplig omprövningslogiken för att hantera dem.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen ger inte för transaktionsisoleringen. Till exempel en klient kan läsa den **aktuella** och **Arkiv** tabeller när arbetsrollen har mellan stegen **4** och **5**, och se en Inkonsekvent vy över data. Observera att data ska vara konsekvent så småningom.  
* Du måste vara säker på att steg 4 och 5 är idempotenta för att säkerställa konsekvens.  
* Du kan skala lösningen med hjälp av flera köer och worker-rollinstanser.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill att garantera konsekvens mellan entiteter som finns i olika partitioner eller tabeller. Du kan utöka det här mönstret för att säkerställa konsekvens för åtgärder i Table service och Blob-tjänsten och andra Azure Storage datakällor, till exempel databasen eller filsystemet.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Entitetsgrupptransaktioner  
* [Sammanfoga eller ersätta](#merge-or-replace)  

> [!NOTE]
> Om transaktionsisoleringen är viktigt att din lösning, bör du göra om tabellerna så att du kan använda EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Mönster för index-entiteter
Underhålla index entiteter för att aktivera effektiv sökning som returnerar en lista över entiteter.  

### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av den **PartitionKey** och **RowKey** värden. På så sätt kan ett klientprogram att hämta en entitet som effektivt med en punkt-fråga. Till exempel använder tabellstrukturen visas nedan, ett klientprogram enkelt kan hämta en enskild medarbetare entitet med hjälp av ID och ett avdelningsnamn (den **PartitionKey** och **RowKey**).  

![Medarbetaren entitet](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Om du vill kunna hämta en lista över anställda entiteter baserat på värdet för en annan icke-unikt egenskap, till exempel efternamn, måste du använda en mindre effektivt partition-sökning för att hitta matchningar i stället för att använda ett index för att leta upp dem direkt. Det beror på att table service inte tillhandahåller sekundära index.  

### <a name="solution"></a>Lösning
Om du vill aktivera sökning efter efternamn med entitetsstruktur som anges ovan, måste du ha en lista över anställda ID: n. Om du vill hämta medarbetare entiteter med ett visst efternamn, till exempel Jones, måste du först lokalisera listan över anställdas ID: n för anställda med Jones som efternamn och hämtar sedan dessa medarbetare entiteter. Det finns tre huvudsakliga alternativ för att lagra listor med medarbetare-ID: n:  

* Använda blob storage.  
* Skapa index entiteter i samma partition som anställdas enheter.  
* Skapa index entiteter i en separat partition eller tabellen.  

<u>Alternativ #1: Använda blob storage</u>  

För det första alternativet, du skapa en blob för varje unikt efternamn och varje blob store en lista över de **PartitionKey** (avdelning) och **RowKey** (anställnings-ID) värden för anställda som har det senaste namnet. När du lägger till eller ta bort en medarbetare bör du kontrollera att innehållet i den relevanta blobben är konsekvent med enheterna som anställda.  

<u>Alternativ #2:</u> Skapa index entiteter i samma partition  

För det andra alternativet, använder du index entiteter som lagrar följande data:  

![Medarbetaren index entitet](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

Den **EmployeeIDs** egenskapen innehåller en lista över anställda ID: n för anställda med efternamn som lagras i den **RowKey**.  

Följande steg beskriver hur du bör följa när du lägger till en ny medarbetare om du använder det andra alternativet. I det här exemplet lägger vi till en medarbetare med Id 000152 och efternamn Jones på försäljningsavdelningen:  

1. Hämta entiteten index med en **PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jones”. Spara ETag för den här entiteten som ska användas i steg 2.  
2. Skapa entiteten grupp transaktion (det vill säga en batchåtgärd) som infogar entiteten medarbetare (**PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”000152”), och uppdaterar indexet entiteten (**PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jones”) genom att lägga till nya anställnings-ID i listan i fältet EmployeeIDs. Mer information om entitetsgrupptransaktioner finns i Entitetsgrupptransaktioner.  
3. Om entitet grupp transaktionen misslyckas på grund av en Optimistisk samtidighet-fel (någon annan har bara ändrat entiteten index), måste du börja om igen i steg 1.  

Du kan använda en liknande metod för att ta bort en medarbetare om du använder det andra alternativet. Ändra en anställds efternamn är lite mer komplext eftersom du behöver att köra en entitet grupp transaktion som uppdaterar tre entiteter: anställd entiteten och entiteten index för det gamla efternamnet entiteten index för det nya efternamnet. Varje entitet måste du hämta innan du gör några ändringar för att hämta de ETag-värden som du sedan kan använda för att utföra uppdateringarna med Optimistisk samtidighet.  

Följande steg beskriver hur du bör följa när du behöver leta upp alla anställda med ett visst efternamn på en avdelning om du använder det andra alternativet. Det här exemplet letar vi upp alla anställda med efternamn Jones på försäljningsavdelningen:  

1. Hämta entiteten index med en **PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jones”.  
2. Parsa listan över anställnings-ID i fältet EmployeeIDs.  
3. Om du behöver ytterligare information om var och en av dessa anställda (till exempel sina e-postadresser) kan du hämta var och en av anställdas entiteter med hjälp av **PartitionKey** värdet ”Försäljning” och **RowKey** värden från den lista över anställda som du hämtade i steg 2.  

<u>Alternativ #3:</u> Skapa index entiteter i en separat partition eller tabell  

Det tredje alternativet Använd index entiteter som lagrar följande data:  

![Medarbetaren index entitet i en separat partition](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


Den **EmployeeIDs** egenskapen innehåller en lista över anställda ID: n för anställda med efternamn som lagras i den **RowKey**.  

Du kan inte använda EGTs med det tredje alternativet för att underhålla konsekvensen eftersom indexet entiteter finns i en separat partition från anställdas enheter. Du bör se till att index entiteter är konsekvent med enheterna som anställda.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Denna lösning kräver minst två frågor för att hämta matchande entiteter: en för att fråga indexet entiteter för att hämta listan över **RowKey** värden och frågor för att hämta varje entitet i listan.  
* Tanke på att en enskild entitet har en maximal storlek på 1 MB, förutsätter #2 och 3 # i lösningen att listan över anställdas ID: n för alla angivna efternamn aldrig är större än 1 MB. Om listan över anställdas ID: n är troligt att vara större än 1 MB i storlek, Använd alternativ #1 och lagra indexet data i blob storage.  
* Om du använder alternativet #2 måste (med EGTs som hanterar att lägga till och ta bort anställda och ändra en anställds efternamn) du utvärdera om mängden transaktioner kommer närma dig skalbarhetsgränserna i en given partition. Om så är fallet bör du överväga en konsekvent lösning (alternativ #1 eller #3) som använder köer för att hantera uppdateringsbegäranden och gör det möjligt att lagra dina index entiteter i en separat partition från medarbetare entiteter.  
* Alternativ #2 i den här lösningen förutsätter att du vill leta upp efter efternamn inom en avdelning: till exempel du vill hämta en lista över anställda med ett efternamn Jones på försäljningsavdelningen. Om du vill kunna söka efter alla anställda med ett efternamn Jones i hela organisationen använda antingen alternativ #1 eller #3.
* Du kan implementera en Köbaserad lösning som ger slutlig konsekvens (se den [konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) för mer information).  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill söka efter en uppsättning entiteter som alla har en gemensam egenskapsvärdet, till exempel alla anställda med efternamn Jones.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* Entitetsgrupptransaktioner  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Denormalisering mönster
Kombinera relaterade data tillsammans i en enda enhet så att du kan hämta alla data som du behöver med en enda fråga.  

### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas normalisera du normalt data för att ta bort duplicering, vilket resulterar i frågor som hämtar data från flera tabeller. Om du normalisera data i Azure-tabeller, måste du se flera kommunikationsturer från klienten till servern för att hämta relaterade data. Till exempel med tabellstrukturen nedan om du behöver två tur och RETUR att hämta information för en avdelning: en för att hämta entiteten avdelning som innehåller hanterare användarens ID och sedan en annan begäran att hämta chefens information i en anställd entitet.  

![Avdelning och medarbetare](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Lösning
I stället för att lagra data i två separata entiteter, avnormalisera data och behålla en kopia av chefens information i entiteten avdelning. Exempel:  

![Avdelning entitet](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Avdelning entiteter som lagras med de här egenskaperna, kan du nu hämta all information du behöver om en avdelning med hjälp av en punkt-fråga.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Det finns en kostnad som associeras med att lagra vissa data två gånger. Prestanda-förmån (som färre förfrågningar till lagringstjänsten) vanligtvis uppväger den marginell ökade kostnader för lagring (och den här kostnaden är delvis förskjutning genom en minskning av antalet transaktioner som du behöver för att hämta information om en avdelning ).  
* Du måste ha två entiteter som lagrar information om hanterare av konsekvens. Du kan hantera konsekvens problemet med hjälp av EGTs för att uppdatera flera entiteter i en enda atomisk transaktion: i det här fallet avdelning entiteten och entiteten medarbetare för avdelning manager lagras i samma partition.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du ofta behöva leta upp relaterad information. Det här mönstret minskar antalet frågor som din klient måste göra för att hämta de data som krävs.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* Entitetsgrupptransaktioner  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Sammansatt nyckel mönster
Använd sammansatta **RowKey** värden att aktivera en klient att söka efter relaterade data med en enda fråga.  

### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas är det ganska naturligt att använda kopplingar i frågor för att gå tillbaka relaterade typer av data till klienten i en enskild fråga. Du kan exempelvis använda anställnings-ID för att leta upp en lista över relaterade entiteter som innehåller prestanda och granska data för denna medarbetare.  

Anta att du lagrar medarbetare entiteter i tabelltjänsten med följande struktur:  

![Medarbetare entitetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Du måste också att lagra historiska data som rör granskningar och prestanda för varje år medarbetaren har arbetat för din organisation och du behöver för att kunna komma åt informationen per år. Ett alternativ är att skapa en annan tabell som lagrar entiteter med följande struktur:  

![Alternativa medarbetare entitetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Observera att med den här metoden kan du välja att duplicera viss information (till exempel förnamn och efternamn) i den nya entiteten så att du kan hämta dina data med en enskild begäran. Du kan dock ha stark konsekvens eftersom du inte kan använda en EGT för att uppdatera de två entiteterna atomiskt.  

### <a name="solution"></a>Lösning
Store en ny entitet skapas i den ursprungliga tabellen med entiteter med följande struktur:  

![Lösning för medarbetare entitetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Observera hur **RowKey** är nu en sammansatt nyckel som består av anställnings-ID och året för de data som gör att du kan hämta medarbetarens prestanda och granska data med en enskild begäran för en enda entitet.  

I följande exempel beskrivs hur du kan hämta alla granska data för en viss medarbetare (till exempel medarbetare 000123 på försäljningsavdelningen):  

$filter = (PartitionKey eq ”försäljning”) och (RowKey ge ”empid_000123”) och (RowKey lt 'empid_000124') & $select = RowKey, Arbetsledarens, Peer omdöme, kommentarer  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du bör använda en lämplig avgränsningstecken som gör det enkelt att parsa den **RowKey** värde: till exempel **000123_2012**.  
* Den här entiteten lagras också i samma partition som andra entiteter som innehåller relaterade data för samma medarbetare, vilket innebär att du kan använda EGTs för att upprätthålla stark konsekvens.
* Du bör överväga hur ofta du frågar data för att fastställa om det här mönstret är lämpligt.  Till exempel om du kommer åt granska data används sällan och huvudsakliga medarbetardata ofta bör du behålla dem som separata entiteter.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra en eller flera relaterade entiteter som du frågar ofta.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Entitetsgrupptransaktioner  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  
* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Mönster för log tail
Hämta den *n* entiteter som nyligen lagt till en partition med hjälp av en **RowKey** värde som sorteras listan i omvänd datum- och tidsordning.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt krav är att kunna hämta de nyligen skapade entiteterna, till exempel de senaste tio utgifter ansökningar som görs av en medarbetare. Tabellen frågar stöd för en **$top** fråga åtgärden att returnera först *n* entiteter från en uppsättning: det finns inga motsvarande frågeåtgärden att returnera de sista n entiteterna i en uppsättning.  

### <a name="solution"></a>Lösning
Store entiteter med hjälp av en **RowKey** att naturligt sorterar i tidsvärdet i omvänd ordning med hjälp av så den senaste posten är alltid den första i tabellen.  

Du kan till exempel använda en omvänd skalstreck-värde som härletts från aktuellt datum och tid för att kunna hämta de tio senaste utgiftsanspråk som skickats av en medarbetare. I följande C#-kodexempel visas ett sätt att skapa ett lämpligt ”inverterad ticken” värde för en **RowKey** som sorterar från den senaste till äldsta:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Du kan få tillbaka för datum-tid-värden med följande kod:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabell-fråga som ser ut så här:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du måste fylla omvänd skalstreck värdet med inledande nollor så strängvärdet sorterar som förväntat.  
* Du måste känna till det för skalbarhetsmål på nivån för en partition. Tänk på att inte skapa hotspot-partitioner.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver att få åtkomst till entiteter i omvänd datum/tid-ordning eller när du behöver åtkomst till de senast tillagda entiteterna.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Lägg till åtkomstgruppen / lägga till ett mönster](#prepend-append-anti-pattern)  
* [Hämtar entiteter](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Mönster för hög volym delete
Aktivera borttagning av ett stort antal entiteter genom att lagra alla entiteter för samtidiga borttagning i sina egna separata tabell. Du kan ta bort entiteter genom att ta bort tabellen.  

### <a name="context-and-problem"></a>Kontext och problem
Många program ta bort gamla data som behöver inte längre ska vara tillgängliga för ett klientprogram eller som programmet har arkiverats till ett annat lagringsmedium. Du vanligtvis identifiera sådana data genom att ett datum: exempel: du har ett krav att ta bort poster för alla inloggningsbegäranden som är mer än 60 dagar.  

Ett möjligt design är att använda datum och tid för inloggningsförfrågan i den **RowKey**:  

![Datum och tid för inloggningsförsök](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Den här metoden undviker partition hotspots eftersom programmet kan infoga och ta bort inloggningen entiteter för varje användare i en separat partition. Den här metoden kan dock vara kostsamt och tidskrävande om du har ett stort antal entiteter eftersom först måste du utföra en tabellgenomsökning för att identifiera alla enheter ska ta bort och sedan måste du ta bort varje gamla entitet. Observera att du kan minska antalet sändningar till servern som krävs för att ta bort de gamla enheterna med batchbearbetning flera delete-begäranden till EGTs.  

### <a name="solution"></a>Lösning
Använd en separat tabell för varje dag på inloggningsförsök. Du kan använda entiteten designen ovan för att undvika anslutningar när du infogar entiteter och tar bort gamla enheter är nu helt enkelt en fråga av att ta bort en tabell varje dag (en enda lagringsåtgärd) i stället för att söka efter och tar bort hundratals och tusentals person logga in entiteter varje dag.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Din design som har stöd för andra sätt som programmet ska använda data, till exempel söka efter specifika entiteter, länkar till andra data eller generering samlar in information?  
* Din design undviker aktiva punkter när du infogar nya entiteter?  
* Förvänta dig en fördröjning om du vill återanvända samma namn när du tar bort den. Är det bättre att alltid använda unikt tabellnamn.  
* Förvänta dig vissa begränsningar när du först använda en ny tabell medan tabelltjänsten lär sig mönster i databasåtkomst och distribuerar partitionerna mellan noder. Du bör överväga hur ofta du behöver skapa nya tabeller.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du har ett stort antal entiteter som du måste ta bort samtidigt.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Entitetsgrupptransaktioner
* [Ändra entiteter](#modifying-entities)  

## <a name="data-series-pattern"></a>Serien datamönster
Store fullständig dataserier i en enda entitet att minimera antalet begäranden som du gör.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt scenario är för ett program för att lagra en serie med data som vanligtvis krävs för att hämta allt samtidigt. Ditt program kan till exempel registrera hur många IM meddelanden varje medarbetare skickar varje timme och sedan använda informationen för att rita ut hur många meddelanden varje användare som skickas över föregående 24 timmar. En design kan vara att lagra 24 entiteter för varje medarbetare:  

![Store 24 entiteter för varje medarbetare](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Med den här designen kan du enkelt hitta och uppdatera enheten att uppdatera för varje medarbetare när programmet måste uppdatera värdet för antal meddelande. Men om du vill hämta information för att rita ett diagram för aktiviteten i föregående 24 timmar, måste du hämta 24 entiteter.  

### <a name="solution"></a>Lösning
Använd följande design med en separat egenskap för att lagra meddelandeantalet för varje timme:  

![Meddelandet stats entitet](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Du kan använda en sammanfogning med den här designen för att uppdatera meddelandeantalet för en anställd för en viss timme. Du kan nu hämta all information du behöver att rita diagram med hjälp av en begäran för en enda entitet.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om din fullständiga dataserien trädnamnet inte passar i en enda entitet (en entitet kan ha upp till 252 egenskaper), kan du använda ett annat datalager, till exempel en blob.  
* Om du har flera klienter som uppdaterar en entitet samtidigt, kommer du behöva använda den **ETag** att implementera Optimistisk samtidighet. Om du har många klienter kan få du hög konkurrens.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill uppdatera och hämta en serie som är associerade med en enskild entitet.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för stora entiteter](#large-entities-pattern)  
* [Sammanfoga eller ersätta](#merge-or-replace)  
* [Konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) (om du lagrar dataserien i en blob)  

## <a name="wide-entities-pattern"></a>Mönster för många entiteter
Använda flera fysiska enheter för att lagra logiska entiteter med mer än 252 egenskaper.  

### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan ha högst 252 egenskaper (förutom de obligatoriska systemegenskaperna) och kan inte lagra mer än 1 MB data totalt. I en relationsdatabas skulle du vanligtvis få avrunda några gränser för storleken på en rad genom att lägga till en ny tabell och tillämpa en 1-till-1-relation mellan dem.  

### <a name="solution"></a>Lösning
Du kan lagra flera entiteter för att representera ett objekt för stora företag med mer än 252 egenskaper med Table service. Du kan till exempel använda följande designen som använder två entiteter med olika scheman om du vill spara en uppräkning av antalet IM-meddelanden som skickas av varje medarbetare för de senaste 365 dagarna:  

![Flera entiteter](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Du kan använda en EGT om du behöver göra en ändring som kräver uppdaterar båda entiteter för att hålla dem synkroniserade med varandra. Annars kan använda du en enda merge-operation för att uppdatera meddelandeantalet för en viss dag. För att hämta alla data för en enskild anställd måste du hämta båda enheter som du kan göra med två effektiv förfrågningar som använder både en **PartitionKey** och en **RowKey** värde.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Hämta en fullständig logisk entitet innebär att minst två lagringstransaktioner: en för att hämta varje fysisk entitet.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek eller antalet egenskaper överskrider gränserna för en enskild entitet i Table service.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Entitetsgrupptransaktioner
* [Sammanfoga eller ersätta](#merge-or-replace)

## <a name="large-entities-pattern"></a>Mönster för stora entiteter
Använda blob storage för att lagra stora egenskapsvärden.  

### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte lagra mer än 1 MB data totalt. Om en eller flera av dina egenskaper lagra värden som orsakar den totala storleken på din entitet överskrider värdet, kan inte du lagra hela entiteten i Table service.  

### <a name="solution"></a>Lösning
Om din entitet överskrider 1 MB i storlek eftersom en eller flera egenskaper innehåller en stor mängd data kan du lagra data i Blob-tjänsten och sedan lagra den blob-adressen i en egenskap i entiteten. Du kan till exempel lagra foto av en medarbetare i blob storage och lagra en länk till bilden i den **foto** egenskap för dina medarbetare entitet:  

![Foto-egenskapen](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* För att upprätthålla konsekvens mellan i Table service och data i Blob-tjänsten använder den [konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) att underhålla dina entiteter.
* Hämta en fullständig entitet innebär att minst två lagringstransaktioner: en för att hämta entiteten och en för att hämta blob-data.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill lagra entiteter vars storlek överskrider gränserna för en enskild entitet i Table service.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* [Mönster för många entiteter](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Lägg till åtkomstgruppen/lägga till ett mönster
Öka skalbarheten när du har ett stort antal infogningar genom att sprida tillägg över flera partitioner.  

### <a name="context-and-problem"></a>Kontext och problem
Prepending eller lägger till entiteter i dina entiteter för lagrade vanligtvis resulterar i programmet att lägga till nya entiteter i den första eller sista partitionen i en sekvens av partitioner. I det här fallet som alla infogningar vid en given tidpunkt äger rum i samma partition, skapar en hotspot som förhindrar tabelltjänsten från belastningsutjämning infogningar över flera noder och vilket kan orsaka att ditt program att träffa skalbarhetsmål för partition. Till exempel om du har ett program som loggar nätverks- och komma åt genom att anställda kan sedan en entitetsstruktur enligt nedan kan resultera i den aktuella timman partition blir en överbelastad punkt om mängden transaktioner når skalbarhetsmålen för en enskild partition:  

![Entitetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Lösning
Följande alternativ entitetsstruktur undviker en hotspot på en viss partition som program loggar händelser:  

![Alternativa entitetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Meddelande med det här exemplet hur både den **PartitionKey** och **RowKey** är sammansatta nycklar. Den **PartitionKey** använder både avdelning och employee ID för att distribuera loggning över flera partitioner.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder den alternativa nyckelstruktur som undviker skapa heta partitioner på infogningar effektivt frågorna klientprogrammet gör?  
* Betyder din förväntade mängden transaktioner att du förmodligen att nå skalbarhetsmål för en enskild partition och att begränsas av storage-tjänsten?  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Undvik prepend/lägga till ett mönstret när volymen av transaktioner är sannolikt att begränsning av storage-tjänsten när du använder en frekvent partition.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Mönster för log tail](#log-tail-pattern)  
* [Ändra entiteter](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Logga ett datamönster
Du bör normalt använda Blob-tjänsten i stället för Table service för att lagra loggdata.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt användningsfall för loggdata är att hämta en uppsättning loggposter för ett specifikt datum-/ tidsintervall: till exempel du vill hitta alla fel och kritiska meddelanden som ditt program loggas mellan 15:04 och 15:06 på ett visst datum. Du inte vill använda datum och tid loggmeddelandet för att fastställa den partition som du sparar log entiteter för att: som resulterar i en frekvent partition eftersom vid en given tidpunkt, så kommer alla log-entiteter dela samma **PartitionKey** värde (se den avsnittet [Prepend/lägga till ett mönster](#prepend-append-anti-pattern)). Följande entitetsschemat för ett loggmeddelande resulterar i en frekvent partition eftersom programmet skriver alla loggmeddelanden till partition för den aktuella datum och tid:  

![Log-meddelandeentiteten](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

I det här exemplet på **RowKey** innehåller datum och tid för loggmeddelande så att loggmeddelanden lagras i datum/tid-ordning och innehåller ett meddelande-ID om flera loggmeddelanden delar samma datum och tid.  

En annan metod är att använda en **PartitionKey** som säkerställer att programmet skriver meddelanden över flera olika partitioner. Om källan för loggmeddelandet är ett sätt att distribuera meddelanden över många partitioner, kan du till exempel använda följande entitetsschemat:  

![Alternativa log-meddelandeentiteten](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Problem med det här schemat är dock att om du vill hämta alla loggmeddelanden för en viss tidsrymd måste du söka varje partition i tabellen.

### <a name="solution"></a>Lösning
Föregående avsnitt markerad problemet med försöker använda tabelltjänsten för att lagra loggposter och föreslagna två, otillfredsställande, design. En lösning som ledde till en frekvent partition med risk för prestandaproblem skriva loggmeddelanden; den andra lösningen resulterade i sämre prestanda på grund av kravet på att söka igenom alla partitioner i tabellen för att hämta loggmeddelanden för ett specifikt tidsintervall. BLOB-lagring erbjuder en bättre lösning för den här typen av scenario och det är hur Azure Storage Analytics butiker loggdata som samlas in.  

Det här avsnittet beskriver hur Storage Analytics lagrar loggdata i blob storage som en illustration av den här metoden för att lagra data som du vanligtvis fråga genom att intervallet.  

Lagringsanalys lagrar loggmeddelanden i en avgränsad format i flera blobar. Avgränsad format gör det enkelt för ett klientprogram att parsa data i loggmeddelandet.  

Storage Analytics använder en namngivningskonvention för blobbar som gör det möjligt för dig att hitta blob (eller blobbar) som innehåller loggmeddelanden som du söker. Till exempel innehåller en blob med namnet ”queue/2014/07/31/1800/000001.log” loggmeddelanden som relaterar till kötjänst för med början vid 18:00 31 juli 2014. ”000001” anger att detta är den första loggfilen för den här perioden. Lagringsanalys registrerar också tidsstämplarna för de första och sista loggmeddelanden som lagras i filen som en del av den blob-metadata. API för blob storage kan du hitta blobbar i en behållare baserat på ett namnprefix: Om du vill hitta alla blobbar som innehåller kön loggdata för med början vid 18:00, kan du använda prefixet ”kö/2014/07/31/1800”.  

Storage Analytics buffertar logga meddelanden internt och sedan regelbundet uppdaterar lämplig blob eller skapar en ny med den senaste batchen av loggposter. Detta minskar antalet skrivningar som måste utföras mot blob service.  

Om du implementerar en liknande lösning i ditt eget program, måste du tänka på hur du hanterar en kompromiss mellan tillförlitlighet (skriver varje loggpost till blob storage som händer) och kostnaden och skalbarhet (buffring uppdateringar i ditt program och skriva dem till blob storage i batchar).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du lagrar loggdata:  

* Om du skapar en tabelldesign som undviker eventuella heta partitioner kan hända att du inte kommer åt loggdata effektivt.  
* Om du vill bearbeta loggdata, en klient behöver ofta läsa in många poster.  
* Även om loggdata är ofta strukturerade, kan blob-lagring vara en bättre lösning.  

## <a name="implementation-considerations"></a>Att tänka på vid implementering
Det här avsnittet beskrivs några saker att ha i åtanke när du implementerar de mönster som beskrivs i föregående avsnitt. De flesta av det här avsnittet använder exempel som skrivits i C# och som använder Storage-klientbibliotek (version 4.3.0 då skrivs).  

## <a name="retrieving-entities"></a>Hämtar entiteter
Enligt beskrivningen i avsnittet Design för att fråga, är mest effektiva frågan en punkt-fråga. I vissa situationer kan du dock behöva hämta flera entiteter. Det här avsnittet beskrivs några vanliga metoder för att hämta entiteter med hjälp av Storage-klientbiblioteket.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Kör en punkt-fråga med hjälp av Storage-klientbibliotek
Det enklaste sättet att köra en punkt-fråga är att använda den **hämta** tabellen åtgärden som visas i C# kodfragment som hämtar en entitet med en **PartitionKey** värde ”försäljning” och en  **RowKey** värde ”212”:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observera hur entiteten förväntar att det här exemplet hämtas för att vara av typen **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Hämtar flera entiteter med hjälp av LINQ
Du kan hämta flera entiteter med hjälp av LINQ med Storage-klientbiblioteket och ange en fråga med en **där** satsen. Om du vill undvika en tabellgenomsökning, bör du alltid skriva den **PartitionKey** värdet i where-satsen, och om möjligt den **RowKey** värde att undvika tabell och partition genomsökningar. Table service har stöd för en begränsad uppsättning jämförelseoperatorer (större än, större än eller lika med, mindre, mindre än eller lika med, lika med och inte lika med) att använda i where-satsen. I följande C#-kodavsnitt som söker efter alla anställda vars efternamn börjar med ”B” (förutsatt att den **RowKey** lagrar efternamn) på försäljningsavdelningen (förutsatt att den **PartitionKey** lagrar den avdelningsnamn):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observera hur frågan anger både en **RowKey** och en **PartitionKey** säkerställa bättre prestanda.  

Följande kodexempel visar motsvarande funktioner med fluent-API (Mer information om fluent-API: er i allmänhet finns [bästa praxis för att utforma en Fluent-API](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Exemplet omsluter flera **CombineFilters** metoder för att inkludera tre filtervillkoren.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Hämtning av stora mängder entiteter från en fråga
En optimal frågan returnerar en enskild entitet baserat på en **PartitionKey** värde och ett **RowKey** värde. Du kan dock ha ett krav att returnera många entiteter från samma partition eller även många partitioner i vissa situationer.  

I sådana situationer bör du alltid helt testa programmets prestanda.  

En fråga till tabelltjänsten kan returnera högst 1 000 entiteter i taget och kan köras i högst fem sekunder. Om resultatet innehåller fler än 1 000 entiteter om frågan inte slutfördes inom fem sekunder, eller om frågan överskrider gränsen för partition, returnerar Table service ett fortsättningstoken för att aktivera klientprogram för att begära en uppsättning entiteter. Mer information om hur fortsättning tokens arbete finns i [tidsgräns för fråga och sidbrytning](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Om du använder Storage-klientbiblioteket, hanterar den automatiskt fortsättning token åt dig när den returnerar entiteter från tabelltjänsten. Följande C# kodexemplet använder Storage-klientbiblioteket automatiskt hanterar fortsättning token om table service returnerar dem i ett svar:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Följande C#-kod hanterar uttryckligen fortsättning token:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Du kan styra när programmet hämtar data nästa segment med hjälp av fortsättning token uttryckligen. Till exempel om klientprogrammet gör att användarna kan bläddra igenom de entiteter som lagras i en tabell, en användare behöver inte igenom alla entiteter som hämtas av frågan, så att ditt program använder bara ett fortsättningstoken för att hämta nästa segmentera när användaren hade slutförts bläddring genom alla entiteter i det aktuella segmentet. Den här metoden har flera fördelar:  

* Du kan begränsa mängden data som ska hämtas från tabelltjänsten och att du flyttar över nätverket.  
* Det gör att du kan utföra asynkrona i/o i .NET.  
* Det gör att du kan serialisera fortsättningstoken till beständig lagring, så att du kan fortsätta i händelse av ett program systemkrasch.  

> [!NOTE]
> Ett fortsättningstoken returnerar vanligtvis ett segment som innehåller 1 000 entiteter, men det kan finnas färre. Detta gäller även om du begränsar antalet poster som en fråga som returnerar med hjälp av **ta** att returnera de första n entiteter som matchar sökvillkoren sökning: table service kan returnera ett segment som innehåller färre än n entiteter tillsammans med en fortsättningstoken så att du kan hämta de återstående entiteterna.  
> 
> 

Följande C#-kod visar hur du ändrar antalet enheter som returneras i ett segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projektion för serversidan
En enda entitet kan ha upp till 255 egenskaper och upp till 1 MB i storlek. När du frågar tabellen och hämta entiteter kan du kanske inte behöver alla egenskaper och kan undvika att överföra data onödan (om du vill minska svarstid och kostnader). Du kan använda serversidan projektion Överför bara de egenskaper som du behöver. I följande exempel är hämtar bara **e-post** egenskapen (tillsammans med **PartitionKey**, **RowKey**, **tidsstämpel**, och **ETag**) från de entiteter som valts av frågan.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Observera hur **RowKey** värdet är tillgängliga även om den inte ingick i listan över egenskaper som hämtas.  

## <a name="modifying-entities"></a>Ändra entiteter
Storage-klientbiblioteket kan du ändra dina entiteter som lagras i tabelltjänsten genom att lägga till, ta bort och uppdatera entiteter. Du kan använda EGTs batch flera insert-, update- och delete-åtgärder tillsammans för att minska antalet sändningar som krävs och förbättra prestandan för din lösning.  

Observera att undantag som utlöses när en EGT körs vanligtvis i Storage-klientbiblioteket inkluderar index för den entitet som orsakade batch misslyckas. Detta är användbart när du felsöker kod som använder EGTs.  

Du bör också överväga hur din design påverkar hanteringen av samtidighet och uppdateringsåtgärderna i klientprogrammet.  

### <a name="managing-concurrency"></a>Hantera samtidighet
Som standard tabelltjänsten implementerar Optimistisk samtidighet kontrollerar på nivån för enskilda entiteter för **infoga**, **sammanfoga**, och **ta bort** åtgärder, även om den är möjligt för en klient att tvinga tabelltjänsten att kringgå de här kontrollerna. Läs mer om hur tabelltjänsten hanterar samtidighet [hantera samtidighet i Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Sammanfoga eller ersätta
Den **Ersätt** -metoden för den **TableOperation** klass ersätts alltid fullständig entitet i Table service. Om du inte använder en egenskap i begäran när egenskapen finns i entiteten lagrade begäran att tar bort egenskapen från den lagrade entiteten. Om du inte vill ta bort en egenskap uttryckligen från en lagrad entitet, måste du inkludera varje egenskap i begäran.  

Du kan använda den **sammanfoga** -metoden för den **TableOperation** klassen för att minska mängden data som du skickar till Tabelltjänsten igen när du vill uppdatera en entitet. Den **sammanfoga** metoden ersätter alla egenskaper i entiteten lagrade med egenskapsvärden från entiteten i begäran, men förblir intakta alla egenskaper i lagrade entiteten som inte ingår i begäran. Detta är användbart om du har stora entiteter och behöver bara uppdatera ett litet antal egenskaper i en begäran.  

> [!NOTE]
> Den **Ersätt** och **sammanfoga** metoder misslyckas om entiteten inte finns. Alternativt kan du använda den **InsertOrReplace** och **InsertOrMerge** metoder som skapar en ny entitet om det inte finns.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Arbeta med heterogena entitetstyper
Table service är en *utan schema* tabellarkiv innebär det att en enda tabell kan lagra entiteter av flera typer som ger stor flexibilitet i din design. I följande exempel visas en tabell som lagrar både anställda och avdelning entiteter:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Tidsstämpel</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Avdelningsnamn</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Observera att varje entitet måste fortfarande ha **PartitionKey**, **RowKey**, och **tidsstämpel** värden, men kan ha en uppsättning egenskaper. Det finns inget att ange vilken typ av en entitet om du inte väljer att lagra informationen någonstans. Det finns två alternativ för att identifiera entitetstypen:  

* Lägg till åtkomstgruppen entitetstypen till den **RowKey** (eller eventuellt den **PartitionKey**). Till exempel **EMPLOYEE_000123** eller **DEPARTMENT_SALES** som **RowKey** värden.  
* Använda en separat egenskap för att registrera enhetstypen som visas i tabellen nedan.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Tidsstämpel</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>Avdelningsnamn</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Avdelning</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Det första alternativet, prepending entiteten skriver till den **RowKey**, är användbart om det finns en risk att två entiteter med olika typer kan ha samma nyckelvärde. Den också grupperar entiteter av samma typ tillsammans i partitionen.  

De metoder som beskrivs i det här avsnittet är särskilt relevanta till diskussionen [arvsrelationer](table-storage-design-modeling.md#inheritance-relationships) tidigare i den här guiden i artikeln [modellering relationer](table-storage-design-modeling.md).  

> [!NOTE]
> Du bör inkludera ett versionsnummer i entiteten TYPVÄRDE och låt klientprogram att utvecklas POCO-objekt och arbeta med olika versioner.  
> 
> 

Resten av det här avsnittet beskrivs några av de funktioner i Storage-klientbiblioteket som underlättar arbetet med flera typer av enheter i samma tabell.  

### <a name="retrieving-heterogeneous-entity-types"></a>Hämtning av heterogena entitetstyper
Om du använder Storage-klientbiblioteket, har du tre alternativ för att arbeta med flera typer av enheter.  

Om du vet vilken typ av enhet som lagras med ett visst **RowKey** och **PartitionKey** värden, kan du ange entitetstypen när du hämtar entitet som du ser i föregående två exempel som Hämta entiteter av typen **EmployeeEntity**: [Kör en punkt-fråga med hjälp av Storage-klientbiblioteket](#executing-a-point-query-using-the-storage-client-library) och [hämtar flera entiteter med hjälp av LINQ](#retrieving-multiple-entities-using-linq).  

Det andra alternativet är att använda den **DynamicTableEntity** typ (en egenskapsuppsättning) i stället för en konkret POCO entitetstypen (det här alternativet kan också förbättra prestanda eftersom det finns inget behov att serialisera och deserialisera entiteten till .NET-typer). Följande C#-kod potentiellt hämtar flera entiteter av olika typer från tabellen, men returnerar alla entiteter som **DynamicTableEntity** instanser. Därefter använder den **EntityType** som bestämmer vilken typ av varje entitet:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Observera att om du vill hämta andra egenskaper som du måste använda den **TryGetValue** metoden på den **egenskaper** egenskapen för den **DynamicTableEntity** klass.  

Ett tredje alternativ är att kombinera med hjälp av den **DynamicTableEntity** typ och en **EntityResolver** instans. Detta gör det möjligt för dig att lösa flera POCO-typer i samma fråga. I det här exemplet på **EntityResolver** ombud använder den **EntityType** egenskapen att skilja mellan de två typerna av entiteten som returneras av frågan. Den **lösa** metoden används den **matchare** ombud för att lösa **DynamicTableEntity** -instanser till **TableEntity** instanser.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Ändra heterogena entitetstyper
Du behöver inte vet vilken typ av en entitet att ta bort den och du vet alltid vilken datatyp för en entitet när du infogar den. Du kan dock använda **DynamicTableEntity** Skriv för att uppdatera en entitet utan att känna till dess typ och utan att använda en POCO-entitetsklass. Följande kodexempel hämtas en enda entitet och kontrollerar de **EmployeeCount** egenskapen finns innan du uppdaterar den.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

## <a name="controlling-access-with-shared-access-signatures"></a>Kontrollera åtkomst med signaturer för delad åtkomst
Du kan använda token för signatur för delad åtkomst (SAS) för att aktivera klientprogram för att ändra (och fråga) tabellenheter utan att behöva ta med din lagringskontonyckel i din kod. Det finns vanligtvis tre huvudsakliga fördelar med SAS i ditt program:  

* Du behöver inte distribuera din lagringskontonyckel till ett osäkert plattform (till exempel en mobil enhet) för att tillåta att enheten för att komma åt och ändra entiteter i Table service.  
* Du omfördela några av det arbete som webb-och arbetsroller utföra i hanteringen av dina entiteter till klientenheter, till exempel slutanvändarnas datorer och mobila enheter.  
* Du kan tilldela en begränsad och tid begränsad uppsättning behörigheter till en klient (till exempel att tillåta skrivskyddad åtkomst till specifika resurser).  

Läs mer om hur du använder SAS-token med Table service, [med signaturer för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Du måste dock fortfarande generera SAS-token som ger ett klientprogram till entiteter i tabelltjänsten: du bör göra detta i en miljö som har säker åtkomst till dina lagringskontonycklar. Normalt använder du en web- eller worker-roll för att generera SAS-token och skicka dem till klientprogram som behöver åtkomst till dina entiteter. Eftersom det finns fortfarande en belastning i genererar och leverera SAS-token till klienter, bör du hur du bäst för att minska den här kostnader, särskilt i scenarion med stora volymer.  

Det är möjligt att skapa en SAS-token som beviljar åtkomst till en delmängd entiteter i en tabell. Som standard kan du skapa en SAS-token för en hel tabell, men det är också möjligt att ange att SAS-token ger åtkomst till antingen en mängd **PartitionKey** värden, eller en mängd **PartitionKey** och **RowKey** värden. Du kan välja att generera SAS-token för enskilda användare i systemet så att varje användares SAS-token endast ger dem åtkomst till sina egna entiteter i table service.  

## <a name="asynchronous-and-parallel-operations"></a>Asynkron och parallella åtgärder
Förutsatt att dina förfrågningar sprids över flera partitioner, kan du förbättra svarstiden för dataflöde och klienten med hjälp av asynkrona eller parallella frågor.
Du kan till exempel ha två eller flera arbetsrollinstanser åtkomst till dina tabeller parallellt. Du kan ha enskilda worker-roller som är ansvarig för specifika uppsättningar med partitioner eller helt enkelt har flera arbetarrollinstanser, varje kan komma åt alla partitioner i en tabell.  

Du kan förbättra dataflödet genom att köra lagringsåtgärder asynkront i en client-instans. Storage-klientbiblioteket gör det enkelt att skriva asynkrona frågor och ändringar. Du kan till exempel börja med synkron metod som hämtar alla entiteter i en partition som visas i följande C#-kod:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Du kan enkelt ändra den här koden så att frågan körs asynkront på följande sätt:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

I den här asynkrona exempelvis ser du följande ändringar från den synkrona versionen:  

* Metodsignaturen innehåller nu den **async** modifieraren och returnerar en **uppgift** instans.  
* I stället för att anropa den **ExecuteSegmented** att hämta resultat, metoden nu anropar den **ExecuteSegmentedAsync** metoden och använder den **await** modifieraren till Hämta resultatet asynkront.  

Klientprogrammet kan anropa den här metoden flera gånger (med olika värden för den **avdelning** parametern), och varje fråga ska köras på en separat tråd.  

Observera att det inte finns någon asynkron version av den **kör** -metod i den **TableQuery** klassen eftersom den **IEnumerable** gränssnittet stöder inte asynkrona uppräkning.  

Du kan också infoga, uppdatera och ta bort entiteter asynkront. I följande C#-exempel visas en enkel, synkron metod för att infoga eller ersätta en anställd entitet:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Du kan enkelt ändra den här koden så att uppdateringen körs asynkront på följande sätt:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

I den här asynkrona exempelvis ser du följande ändringar från den synkrona versionen:  

* Metodsignaturen innehåller nu den **async** modifieraren och returnerar en **uppgift** instans.  
* I stället för att anropa den **kör** att uppdatera entiteten, metoden nu anropar den **ExecuteAsync** metoden och använder den **await** modifieraren att hämta resultat asynkront.  

Klientprogrammet kan anropa flera asynkrona metoder som det här och varje metodanropet ska köras på en separat tråd.  

## <a name="next-steps"></a>Nästa steg

- [Modellering relationer](table-storage-design-modeling.md)
- [Design för frågor](table-storage-design-for-query.md)
- [Kryptering av tabelldata som](table-storage-design-encrypt-data.md)
- [Design för dataändringar](table-storage-design-for-modification.md)
