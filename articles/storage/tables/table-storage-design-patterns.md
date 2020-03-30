---
title: Designmönster för Azure-lagringstabell | Microsoft-dokument
description: Använd mönster för Azure-tabelltjänstlösningar.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 5478163a6103bcc84b4f3608d7513c6e7cb11c01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529347"
---
# <a name="table-design-patterns"></a>Mönster för tabelldesign
I den här artikeln beskrivs några mönster som är lämpliga att använda med tabelltjänstlösningar. Dessutom kommer du att se hur du praktiskt taget kan ta itu med några av de frågor och kompromisser diskuteras i andra Tabell lagring design artiklar. I följande diagram sammanfattas relationerna mellan de olika mönstren:  

![för att slå upp relaterade data](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Mönsterkartan ovan belyser vissa relationer mellan mönster (blå) och anti-mönster (orange) som dokumenteras i den här guiden. Det finns många andra mönster som är värda att överväga. Ett av de viktigaste scenarierna för tabelltjänsten är till exempel att använda det [materialiserade vymönstret](https://msdn.microsoft.com/library/azure/dn589782.aspx) från mönstret För att komma [till budfrågorsagensering (CQRS).](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

## <a name="intra-partition-secondary-index-pattern"></a>Sekundärt indexmönster inom partitionen
Lagra flera kopior av varje entitet med olika **RowKey-värden** (i samma partition) för att möjliggöra snabba och effektiva sökninger och alternativa sorteringsorder med hjälp av olika **RowKey-värden.** Uppdateringar mellan kopior kan hållas konsekventa med egts.  

### <a name="context-and-problem"></a>Kontext och problem
Tabelltjänsten indexerar automatiskt entiteter med hjälp av **värdena PartitionKey** och **RowKey.** Detta gör det möjligt för ett klientprogram att hämta en entitet effektivt med hjälp av dessa värden. Med hjälp av tabellstrukturen nedan kan ett klientprogram till exempel använda en punktfråga för att hämta en enskild medarbetarentitet med hjälp av avdelningsnamnet och medarbetar-ID:t **(PartitionKey-** och **RowKey-värdena).** En klient kan också hämta entiteter sorterade efter medarbetar-ID inom varje avdelning.

![Bild06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Om du också vill kunna hitta en medarbetarentitet baserat på värdet av en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitionsgenomsökning för att hitta en matchning. Detta beror på att tabelltjänsten inte tillhandahåller sekundära index. Dessutom finns det inget alternativ för att begära en lista över medarbetare sorterade i en annan ordning än **RowKey-ordning.**  

### <a name="solution"></a>Lösning
Om du vill gå runt på bristen på sekundära index kan du lagra flera kopior av varje entitet med varje kopia med ett annat **RowKey-värde.** Om du lagrar en entitet med de strukturer som visas nedan kan du effektivt hämta medarbetarentiteter baserat på e-postadress eller medarbetar-ID. Med prefixvärdena för **RowKey,**"empid_" och "email_" kan du fråga efter en enskild medarbetare eller ett antal anställda med hjälp av ett intervall med e-postadresser eller medarbetar-ID:er.  

![Medarbetarenheter](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Följande två filtervillkor (ett som söker upp med medarbetar-ID och ett som söker upp via e-postadress) anger båda punktfrågor:  

* $filter=(PartitionKey eq 'Sales') och (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') och (RowKey eq 'email_jonesj@contoso.com')  

Om du frågar efter ett intervall med medarbetarentiteter kan du ange ett intervall sorterat i medarbetar-ID-ordning eller ett intervall som sorteras i e-postadressordning genom att fråga efter entiteter med lämpligt prefix i **RowKey**.  

* För att hitta alla anställda på försäljningsavdelningen med ett medarbetar-ID i intervallet 000100 till 000199 använder du: $filter=(PartitionKey eq 'Sales') och (RowKey ge 'empid_000100') och (RowKey le 'empid_000199')  
* Så här hittar du alla anställda på försäljningsavdelningen med en e-postadress som börjar med bokstaven "a" använder: $filter=(PartitionKey eq 'Sales') och (RowKey ge 'email_a') och (RowKey lt 'email_b')  
  
  Filtersyntaxen som används i exemplen ovan kommer från REST API:et för tabelltjänsten, mer information finns i [Frågeentiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Tabell lagring är relativt billigt att använda så kostnaden omkostnader för att lagra dubbla data bör inte vara ett stort problem. Du bör dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagringskrav och bara lägga till dubblettentiteter för att stödja de frågor som klientprogrammet ska köra.  
* Eftersom de sekundära indexentiteterna lagras i samma partition som de ursprungliga entiteterna bör du se till att du inte överskrider skalbarhetsmålen för en enskild partition.  
* Du kan hålla dina dubbla entiteter konsekventa med varandra genom att använda EGTs för att uppdatera de två kopiorna av entiteten atomärt. Detta innebär att du bör lagra alla kopior av en entitet i samma partition. Mer information finns i avsnittet [Använda entitetsgruppstransaktioner](table-storage-design.md#entity-group-transactions).  
* Värdet som används för **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatta nyckelvärden.  
* Utfyllnad numeriska värden i **RowKey** (till exempel medarbetar-ID 000223), möjliggör korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis duplicera alla egenskaper för din entitet. Om till exempel de frågor som slår upp entiteterna med hjälp av e-postadressen i **RowKey** aldrig behöver medarbetarens ålder, kan dessa entiteter ha följande struktur:

   ![Struktur för medarbetarens entitet](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Det är vanligtvis bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga än att använda en fråga för att hitta en entitet och en annan för att slå upp nödvändiga data.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när klientprogrammet behöver hämta entiteter med hjälp av en mängd olika nycklar, när klienten behöver hämta entiteter i olika sorteringsorder och där du kan identifiera varje entitet med hjälp av en mängd unika värden. Du bör dock vara säker på att du inte överskrider gränserna för partitionskalbarhet när du utför entitetssökningar med hjälp av de olika **RowKey-värdena.**  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sekundärt indexmönster mellan partitioner](#inter-partition-secondary-index-pattern)
* [Sammansattnyckelmönster](#compound-key-pattern)
* Transaktioner i entitetsgrupp
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Sekundärt indexmönster mellan partitioner
Lagra flera kopior av varje entitet med olika **RowKey-värden** i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökninger och alternativa sorteringsorder med hjälp av olika **RowKey-värden.**  

### <a name="context-and-problem"></a>Kontext och problem
Tabelltjänsten indexerar automatiskt entiteter med hjälp av **värdena PartitionKey** och **RowKey.** Detta gör det möjligt för ett klientprogram att hämta en entitet effektivt med hjälp av dessa värden. Med hjälp av tabellstrukturen nedan kan ett klientprogram till exempel använda en punktfråga för att hämta en enskild medarbetarentitet med hjälp av avdelningsnamnet och medarbetar-ID:t **(PartitionKey-** och **RowKey-värdena).** En klient kan också hämta entiteter sorterade efter medarbetar-ID inom varje avdelning.  

![Anställnings-ID](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Om du också vill kunna hitta en medarbetarentitet baserat på värdet av en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitionsgenomsökning för att hitta en matchning. Detta beror på att tabelltjänsten inte tillhandahåller sekundära index. Dessutom finns det inget alternativ för att begära en lista över medarbetare sorterade i en annan ordning än **RowKey-ordning.**  

Du förutser en stor transaktionsvolym mot dessa entiteter och vill minimera risken för att tabelltjänsten stryper klienten.  

### <a name="solution"></a>Lösning
Om du vill gå runt på bristen på sekundära index kan du lagra flera kopior av varje entitet med varje kopia med olika **PartitionKey-** och **RowKey-värden.** Om du lagrar en entitet med de strukturer som visas nedan kan du effektivt hämta medarbetarentiteter baserat på e-postadress eller medarbetar-ID. Med prefixvärdena för **PartitionKey**, "empid_" och "email_" kan du identifiera vilket index du vill använda för en fråga.  

![Primärt index och sekundärt index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Följande två filtervillkor (ett som söker upp med medarbetar-ID och ett som söker upp via e-postadress) anger båda punktfrågor:  

* $filter=(PartitionKey eq 'empid_Sales') och (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') och (RowKey eqjonesj@contoso.com' ')  

Om du frågar efter ett intervall med medarbetarentiteter kan du ange ett intervall sorterat i medarbetar-ID-ordning eller ett intervall som sorteras i e-postadressordning genom att fråga efter entiteter med lämpligt prefix i **RowKey**.  

* Så här hittar du alla anställda på försäljningsavdelningen med ett medarbetar-ID i intervallet **000100** till **000199** sorterade i användning av medarbetar-ID:$filter=(PartitionKey eq 'empid_Sales') och (RowKey ge '000100' ) och (RowKey le '000199')  
* Så här hittar du alla anställda på försäljningsavdelningen med en e-postadress som börjar med "a" sorterad i e-postadressordningsanvändning: $filter=(PartitionKey eq 'email_Sales') och (RowKey ge 'a') och (RowKey lt 'b')  

Filtersyntaxen som används i exemplen ovan kommer från REST API:et för tabelltjänsten, mer information finns i [Frågeentiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du kan hålla dina dubbla entiteter så småningom konsekventa med varandra genom att använda [mönstret Så småningom konsekventa transaktioner](#eventually-consistent-transactions-pattern) för att underhålla de primära och sekundära indexentiteterna.  
* Tabell lagring är relativt billigt att använda så kostnaden omkostnader för att lagra dubbla data bör inte vara ett stort problem. Du bör dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagringskrav och bara lägga till dubblettentiteter för att stödja de frågor som klientprogrammet ska köra.  
* Värdet som används för **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatta nyckelvärden.  
* Utfyllnad numeriska värden i **RowKey** (till exempel medarbetar-ID 000223), möjliggör korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis duplicera alla egenskaper för din entitet. Om till exempel de frågor som slår upp entiteterna med hjälp av e-postadressen i **RowKey** aldrig behöver medarbetarens ålder, kan dessa entiteter ha följande struktur:
  
   ![Medarbetarens entitet (sekundärt index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Det är vanligtvis bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga än att använda en fråga för att hitta en entitet med hjälp av det sekundära indexet och en annan för att slå upp de data som krävs i det primära indexet.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när klientprogrammet behöver hämta entiteter med hjälp av en mängd olika nycklar, när klienten behöver hämta entiteter i olika sorteringsorder och där du kan identifiera varje entitet med hjälp av en mängd unika värden. Använd det här mönstret när du vill undvika att överskrida gränserna för partitionskalbarhet när du utför entitetssökningar med hjälp av de olika **RowKey-värdena.**  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern)  
* [Sekundärt indexmönster inom partitionen](#intra-partition-secondary-index-pattern)  
* [Sammansattnyckelmönster](#compound-key-pattern)  
* Transaktioner i entitetsgrupp  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Så småningom konsekvent transaktionsmönster
Aktivera så småningom konsekvent beteende över partitionsgränser eller lagringssystemgränser med hjälp av Azure-köer.  

### <a name="context-and-problem"></a>Kontext och problem
EGTs aktivera atomära transaktioner över flera entiteter som delar samma partitionsnyckel. Av prestanda- och skalbarhetsskäl kan du välja att lagra entiteter som har konsekvenskrav i separata partitioner eller i ett separat lagringssystem: i ett sådant scenario kan du inte använda egts för att upprätthålla konsekvens. Du kan till exempel ha ett krav på att upprätthålla eventuell överensstämmelse mellan:  

* Entiteter som lagras i två olika partitioner i samma tabell, i olika tabeller eller i olika lagringskonton.  
* En entitet som lagras i tabelltjänsten och en blob som lagras i Blob-tjänsten.  
* En entitet som lagras i tabelltjänsten och en fil i ett filsystem.  
* En entitet som lagras i tabelltjänsten har ännu indexerats med hjälp av Azure Cognitive Search-tjänsten.  

### <a name="solution"></a>Lösning
Genom att använda Azure-köer kan du implementera en lösning som ger slutlig konsekvens mellan två eller flera partitioner eller lagringssystem.
För att illustrera den här metoden antar du att du har ett krav för att kunna arkivera gamla medarbetare. Gamla medarbetarentiteter efterfrågas sällan och bör uteslutas från alla aktiviteter som hanterar aktuella medarbetare. Om du vill implementera det här kravet lagrar du aktiva medarbetare i tabellen **Aktuell och** gamla medarbetare i arkivtabellen. **Archive** Arkivering av en medarbetare kräver att du tar bort entiteten från tabellen **Aktuell** och lägger till entiteten i **arkivtabellen,** men du kan inte använda en EGT för att utföra dessa två operationer. För att undvika risken för att ett fel gör att en entitet visas i båda eller båda tabellerna måste arkivåtgärden så småningom vara konsekvent. I följande sekvensdiagram beskrivs stegen i den här åtgärden. Mer information ges för undantagssökvägar i texten nedan.  

![Azure-kölösning](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

En klient initierar arkivåtgärden genom att placera ett meddelande i en Azure-kö, i det här exemplet för att arkivera medarbetare #456. En arbetarroll avbländerar kön efter nya meddelanden. när den hittar en, läser det meddelandet och lämnar en dold kopia i kön. Arbetarrollen hämtar sedan en kopia av entiteten från tabellen **Aktuell,** infogar en kopia i **arkivtabellen** och tar sedan bort originalet från tabellen **Aktuell.** Slutligen, om det inte fanns några fel från föregående steg, tar arbetarrollen bort det dolda meddelandet från kön.  

I det här exemplet infogar steg **Archive** 4 medarbetaren i arkivtabellen. Det kan lägga till medarbetaren i en blob i Blob-tjänsten eller en fil i ett filsystem.  

### <a name="recovering-from-failures"></a>Återställa från fel
Det är viktigt att åtgärderna i steg **4** och **5** måste vara *idempotenta* om arbetarrollen behöver starta om arkivåtgärden. Om du använder tabelltjänsten bör du för steg **4** använda en "infoga eller ersätta"-åtgärd. för steg **5** bör du använda en "ta bort om det finns" i klientbiblioteket du använder. Om du använder ett annat lagringssystem måste du använda en lämplig idempotent åtgärd.  

Om arbetarrollen aldrig slutför steg **6**visas meddelandet igen i kön som är redo för arbetarrollen att försöka bearbeta det igen efter en timeout. Arbetarrollen kan kontrollera hur många gånger ett meddelande i kön har lästs och, om det behövs, flagga det är ett "gift" meddelande för undersökning genom att skicka det till en separat kö. Mer information om hur du läser kömeddelanden och kontrollerar antalet köer finns i [Hämta meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Vissa fel från tabell- och kötjänsterna är tillfälliga fel och klientprogrammet bör innehålla lämplig logik för återförsök för att hantera dem.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen ger inte transaktionsisolering. En klient kan till exempel läsa tabellerna **Aktuellt** och **Arkivera** när arbetarrollen var mellan steg **4** och **5**och se en inkonsekvent vy av data. Uppgifterna kommer att vara konsekventa så småningom.  
* Du måste vara säker på att steg 4 och 5 är idempotenta för att säkerställa eventuell konsekvens.  
* Du kan skala lösningen med hjälp av flera köer och arbetsrollinstanser.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill garantera eventuell överensstämmelse mellan entiteter som finns i olika partitioner eller tabeller. Du kan utöka det här mönstret för att säkerställa eventuell konsekvens för åtgärder i tabelltjänsten och Blob-tjänsten och andra datakällor som inte är Azure Storage, till exempel databas eller filsystem.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Transaktioner i entitetsgrupp  
* [Slå samman eller ersätta](#merge-or-replace)  

> [!NOTE]
> Om transaktionsisolering är viktigt för din lösning bör du överväga att omforma dina tabeller så att du kan använda EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Mönster för indexentiteter
Underhåll indexentiteter för att möjliggöra effektiva sökningar som returnerar listor över entiteter.  

### <a name="context-and-problem"></a>Kontext och problem
Tabelltjänsten indexerar automatiskt entiteter med hjälp av **värdena PartitionKey** och **RowKey.** Detta gör det möjligt för ett klientprogram att hämta en entitet effektivt med hjälp av en punktfråga. Med hjälp av tabellstrukturen nedan kan ett klientprogram till exempel effektivt hämta en enskild medarbetarentitet med hjälp av avdelningsnamnet och medarbetar-ID :et **(PartitionKey** och **RowKey**).  

![Medarbetarentitet](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Om du också vill kunna hämta en lista över medarbetarentiteter baserat på värdet för en annan icke-unik egenskap, till exempel deras efternamn, måste du använda en mindre effektiv partitionsgenomsökning för att hitta matchningar i stället för att använda ett index för att slå upp dem direkt. Detta beror på att tabelltjänsten inte tillhandahåller sekundära index.  

### <a name="solution"></a>Lösning
Om du vill aktivera uppslag efter efternamn med entitetsstrukturen som visas ovan måste du underhålla listor över medarbetar-ID:n. Om du vill hämta de medarbetarentiteter som har ett visst efternamn, till exempel Jones, måste du först hitta listan över medarbetar-ID:n för medarbetare med Jones som efternamn och sedan hämta dessa medarbetarentiteter. Det finns tre huvudalternativ för att lagra listor över medarbetar-ID:  

* Använd blob-lagring.  
* Skapa indexentiteter i samma partition som medarbetarentiteterna.  
* Skapa indexentiteter i en separat partition eller tabell.  

<u>Alternativ #1: Använd blob-lagring</u>  

För det första alternativet skapar du en blob för varje unikt efternamn och i varje blob lagras en lista över **värdena PartitionKey** (avdelning) och **RowKey** (medarbetar-ID) för medarbetare som har det efternamnet. När du lägger till eller tar bort en medarbetare bör du se till att innehållet i den relevanta blobben så småningom överensstämmer med medarbetarentiteterna.  

<u>Alternativ #2:</u> Skapa indexentiteter i samma partition  

För det andra alternativet använder du indexentiteter som lagrar följande data:  

![Entiteten med medarbetarindex](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

Egenskapen **EmployeeIDs** innehåller en lista över medarbetar-ID:n för medarbetare med efternamnet som lagras i **RowKey**.  

I följande steg beskrivs den process du bör följa när du lägger till en ny medarbetare om du använder det andra alternativet. I det här exemplet lägger vi till en medarbetare med ID 000152 och ett efternamn Jones på försäljningsavdelningen:  

1. Hämta indexentiteten med ett **PartitionKey-värde** "Försäljning" och **RowKey-värdet** "Jones". Spara ETag för den här entiteten som ska användas i steg 2.  
2. Skapa en entitetsgruppstransaktion (d.v.s. en batchoperation) som infogar den nya medarbetarentiteten (**PartitionKey-värdet** "Sales" och **RowKey-värdet** "00152" och uppdaterar indexentiteten (**PartitionKey-värdet** "Försäljning" och **RowKey-värde** "Jones") genom att lägga till det nya medarbetar-ID:t i listan i fältet EmployeeIDs. Mer information om transaktioner med entitetsgrupper finns i Transaktioner för entitetsgrupp.  
3. Om entitetsgruppstransaktionen misslyckas på grund av ett optimistiskt samtidighetsfel (någon annan har just ändrat indexentiteten) måste du börja om i steg 1 igen.  

Du kan använda en liknande metod för att ta bort en medarbetare om du använder det andra alternativet. Att ändra en medarbetares efternamn är något mer komplext eftersom du måste köra en entitetsgruppstransaktion som uppdaterar tre entiteter: medarbetarentiteten, indexentiteten för det gamla efternamnet och indexentiteten för det nya efternamnet. Du måste hämta varje entitet innan du gör några ändringar för att hämta ETag-värdena som du sedan kan använda för att utföra uppdateringarna med optimistisk samtidighet.  

I följande steg beskrivs den process som du bör följa när du behöver slå upp alla medarbetare med ett visst efternamn på en avdelning om du använder det andra alternativet. I det här exemplet söker vi upp alla medarbetare med efternamn Jones på försäljningsavdelningen:  

1. Hämta indexentiteten med ett **PartitionKey-värde** "Försäljning" och **RowKey-värdet** "Jones".  
2. Tolka listan över medarbetar-ID:er i fältet EmployeeIDs.  
3. Om du behöver ytterligare information om var och en av dessa anställda (till exempel deras e-postadresser) hämtar du var och en av medarbetarenheterna med hjälp av **PartitionKey-värdet** "Försäljning" och **RowKey-värden** från listan över medarbetare som du erhöll i steg 2.  

<u>Alternativ #3:</u> Skapa indexentiteter i en separat partition eller tabell  

För det tredje alternativet använder du indexentiteter som lagrar följande data:  

![Medarbetarindexentiteten i en separat partition](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


Egenskapen **EmployeeIDs** innehåller en lista över medarbetar-ID:n för medarbetare med efternamnet som lagras i **RowKey**.  

Med det tredje alternativet kan du inte använda EGT för att upprätthålla konsekvens eftersom indexentiteterna finns i en separat partition från medarbetarentiteterna. Kontrollera att indexentiteterna så småningom överensstämmer med med de anställdas entiteter.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen kräver minst två frågor för att hämta matchande entiteter: en för att fråga indexentiteterna för att hämta listan över **RowKey-värden** och sedan frågor för att hämta varje entitet i listan.  
* Med tanke på att en enskild entitet har en maximal storlek på 1 MB förutsätter alternativ #2 och alternativ #3 i lösningen att listan över medarbetar-ID:n för ett visst efternamn aldrig är större än 1 MB. Om listan över medarbetar-ID:er sannolikt är större än 1 MB använder du alternativet #1 och lagrar indexdata i blob-lagring.  
* Om du använder alternativ #2 (med egts för att hantera att lägga till och ta bort medarbetare och ändra en medarbetares efternamn) måste du utvärdera om transaktionsvolymen närmar sig skalbarhetsgränserna i en viss partition. Om så är fallet bör du överväga en så småningom konsekvent lösning (alternativ #1 eller alternativ #3) som använder köer för att hantera uppdateringsbegäranden och gör att du kan lagra indexentiteterna i en separat partition från medarbetarentiteterna.  
* Alternativ #2 i den här lösningen förutsätter att du vill slå upp efter efternamn inom en avdelning: till exempel vill du hämta en lista över medarbetare med ett efternamn Jones på försäljningsavdelningen. Om du vill kunna slå upp alla anställda med ett efternamn Jones i hela organisationen, använd antingen alternativ #1 eller alternativ #3.
* Du kan implementera en köbaserad lösning som ger slutlig konsekvens (se [mönstret Så småningom konsekventa transaktioner](#eventually-consistent-transactions-pattern) för mer information).  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill slå upp en uppsättning entiteter som alla delar ett gemensamt egenskapsvärde, till exempel alla medarbetare med efternamnet Jones.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansattnyckelmönster](#compound-key-pattern)  
* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern)  
* Transaktioner i entitetsgrupp  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Denormalisering mönster
Kombinera relaterade data tillsammans i en enda entitet så att du kan hämta alla data du behöver med en enda punktfråga.  

### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas normaliserar du vanligtvis data för att ta bort duplicering, vilket resulterar i frågor som hämtar data från flera tabeller. Om du normaliserar dina data i Azure-tabeller måste du göra flera tur- och returresor från klienten till servern för att hämta relaterade data. Med tabellstrukturen som visas nedan behöver du till exempel två tur- och returresor för att hämta information för en avdelning: en för att hämta den avdelningsentitet som innehåller chefens ID och sedan en annan begäran om att hämta chefens information i en medarbetarentitet.  

![Avdelningsenhet och medarbetarenhet](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Lösning
I stället för att lagra data i två separata entiteter, denormalisera data och behålla en kopia av chefens information i avdelningentiteten. Ett exempel:  

![Avdelningsenhet](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Med avdelningsentiteter som lagras med dessa egenskaper kan du nu hämta all information du behöver om en avdelning med hjälp av en punktfråga.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Det finns vissa kostnader i samband med lagring av vissa data två gånger. Prestandavinsten (till följd av färre förfrågningar till lagringstjänsten) uppväger vanligtvis den marginella ökningen av lagringskostnaderna (och denna kostnad uppvägs delvis av en minskning av antalet transaktioner som du behöver för att hämta information om en avdelning ).  
* Du måste behålla konsekvensen i de två entiteter som lagrar information om chefer. Du kan hantera konsekvensproblemet genom att använda EGTs för att uppdatera flera entiteter i en enda atomär transaktion: i det här fallet lagras avdelningsentiteten och medarbetarentiteten för avdelningschefen i samma partition.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du ofta behöver söka efter relaterad information. Det här mönstret minskar antalet frågor som klienten måste ställa för att hämta de data som krävs.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansattnyckelmönster](#compound-key-pattern)  
* Transaktioner i entitetsgrupp  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Sammansattnyckelmönster
Använd sammansatta **RowKey-värden** för att en klient ska kunna slå upp relaterade data med en enda punktfråga.  

### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas är det naturligt att använda kopplingar i frågor för att returnera relaterade datadelar till klienten i en enda fråga. Du kan till exempel använda medarbetar-ID för att slå upp en lista över relaterade entiteter som innehåller prestanda- och granskningsdata för medarbetaren.  

Anta att du lagrar medarbetarentiteter i tabelltjänsten med hjälp av följande struktur:  

![Struktur för medarbetarens entitet](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Du måste också lagra historiska data om recensioner och resultat för varje år medarbetaren har arbetat för din organisation och du måste kunna komma åt denna information per år. Ett alternativ är att skapa en annan tabell som lagrar entiteter med följande struktur:  

![Struktur för en alternativ anställdhet](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Observera att med den här metoden kan du välja att duplicera viss information (till exempel förnamn och efternamn) i den nya entiteten så att du kan hämta dina data med en enda begäran. Du kan dock inte upprätthålla en stark konsekvens eftersom du inte kan använda en EGT för att uppdatera de två entiteterna atomärt.  

### <a name="solution"></a>Lösning
Lagra en ny entitetstyp i den ursprungliga tabellen med hjälp av entiteter med följande struktur:  

![Lösning för medarbetarens entitetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Lägg märke till hur **RowKey** nu är en sammansatt nyckel som består av medarbetar-ID och året för granskningsdata som gör att du kan hämta medarbetarens prestanda och granska data med en enda begäran om en enda entitet.  

I följande exempel beskrivs hur du kan hämta alla granskningsdata för en viss medarbetare (till exempel medarbetare 000123 på försäljningsavdelningen):  

$filter=(PartitionKey eq 'Sales') och (RowKey ge 'empid_000123') och (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du bör använda ett lämpligt avgränsare som gör det enkelt att tolka **RowKey-värdet:** till exempel **000123_2012**.  
* Du lagrar också den här entiteten i samma partition som andra entiteter som innehåller relaterade data för samma medarbetare, vilket innebär att du kan använda EGTs för att upprätthålla en stark konsekvens.
* Du bör överväga hur ofta du kommer att fråga data för att avgöra om det här mönstret är lämpligt.  Om du till exempel kommer åt granskningsdata sällan och de viktigaste medarbetardata ofta bör du behålla dem som separata entiteter.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra en eller flera relaterade entiteter som du frågar ofta.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Transaktioner i entitetsgrupp  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  
* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Mönster för stocksvans
Hämta *n* n-entiteterna som senast lades till i en partition med hjälp av ett **RowKey-värde** som sorteras i omvänd datum och tidsordning.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt krav har kunnat hämta de senast skapade entiteterna, till exempel de 10 senaste utgiftsanspråk som skickats av en medarbetare. Tabellfrågor stöder en **$top** frågeåtgärd för att returnera de första *n-entiteterna* från en uppsättning: det finns ingen motsvarande frågeåtgärd för att returnera de sista n entiteterna i en uppsättning.  

### <a name="solution"></a>Lösning
Lagra entiteterna med hjälp av en **RowKey** som naturligt sorteras i omvänd datum/tidsordning med hjälp av så att den senaste posten alltid är den första i tabellen.  

Om du till exempel vill kunna hämta de 10 senaste utgiftsanspråk som skickats av en medarbetare kan du använda ett omvänt skalstrecksvärde som härletts från aktuellt datum/tid. Följande C#-kodexempel visar ett sätt att skapa ett lämpligt "inverterade fästingar" för en **RowKey** som sorteras från det senaste till det äldsta:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Du kan återgå till datumtidsvärdet med hjälp av följande kod:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabellfrågan ser ut så här:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du måste pad omvänd skalstreck värde med inledande nollor för att säkerställa strängen värde sorterar som förväntat.  
* Du måste vara medveten om skalbarhetsmålen på partitionsnivå. Var försiktig så att du inte skapar aktiva punktpartitioner.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver komma åt entiteter i omvänd datum/tidsordning eller när du behöver komma åt de senast tillagda entiteterna.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Prepend / bifoga anti-mönster](#prepend-append-anti-pattern)  
* [Hämta entiteter](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Ta bort mönster för hög volym
Aktivera borttagning av en stor mängd entiteter genom att lagra alla entiteter för samtidig radering i sin egen separata tabell. du tar bort entiteterna genom att ta bort tabellen.  

### <a name="context-and-problem"></a>Kontext och problem
Många program tar bort gamla data som inte längre behöver vara tillgängliga för ett klientprogram eller som programmet har arkiverat till ett annat lagringsmedium. Du identifierar vanligtvis sådana data efter ett datum: du har till exempel ett krav på att ta bort poster för alla inloggningsbegäranden som är äldre än 60 dagar.  

En möjlig design är att använda datum och tid för inloggningsbegäran i **RowKey:**  

![Datum och tid för inloggningsförsök](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Den här metoden undviker partitionshotspots eftersom programmet kan infoga och ta bort inloggningsenheter för varje användare i en separat partition. Den här metoden kan dock vara kostsam och tidskrävande om du har ett stort antal entiteter eftersom du först måste utföra en tabellsökning för att identifiera alla entiteter som ska tas bort och sedan måste du ta bort varje gammal entitet. Du kan minska antalet tur- och returresor till den server som krävs för att ta bort de gamla entiteterna genom att batcha flera borttagningsbegäranden i EGTs.  

### <a name="solution"></a>Lösning
Använd en separat tabell för varje dag av inloggningsförsök. Du kan använda entitetsdesignen ovan för att undvika aktiveringspunkter när du infogar entiteter, och att ta bort gamla entiteter är nu helt enkelt en fråga om att ta bort en tabell varje dag (en enda lagringsåtgärd) i stället för att hitta och ta bort hundratals och tusentals enskilda inloggningsenheter varje dag.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder din design andra sätt som ditt program använder data, till exempel att leta upp specifika entiteter, länka med andra data eller generera aggregerad information?  
* Undviker din design aktiva punkter när du infogar nya enheter?  
* Räkna med en fördröjning om du vill återanvända samma tabellnamn när du har tagit bort det. Det är bättre att alltid använda unika tabellnamn.  
* Förvänta dig en viss begränsning när du först använder en ny tabell medan tabelltjänsten lär sig åtkomstmönstren och distribuerar partitionerna över noder. Du bör överväga hur ofta du behöver skapa nya tabeller.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du har en hög mängd entiteter som du måste ta bort samtidigt.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Transaktioner i entitetsgrupp
* [Ändra entiteter](#modifying-entities)  

## <a name="data-series-pattern"></a>Mönster för dataserier
Lagra fullständiga dataserier i en enda entitet för att minimera antalet begäranden du gör.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt scenario är att ett program lagrar en serie data som det vanligtvis behöver hämta alla på en gång. Ditt program kan till exempel registrera hur många snabbmeddelande som varje anställd skickar varje timme och sedan använda den här informationen för att rita hur många meddelanden varje användare har skickat under de föregående 24 timmarna. En design kan vara att lagra 24 entiteter för varje anställd:  

![Lagra 24 entiteter för varje medarbetare](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Med den här designen kan du enkelt hitta och uppdatera entiteten för att uppdatera för varje medarbetare när programmet behöver uppdatera värdet för antalet meddelanden. Om du vill hämta informationen för att rita ett diagram över aktiviteten under de föregående 24 timmarna måste du hämta 24 entiteter.  

### <a name="solution"></a>Lösning
Använd följande design med en separat egenskap för att lagra antalet meddelanden för varje timme:  

![Entiteten meddelandestatistik](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Med den här designen kan du använda en kopplingsåtgärd för att uppdatera meddelandeantalet för en medarbetare under en viss timme. Nu kan du hämta all information du behöver för att rita diagrammet med en begäran om en enda entitet.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om hela dataserien inte passar in i en enda entitet (en entitet kan ha upp till 252 egenskaper) använder du ett alternativt datalager, till exempel en blob.  
* Om du har flera klienter som uppdaterar en entitet samtidigt måste du använda **ETag för** att implementera optimistisk samtidighet. Om du har många kunder, kan du uppleva hög konkurrens.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver uppdatera och hämta en dataserie som är associerad med en enskild entitet.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för stora enheter](#large-entities-pattern)  
* [Slå samman eller ersätta](#merge-or-replace)  
* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern) (om du lagrar dataserien i en blob)  

## <a name="wide-entities-pattern"></a>Brett entitetsmönster
Använd flera fysiska entiteter för att lagra logiska entiteter med mer än 252 egenskaper.  

### <a name="context-and-problem"></a>Kontext och problem
En enskild enhet kan inte ha fler än 252 egenskaper (exklusive obligatoriska systemegenskaper) och kan inte lagra mer än 1 MB data totalt. I en relationsdatabas får du vanligtvis runda eventuella gränser för storleken på en rad genom att lägga till en ny tabell och tillämpa en 1-till-1-relation mellan dem.  

### <a name="solution"></a>Lösning
Med tabelltjänsten kan du lagra flera entiteter för att representera ett enda stort affärsobjekt med fler än 252 egenskaper. Om du till exempel vill lagra antalet snabbmeddelandemeddelanden som skickats av varje medarbetare under de senaste 365 dagarna kan du använda följande design som använder två entiteter med olika scheman:  

![Flera enheter](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Om du behöver göra en ändring som kräver att du uppdaterar båda entiteterna för att hålla dem synkroniserade med varandra kan du använda en EGT. Annars kan du använda en enda kopplingsåtgärd för att uppdatera antalet meddelanden för en viss dag. Om du vill hämta alla data för en enskild medarbetare måste du hämta båda entiteterna, vilket du kan göra med två effektiva begäranden som använder både ett **PartitionKey-** och **RowKey-värde.**  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Hämtning av en fullständig logisk entitet omfattar minst två lagringstransaktioner: en för att hämta varje fysisk entitet.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek eller antal egenskaper överskrider gränserna för en enskild entitet i tabelltjänsten.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* Transaktioner i entitetsgrupp
* [Slå samman eller ersätta](#merge-or-replace)

## <a name="large-entities-pattern"></a>Mönster för stora enheter
Använd blob-lagring för att lagra stora egenskapsvärden.  

### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte lagra mer än 1 MB data totalt. Om ett eller flera av dina egenskapsarkivvärden som gör att den totala storleken på din entitet överskrider det här värdet kan du inte lagra hela entiteten i tabelltjänsten.  

### <a name="solution"></a>Lösning
Om din entitet är större än 1 MB eftersom en eller flera egenskaper innehåller en stor mängd data, kan du lagra data i Blob-tjänsten och sedan lagra adressen till bloben i en egenskap i entiteten. Du kan till exempel lagra fotot på en anställd i blob-lagring och lagra en länk till fotot i egenskapen **Foto** för din anställdahet:  

![Foto egendom](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om du vill behålla eventuell överensstämmelse mellan entiteten i tabelltjänsten och data i Blob-tjänsten använder du [mönstret Så småningom konsekventa transaktioner](#eventually-consistent-transactions-pattern) för att underhålla dina entiteter.
* Hämtning av en fullständig entitet omfattar minst två lagringstransaktioner: en för att hämta entiteten och en för att hämta blob-data.  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek överskrider gränserna för en enskild entitet i tabelltjänsten.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern)  
* [Brett entitetsmönster](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Prepend/lägg till anti-mönster
Öka skalbarheten när du har en hög mängd skär genom att sprida skären över flera partitioner.  

### <a name="context-and-problem"></a>Kontext och problem
Prepending eller lägga till entiteter till dina lagrade entiteter resulterar vanligtvis i att programmet lägger till nya entiteter i den första eller sista partitionen i en sekvens av partitioner. I det här fallet sker alla skär vid en given tidpunkt i samma partition, vilket skapar en aktiveringspunkt som förhindrar att tabelltjänsten inte balanserar skär över flera noder och eventuellt orsakar att programmet träffar skalbarhetsmålen för Partition. Om du till exempel har ett program som loggar nätverks- och resursåtkomst av medarbetare kan en entitetsstruktur som visas nedan leda till att den aktuella timmens partition blir en aktiveringspunkt om transaktionsvolymen når skalbarhetsmålet för en enskild partition:  

![Entitetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Lösning
Följande alternativa entitetsstruktur undviker en aktiveringspunkt på en viss partition när programmet loggar händelser:  

![Alternativ enhetsstruktur](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Observera med det här exemplet hur både **PartitionKey** och **RowKey** är sammansatta nycklar. **PartitionKey** använder både avdelningen och medarbetar-ID för att distribuera loggningen över flera partitioner.  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder den alternativa nyckelstrukturen som undviker att skapa heta partitioner på skär effektivt de frågor som klientprogrammet ställer?  
* Innebär den förväntade transaktionsvolymen att du sannolikt kommer att nå skalbarhetsmålen för en enskild partition och begränsas av lagringstjänsten?  

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Undvik det anti-mönster som är förpreset/tillägg när transaktionsvolymen sannolikt kommer att leda till begränsning av lagringstjänsten när du öppnar en frekvent partition.  

### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansattnyckelmönster](#compound-key-pattern)  
* [Mönster för stocksvans](#log-tail-pattern)  
* [Ändra entiteter](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Logga data mot mönster
Vanligtvis bör du använda Blob-tjänsten i stället för tabelltjänsten för att lagra loggdata.  

### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt användningsfall för loggdata är att hämta ett urval av loggposter för ett visst datum-/tidsintervall: du vill till exempel hitta alla fel och viktiga meddelanden som programmet loggade mellan 15:04 och 15:06 på ett visst datum. Du vill inte använda datum och tid för loggmeddelandet för att bestämma vilken partition du sparar loggentiteter till: det resulterar i en frekvent partition eftersom alla loggentiteter vid varje given tidpunkt delar samma **PartitionKey-värde** (se avsnittet [Prepend/append anti-pattern](#prepend-append-anti-pattern)). Följande entitetsschema för ett loggmeddelande resulterar till exempel i en frekvent partition eftersom programmet skriver alla loggmeddelanden till partitionen för aktuellt datum och timme:  

![Entiteten logga meddelanden](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

I det här exemplet innehåller **RowKey** datum och tid för loggmeddelandet för att säkerställa att loggmeddelanden lagras sorterade i datum/tidsordning och innehåller ett meddelande-ID om flera loggmeddelanden delar samma datum och tid.  

En annan metod är att använda en **PartitionKey** som säkerställer att programmet skriver meddelanden över en rad partitioner. Om källan till loggmeddelandet till exempel är ett sätt att distribuera meddelanden över många partitioner kan du använda följande entitetsschema:  

![Entiteten för alternativt loggmeddelande](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Problemet med det här schemat är dock att för att hämta alla loggmeddelanden för en viss tidsperiod måste du söka varje partition i tabellen.

### <a name="solution"></a>Lösning
I föregående avsnitt belystes problemet med att försöka använda tabelltjänsten för att lagra loggposter och föreslog två, otillfredsställande, design. En lösning ledde till en het partition med risk för dålig prestanda att skriva loggmeddelanden; Den andra lösningen resulterade i dåliga frågeprestanda på grund av kravet på att söka igenom varje partition i tabellen för att hämta loggmeddelanden under en viss tidsperiod. Blob storage erbjuder en bättre lösning för den här typen av scenario och det är så Här lagrar Azure Storage Analytics loggdata som samlas in.  

I det här avsnittet beskrivs hur Lagringsanalys lagrar loggdata i blob-lagring som en illustration av den här metoden för att lagra data som du vanligtvis frågar efter intervall.  

Storage Analytics lagrar loggmeddelanden i avgränsat format i flera blobbar. Det avgränsade formatet gör det enkelt för ett klientprogram att tolka data i loggmeddelandet.  

Storage Analytics använder en namngivningskonvention för blobbar som gör att du kan hitta blobben (eller blobbar) som innehåller de loggmeddelanden som du söker efter. Till exempel innehåller en blob med namnet "queue/2014/07/31/1800/000001.log" loggmeddelanden som relaterar till kötjänsten för timmen som börjar klockan 18:00 den 31 juli 2014. "000001" anger att detta är den första loggfilen för den här perioden. Storage Analytics registrerar också tidsstämplarna för de första och sista loggmeddelanden som lagras i filen som en del av blobens metadata. Api:et för blob-lagring gör att du kan hitta blobbar i en behållare baserat på ett namnprefix: för att hitta alla blobbar som innehåller köloggdata för timmen som börjar klockan 18:00 kan du använda prefixet "queue/2014/07/31/1800".  

Storage Analytics buffrar loggmeddelanden internt och uppdaterar sedan regelbundet lämplig blob eller skapar en ny med den senaste batchen med loggposter. Detta minskar antalet skrivningar som den måste utföra till blob-tjänsten.  

Om du implementerar en liknande lösning i ditt eget program måste du överväga hur du hanterar avvägningen mellan tillförlitlighet (skriva varje loggpost till blob-lagring när det händer) och kostnad och skalbarhet (buffring av uppdateringar i ditt program och skriva dem att blob lagring i omgångar).  

### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande punkter när du bestämmer hur loggdata ska lagras:  

* Om du skapar en tabelldesign som undviker potentiella heta partitioner kan det hända att du inte kan komma åt loggdata effektivt.  
* För att bearbeta loggdata måste en klient ofta läsa in många poster.  
* Även om loggdata ofta är strukturerade kan blob-lagring vara en bättre lösning.  

## <a name="implementation-considerations"></a>Att tänka på vid implementering
I det här avsnittet beskrivs några av de överväganden som ska beaktas när du implementerar de mönster som beskrivs i föregående avsnitt. De flesta av de här avsnitten använder exempel skrivna i C# som använder Storage Client Library (version 4.3.0 i skrivande stund).  

## <a name="retrieving-entities"></a>Hämta entiteter
Som beskrivs i avsnittet Design för frågor är den mest effektiva frågan en punktfråga. I vissa fall kan du dock behöva hämta flera entiteter. I det här avsnittet beskrivs några vanliga metoder för att hämta entiteter med hjälp av storage client-biblioteket.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Köra en punktfråga med lagringsklientbiblioteket
Det enklaste sättet att köra en punktfråga är att använda åtgärden **Hämta** tabell som visas i följande C#-kodavsnitt som hämtar en entitet med en **PartitionKey** av värdet "Sales" och en **RowKey** med värdet "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Lägg märke till hur det här exemplet förväntar sig att entiteten som hämtas ska vara av typen **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Hämtar flera enheter med LINQ
Du kan använda LINQ för att hämta flera entiteter från tabelltjänsten när du arbetar med Microsoft Azure Cosmos Table Standard Library. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Om du vill att exemplen nedan ska fungera måste du inkludera namnområden:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

EmployeeTable är ett CloudTable-objekt som implementerar en CreateQuery\<ITableEntity>() som\<returnerar en TableQuery ITableEntity->. Objekt av den här typen implementerar en IQueryable och tillåter användning av både LINQ-frågeuttryck och punktateringssyntax.

Hämtar flera entiteter och uppnås genom att ange en fråga med en **where-sats.** För att undvika en tabellsökning bör du alltid inkludera **PartitionKey-värdet** i where-satsen, och om möjligt **RowKey-värdet** för att undvika tabell- och partitionssökningar. Tabelltjänsten stöder en begränsad uppsättning jämförelseoperatorer (större än, större än eller lika, mindre än, mindre än eller lika, lika och inte lika) att använda i where-satsen. 

Följande C#-kodavsnitt hittar alla anställda vars efternamn börjar med "B" (förutsatt att **RowKey** lagrar efternamnet) på försäljningsavdelningen (förutsatt att **PartitionKey** lagrar avdelningsnamnet):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Lägg märke till hur frågan anger både en **RowKey** och en **PartitionKey** för att säkerställa bättre prestanda.  

Följande kodexempel visar motsvarande funktioner utan att använda LINQ-syntax:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Exemplet kapslar flera **CombineFilters-metoder** för att inkludera de tre filtervillkoren.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Hämta ett stort antal entiteter från en fråga
En optimal fråga returnerar en enskild entitet baserat på ett **PartitionKey-värde** och ett **RowKey-värde.** I vissa fall kan du dock ha ett krav på att returnera många entiteter från samma partition eller till och med från många partitioner.  

Du bör alltid testa programmets prestanda i sådana scenarier.  

En fråga mot tabelltjänsten kan returnera högst 1 000 entiteter samtidigt och kan köras i högst fem sekunder. Om resultatuppsättningen innehåller mer än 1 000 entiteter, om frågan inte slutfördes inom fem sekunder, eller om frågan korsar partitionsgränsen, returnerar tabelltjänsten en fortsättningstoken för att klientprogrammet ska kunna begära nästa uppsättning entiteter. Mer information om hur fortsättningstoken fungerar finns i [Frågetidout och sidnumrering](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Om du använder lagringsklientbiblioteket kan det automatiskt hantera fortsättningstoken åt dig när det returnerar entiteter från tabelltjänsten. Följande C#-kodexempel med lagringsklientbiblioteket hanterar automatiskt fortsättningstoken om tabelltjänsten returnerar dem i ett svar:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Följande C#-kod hanterar fortsättningstoken uttryckligen:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Genom att uttryckligen använda fortsättningstoken kan du styra när programmet hämtar nästa datasegment. Om klientprogrammet till exempel gör det möjligt för användare att bläddra igenom de entiteter som lagras i en tabell, kan en användare välja att inte bläddra igenom alla entiteter som hämtas av frågan så att ditt program bara använder en fortsättningstoken för att hämta nästa segment när användaren hade slutfört växlingen genom alla entiteter i det aktuella segmentet. Detta tillvägagångssätt har flera fördelar:  

* Det gör att du kan begränsa mängden data som ska hämtas från tabelltjänsten och att du flyttar över nätverket.  
* Det gör att du kan utföra asynkron IO i .NET.  
* Det gör att du kan serialisera fortsättningstoken till beständig lagring så att du kan fortsätta i händelse av en programkrasch.  

> [!NOTE]
> En fortsättningstoken returnerar vanligtvis ett segment som innehåller 1 000 entiteter, även om det kan vara färre. Detta är också fallet om du begränsar antalet transaktioner som en fråga returnerar genom att använda **Take** för att returnera de första n-entiteterna som matchar uppslagsvillkoren: tabelltjänsten kan returnera ett segment som innehåller färre än n entiteter tillsammans med en fortsättningstoken så att du kan hämta de återstående entiteterna.  
> 
> 

Följande C#-kod visar hur du ändrar antalet entiteter som returneras inom ett segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projektion på serversidan
En enda entitet kan ha upp till 255 egenskaper och vara upp till 1 MB i storlek. När du frågar tabellen och hämtar entiteter behöver du kanske inte alla egenskaper och kan undvika att överföra data i onödan (för att minska svarstiden och kostnaden). Du kan använda projektion på serversidan för att överföra bara de egenskaper du behöver. Följande exempel är hämtar bara egenskapen **E-post** (tillsammans med **PartitionKey**, **RowKey**, **Timestamp**och **ETag**) från de entiteter som valts av frågan.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Lägg märke till hur **RowKey-värdet** är tillgängligt även om det inte ingick i listan över egenskaper att hämta.  

## <a name="modifying-entities"></a>Ändra entiteter
Med storage client-biblioteket kan du ändra de entiteter som lagras i tabelltjänsten genom att infoga, ta bort och uppdatera entiteter. Du kan använda EGTs för att batcha flera skär, uppdatera och ta bort åtgärder tillsammans för att minska antalet tur- och returresor som krävs och förbättra prestanda för din lösning.  

Undantag som genereras när storage client library kör en EGT innehåller vanligtvis index för entiteten som orsakade batchen misslyckas. Detta är användbart när du felsöker kod som använder EGTs.  

Du bör också överväga hur din design påverkar hur klientprogrammet hanterar samtidighet och uppdateringsåtgärder.  

### <a name="managing-concurrency"></a>Hantera samtidighet
Som standard implementerar tabelltjänsten optimistiska samtidighetskontroller på nivån för enskilda entiteter för **Åtgärder infoga,** **sammanfoga**och **ta bort,** även om det är möjligt för en klient att tvinga tabelltjänsten att kringgå dessa kontroller. Mer information om hur tabelltjänsten hanterar samtidighet finns [i Hantera samtidighet i Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Slå samman eller ersätta
Metoden **Ersätt** för klassen **TableOperation** ersätter alltid den fullständiga entiteten i tabelltjänsten. Om du inte inkluderar en egenskap i begäran när den egenskapen finns i den lagrade entiteten, tas den egenskapen bort från den lagrade entiteten. Om du inte vill ta bort en egenskap uttryckligen från en lagrad entitet måste du inkludera alla egenskaper i begäran.  

Du kan använda metoden **Sammanfoga** i klassen **TableOperation** för att minska mängden data som du skickar till tabelltjänsten när du vill uppdatera en entitet. **Metoden Koppla** ersätter alla egenskaper i den lagrade entiteten med egenskapsvärden från den entitet som ingår i begäran, men lämnar intakta egenskaper i den lagrade entiteten som inte ingår i begäran. Detta är användbart om du har stora entiteter och bara behöver uppdatera ett litet antal egenskaper i en begäran.  

> [!NOTE]
> Metoderna **Ersätt** och **sammanfoga** misslyckas om entiteten inte finns. Som ett alternativ kan du använda metoderna **InsertOrReplace** och **InsertOrMerge** som skapar en ny entitet om den inte finns.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Arbeta med heterogena entitetstyper
Tabelltjänsten är ett *schemalösa* tabellarkiv som innebär att en enda tabell kan lagra entiteter av flera typer som ger stor flexibilitet i designen. I följande exempel visas en tabell som lagrar både medarbetare och avdelningsenheter:  

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
<th>DepartmentName</th>
<th>MedarbetareCount</th>
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

Varje entitet måste fortfarande ha **Värden för PartitionKey**, **RowKey**och **Timestamp,** men kan ha alla egenskaper. Dessutom finns det inget som anger vilken typ av en enhet om du inte väljer att lagra den informationen någonstans. Det finns två alternativ för att identifiera entitetstypen:  

* Förbered entitetstypen till **RowKey** (eller möjligen **PartitionKey**). Till exempel **EMPLOYEE_000123** eller **DEPARTMENT_SALES** som **RowKey-värden.**  
* Använd en separat egenskap för att registrera entitetstypen enligt tabellen nedan.  

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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Anställd</td>
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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Anställd</td>
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
<th>EntityType</th>
<th>DepartmentName</th>
<th>MedarbetareCount</th>
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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Anställd</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Det första alternativet, som väntar på entitetstypen till **RowKey,** är användbart om det finns en möjlighet att två entiteter av olika typer kan ha samma nyckelvärde. Den grupperar också entiteter av samma typ tillsammans i partitionen.  

De tekniker som beskrivs i det här avsnittet är särskilt relevanta för diskussionen [Arv relationer](table-storage-design-modeling.md#inheritance-relationships) tidigare i den här guiden i artikeln [Modellering relationer](table-storage-design-modeling.md).  

> [!NOTE]
> Du bör överväga att inkludera ett versionsnummer i värdet för entitetstyp för att klientprogram ska kunna utveckla POCO-objekt och arbeta med olika versioner.  
> 
> 

I resten av det här avsnittet beskrivs några av funktionerna i lagringsklientbiblioteket som underlättar arbetet med flera entitetstyper i samma tabell.  

### <a name="retrieving-heterogeneous-entity-types"></a>Hämta typer av heterogena entiteter
Om du använder lagringsklientbiblioteket har du tre alternativ för att arbeta med flera entitetstyper.  

Om du känner till typen av entitet som lagras med ett visst **RowKey-** och **PartitionKey-värden** kan du ange entitetstypen när du hämtar entiteten enligt de två föregående exemplen som hämtar entiteter av typen **EmployeeEntity:** [Kör en punktfråga med hjälp av storage client library](#executing-a-point-query-using-the-storage-client-library) och hämtar flera [entiteter med LINQ](#retrieving-multiple-entities-using-linq).  

Det andra alternativet är att använda typen **DynamicTableEntity** (en egenskapspåse) i stället för en konkret POCO-entitetstyp (det här alternativet kan också förbättra prestanda eftersom det inte finns något behov av att serialisera och avserialisera entiteten till .NET-typer). Följande C#-kod hämtar potentiellt flera entiteter av olika typer från tabellen, men returnerar alla entiteter som **DynamicTableEntity-instanser.** Den använder sedan egenskapen **EntityType** för att bestämma typen av varje entitet:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
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
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Om du vill hämta andra egenskaper måste du använda metoden **TryGetValue** i egenskapen **Egenskaper** för klassen **DynamicTableEntity.**  

Ett tredje alternativ är att kombinera med hjälp av typen **DynamicTableEntity** och en **EntityResolver-instans.** På så sätt kan du matcha till flera POCO-typer i samma fråga. I det här exemplet använder **EntityResolver-delegaten** egenskapen **EntityType** för att skilja mellan de två typer av entiteter som frågan returnerar. Metoden **Resolve** använder **resolver-delegaten** för att matcha **DynamicTableEntity-instanser** i **TableEntity-instanser.**  

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
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Ändra typer av heterogena entiteter
Du behöver inte känna till vilken typ av en entitet som ska tas bort, och du vet alltid vilken typ av en entitet när du infogar den. Du kan dock använda **DynamicTableEntity-typ** för att uppdatera en entitet utan att känna till dess typ och utan att använda en POCO-entitetsklass. Följande kodexempel hämtar en enskild entitet och kontrollerar att egenskapen **EmployeeCount** finns innan den uppdateras.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Styra åtkomst med signaturer för delad åtkomst
Du kan använda SAS-token (Shared Access Signature) för att klientprogram ska kunna ändra (och fråga) tabellentiteter utan att behöva inkludera din lagringskontonyckel i koden. Vanligtvis finns det tre huvudsakliga fördelar med att använda SAS i ditt program:  

* Du behöver inte distribuera din lagringskontonyckel till en osäker plattform (till exempel en mobil enhet) för att enheten ska kunna komma åt och ändra entiteter i tabelltjänsten.  
* Du kan avlasta en del av det arbete som webb- och arbetsroller utför för att hantera dina entiteter till klientenheter som slutanvändardatorer och mobila enheter.  
* Du kan tilldela en klient en begränsad och tidsbegränsad uppsättning behörigheter (till exempel tillåta skrivskyddad åtkomst till specifika resurser).  

Mer information om hur du använder SAS-token med tabelltjänsten finns i [Använda SIGNATURER för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md).  

Du måste dock fortfarande generera SAS-token som ger ett klientprogram till entiteterna i tabelltjänsten: du bör göra detta i en miljö som har säker åtkomst till dina lagringskontonycklar. Vanligtvis använder du en webb- eller arbetarroll för att generera SAS-token och leverera dem till klientprogrammen som behöver åtkomst till dina entiteter. Eftersom det fortfarande finns en overhead som är involverad i att generera och leverera SAS-token till klienter, bör du överväga hur du bäst kan minska den här omkostnaden, särskilt i scenarier med stora volymer.  

Det är möjligt att generera en SAS-token som ger åtkomst till en delmängd av entiteterna i en tabell. Som standard skapar du en SAS-token för en hel tabell, men det är också möjligt att ange att SAS-token beviljar åtkomst till antingen ett intervall med **PartitionKey-värden** eller ett intervall med **PartitionKey-** och **RowKey-värden.** Du kan välja att generera SAS-token för enskilda användare av ditt system så att varje användares SAS-token endast ger dem åtkomst till sina egna entiteter i tabelltjänsten.  

## <a name="asynchronous-and-parallel-operations"></a>Asynkrona och parallella operationer
Förutsatt att du sprider dina begäranden över flera partitioner kan du förbättra dataflödet och klientens svarstider med hjälp av asynkrona eller parallella frågor.
Du kan till exempel ha två eller flera arbetsrollinstanser som ansluter till tabellerna parallellt. Du kan ha enskilda arbetarroller som ansvarar för vissa uppsättningar partitioner, eller helt enkelt ha flera arbetsrollinstanser, som var och en kan komma åt alla partitioner i en tabell.  

Inom en klientinstans kan du förbättra dataflödet genom att köra lagringsåtgärder asynkront. Storage Client Library gör det enkelt att skriva asynkrona frågor och ändringar. Du kan till exempel börja med den synkrona metoden som hämtar alla entiteter i en partition som visas i följande C#-kod:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Du kan enkelt ändra den här koden så att frågan körs asynkront enligt följande:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

I det här asynkrona exemplet kan du se följande ändringar från den synkrona versionen:  

* Metodsignaturen innehåller nu **asynkronmodifieraren** och returnerar en **aktivitetsinstans.**  
* I stället för att anropa **metoden ExecuteSegmented** för att hämta resultat anropar metoden **executesegmentedAsync** och använder **den väntande** modifieraren för att hämta resultat asynkront.  

Klientprogrammet kan anropa den här metoden flera **department** gånger (med olika värden för avdelningsparametern) och varje fråga körs på en separat tråd.  

Det finns ingen asynkron version av **körmetoden** i klassen **TableQuery** eftersom gränssnittet **IEnumerable** inte stöder asynkron uppräkning.  

Du kan också infoga, uppdatera och ta bort entiteter asynkront. I följande C#-exempel visas en enkel, synkron metod för att infoga eller ersätta en medarbetaretitet:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Du kan enkelt ändra den här koden så att uppdateringen körs asynkront enligt följande:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

I det här asynkrona exemplet kan du se följande ändringar från den synkrona versionen:  

* Metodsignaturen innehåller nu **asynkronmodifieraren** och returnerar en **aktivitetsinstans.**  
* I stället för att anropa **metoden Kör** för att uppdatera entiteten anropar metoden nu **metoden ExecuteAsync** och använder **den väntande** modifieraren för att hämta resultat asynkront.  

Klientprogrammet kan anropa flera asynkrona metoder som den här, och varje metodanrop körs på en separat tråd.  

## <a name="next-steps"></a>Nästa steg

- [Modellera relationer](table-storage-design-modeling.md)
- [Utforma för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)
