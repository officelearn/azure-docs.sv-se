---
title: "Kompletterande lektion i Azure Analysis Services-självstudiekurs: Dynamisk säkerhet | Microsoft Docs"
description: "Beskriver hur du använder dynamisk säkerhet med hjälp av radfilter i Azure Analysis Services-kursen."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 4e97a558ae1a2601b5275a73164b483351f03857
ms.contentlocale: sv-se
ms.lasthandoff: 07/26/2017

---
# <a name="supplemental-lesson---dynamic-security"></a>Kompletterande lektion – Dynamisk säkerhet

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

I den här kompletterande lektionen skapar du en ytterligare roll som implementerar dynamisk säkerhet. Med dynamisk säkerhet får du säkerhet på radnivå baserat på användarnamn eller inloggnings-id för den användare som är inloggad. 
  
Om du vill implementera dynamisk säkerhet lägger du till en tabell i din modell som innehåller användarnamnen för de användare som ska kunna ansluta till modellen och visa modellobjekt och data. Modellen du skapar i den här självstudiekursen finns i kontexten för Adventure Works. Men för att slutföra den här lektionen måste du lägga till en tabell som innehåller användare från din egen domän. Du behöver inte lösenorden för användarnamnen som läggs till. För att skapa en EmployeeSecurity-tabell med ett litet antal användare från din egen domän använder du funktionen Klistra in och klistrar in data från ett Excel-kalkylblad. I ett verkligt scenario skulle tabellen som innehåller användarnamn vanligtvis vara en tabell från en faktisk databas som en datakälla, till exempel en verklig DimEmployee-tabell.  
  
När du implementerar dynamisk säkerhet använder du två DAX-funktioner: [USERNAME (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) och [LOOKUPVALUE (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Dessa funktioner, som tillämpas i en radfilterformel, definieras i en ny roll. Med hjälp av funktionen LOOKUPVALUE anger formeln ett värde från tabellen EmployeeSecurity. Formeln skickar sedan att värdet till funktionen USERNAME som anger användarnamnet för den inloggade användaren som tillhör den här rollen. Användaren kan sedan endast visa de data som anges av rollens radfilter. I det här scenariot anger du att säljpersonal endast ska kunna visa information om Internetförsäljning för de försäljningsområden som de är medlem i.  
  
De aktiviteter som är unika för det här Adventure Works-tabellmodellscenariot, men som inte nödvändigtvis skulle gälla för ett verkligt scenario identifieras som sådana. Varje aktivitet innehåller ytterligare information som beskriver syftet med aktiviteten.  
  
Uppskattad tidsåtgång för den här lektionen: **30 minuter**  
  
## <a name="prerequisites"></a>Krav  
Den här kompletterande lektionen ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför aktiviteterna i den här kompletterande lektionen måste du ha slutfört alla föregående lektioner.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>Lägga till tabellen DimSalesTerritory i AW Internet Sales-tabellmodellprojektet  
Om du vill implementera dynamisk säkerhet för det här Adventure Works-scenariot måste du lägga till ytterligare två tabeller i modellen. Först lägger du till tabellen DimSalesTerritory (som försäljningsområde) från samma AdventureWorksDW-databas. Sedan tillämpar du ett radfilter för tabellen SalesTerritory som definierar vilka data som den inloggade användaren kan visa.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>Lägga till tabellen DimSalesTerritory  
  
1.  I tabellmodellutforskaren > **Datakällor** högerklickar du på din anslutning och sedan på **Importera nya tabeller**.  

    Om dialogrutan Autentiseringsuppgifter för personifiering visas anger du de autentiseringsuppgifter för personifiering som du använde i Lektion 2: Lägga till data.
  
2.  I navigatören väljer du tabellen **DimSalesTerritory** och klickar sedan på **OK**.    
  
3.  I frågeredigeraren klickar du på frågan **DimSalesTerritory** och tar sedan bort kolumnen **SalesTerritoryAlternateKey**.  
  
7.  Klicka på **Importera**.  
  
    Den nya tabellen läggs till i modellarbetsytan. Objekt och data från tabellen DimSalesTerritory importeras sedan till din AW Internet Sales-tabellmodell.  
  
9. När tabellen har importerats klickar du på **Stäng**.  

## <a name="add-a-table-with-user-name-data"></a>Lägga till en tabell med användarnamndata  
Tabellen DimEmployee i AdventureWorksDW-exempeldatabasen innehåller användare från AdventureWorks-domänen. Dessa användarnamn finns inte i din egen miljö. Du måste skapa en tabell i din modell som innehåller ett litet antal (minst tre) faktiska användare från din organisation. Sedan lägger du till dessa användare som medlemmar i den nya rollen. Du behöver inte lösenorden för exempelanvändarnamnen, men du behöver de faktiska Windows-användarnamnen från din domän.  
  
#### <a name="to-add-an-employeesecurity-table"></a>Så här lägger du till en EmployeeSecurity-tabell  
  
1.  Öppna Microsoft Excel och skapa ett kalkylblad.  
  
2.  Kopiera följande tabell, inklusive rubrikraden, och klistra in den i kalkylbladet.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Ersätt förnamn, efternamn och domän\användarnamn med namnen och inloggnings-id:n för de tre användarna i din organisation. Placera samma användare på de första två raderna, för EmployeeId 1, så att den här användaren tillhör fler än ett försäljningsområde. Lämna fälten EmployeeId och SalesTerritoryId som de är.  
  
4.  Spara kalkylbladet som **SampleEmployee**.  
  
5.  I kalkylbladet markerar du alla celler med information om anställda, inklusive rubrikerna. Högerklicka sedan på de markerade data och klicka på **Kopiera**.  
  
6.  Välj **Redigera**-menyn i SSDT och klicka på **Klistra in**.  
  
    Om Klistra in är nedtonat klickar du på valfri kolumn i valfri tabell i modelldesignerfönstret. Försök sedan igen.  
  
7.  Skriv **EmployeeSecurity** i **Tabellnamn** i dialogrutan **Förhandsgranska inklistring**.  
  
8.  I **Data som ska klistras in** kontrollerar du att data innehåller alla användardata och alla rubriker från kalkylbladet SampleEmployee.  
  
9. Kontrollera att **Låt första raden utgöra kolumnrubriker** är markerat och klicka sedan på **Ok**.  
  
    En ny tabell med namnet EmployeeSecurity skapas med information om anställda som kopieras från kalkylbladet SampleEmployee.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Skapa relationer mellan tabellerna FactInternetSales, DimGeography och DimSalesTerritory  
Tabellerna FactInternetSales, DimGeography och DimSalesTerritory innehåller var och en kolumnen SalesTerritoryId. Kolumnen SalesTerritoryId i tabellen DimSalesTerritory innehåller värden med olika id för varje försäljningsområde.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Så här skapar du relationer mellan tabellerna FactInternetSales, DimGeography och DimSalesTerritory  
  
1.  Klicka på och håll ned kolumnen **SalesTerritoryId** i tabellen **DimGeography** i diagramvyn och dra sedan markören till kolumnen **SalesTerritoryId** i tabellen **DimSalesTerritory** och släpp.  
  
2.  Klicka på och håll ned kolumnen **SalesTerritoryId** i tabellen **FactInternetSales** och dra sedan markören till kolumnen **SalesTerritoryId** i tabellen **DimSalesTerritory** och släpp.  
  
    Observera att egenskapen Active för den här relationen är False (falsk), vilket innebär att relationen är inaktiv. Det finns redan en annan aktiv relation för tabellen FactInternetSales.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Dölja tabellen EmployeeSecurity för klientprogram  
I den här aktiviteten döljer du tabellen EmployeeSecurity så att den inte visas i fältlistan i klientprogram. Tänk på att du inte skyddar tabellen genom att dölja den. Användare kan fortfarande fråga på EmployeeSecurity-tabelldata om de vet hur man gör. Om du vill skydda data i tabellen EmployeeSecurity och hindra användare från att kunna fråga på data i tabellen använder du ett filter (se senare aktivitet).  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Så här döljer du tabellen EmployeeSecurity för klientprogram  
  
-   Högerklicka på tabellrubriken **Employee** (Anställd) i diagramvyn i modelldesignern och klicka sedan på **Dölj för klientverktyg**.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Skapa användarrollen Säljpersonal per område  
I den här aktiviteten skapar du en användarroll. Rollen innefattar ett radfilter som definierar vilka rader i tabellen DimSalesTerritory som ska vara synliga för användare. Filtret tillämpas sedan i relationsriktningen en-till-flera för alla andra tabeller som är relaterade till DimSalesTerritory. Du kan också tillämpa ett filter som skyddar hela EmployeeSecurity-tabellen så att ingen användare som är medlem i rollen kan fråga på data i tabellen.  
  
> [!NOTE]  
> Rollen Säljpersonal per område som du skapar i den här lektionen begränsar medlemmarna så att de bara kan visa (eller fråga på) försäljningsdata för det försäljningsområde som de tillhör. Om du lägger till en användare som medlem i rollen Säljpersonal per område och användaren även är medlem i en roll som skapats i [Lektion 11: Skapa roller](../tutorials/aas-lesson-11-create-roles.md) får du en kombination av behörigheter. När en användare är medlem i flera roller ackumuleras behörigheterna och radfitren som definierats för varje roll. Det vill säga att användaren får högre behörighet som bestäms av kombinationen av rollerna.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Så här skapar du användarrollen Säljpersonal per område  
  
1.  Klicka på **Modell**-menyn i SSDT och klicka sedan på **Roller**.  
  
2.  Klicka på **Ny** i **rollhanteraren**.  
  
    En ny roll med behörigheten Ingen läggs till i listan.  
  
3.  Klicka på den nya rollen och ändra sedan namnet på rollen till **Säljpersonal per område** i kolumnen **Namn**.  
  
4.  Klicka på listrutan i kolumnen **Behörigheter** och välj sedan behörigheten **Läsa**.  
  
5.  Klicka på fliken **Medlemmar** och sedan på **Lägg till**.  
  
6.  I **Ange ett objektnamn du vill markera** i dialogrutan **Välj användare eller grupp** anger du det första exempelanvändarnamnet du använde när du skapade tabellen EmployeeSecurity. Klicka på **Kontrollera namn** för att verifiera att användarnamnet är giltigt och klicka sedan på **Ok**.  
  
    Upprepa det här steget för att lägga till de andra exempelanvändarnamnen du använde när du skapade tabellen EmployeeSecurity.  
  
7.  Klicka på fliken **Radfilter**.  
  
8.  Skriv följande formel i kolumnen **DAX-filter** i tabellen **EmployeeSecurity**:  
  
    ```
      =FALSE()  
    ```
  
    Den här formeln anger alla kolumner matchar det booleska värdet False (falskt). Medlemmar i rollen Säljpersonal per område kan inte fråga på någon av kolumnerna i tabellen EmployeeSecurity.  
  
9. Skriv följande formel för tabellen **DimSalesTerritory**:  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    I den här formeln returnerar funktionen LOOKUPVALUE alla värden för kolumnen DimEmployeeSecurity[SalesTerritoryId] där EmployeeSecurity[LoginId] är samma som det inloggade Windows-användarnamnet och EmployeeSecurity[SalesTerritoryId] är samma som DimSalesTerritory[SalesTerritoryId].  
  
    Den uppsättning försäljningsområdes-ID:n som returneras av LOOKUPVALUE används sedan för att begränsa vilka rader som visas i tabellen DimSalesTerritory. Endast de rader där SalesTerritoryID för raden finns i den uppsättning av ID:n som returneras av funktionen LOOKUPVALUE visas.  
  
10. Klicka på **Ok** i rollhanteraren.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Testa användarrollen Säljpersonal per område  
I den här uppgiften använder du funktionen Analysera i Excel i SSDT för att testa effektiviteten hos användarrollen Säljpersonal per område. Du anger något av de användarnamn som du har lagt till i tabellen EmployeeSecurity som medlem i rollen. Det här användarnamnet används sedan som gällande användarnamn i anslutningen som skapas mellan Excel och modellen.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Så här testar du användarrollen Säljpersonal per område  
  
1.  Klicka på **Modell**-menyn i SSDT och klicka sedan på **Analysera i Excel**.  
  
2.  Välj **Annan Windows-användare** i **Ange det användarnamn eller den roll som ska användas för att ansluta till modellen** i dialogrutan **Analysera i Excel** och klicka sedan på **Bläddra**.  
  
3.  I **Ange ett objektnamn du vill markera** i dialogrutan **Välj användare eller grupp** skriver du ett användarnamn som du har lagt till i tabellen EmployeeSecurity. Klicka sedan på **Kontrollera namn**.  
  
4.  Klicka på **Ok** för att stänga dialogrutan **Välj användare eller grupp** och klicka sedan på **Ok** för att stänga dialogrutan **Analysera i Excel**.  
  
    Excel öppnas med en ny arbetsbok. En pivottabell skapas automatiskt. Listan Pivottabellfält innehåller de flesta datafält som är tillgängliga i den nya modellen.  
  
    Observera att tabellen EmployeeSecurity inte visas i listan Pivottabellfält. Du valde att dölja den här tabellen för klientverktyg i en föregående aktivitet.  
  
5.  I **∑ Internet Sales** (mått) i listan **Fält** väljer du måttet **InternetTotalSales**. Måttet anges i fälten **Värden**.  
  
6.  Välj kolumnen **SalesTerritoryId** i tabellen **DimSalesTerritory**. Kolumnen anges i fälten **Radetiketter**.  
  
    Observera att Internetförsäljningssiffror endast visas för den region som det gällande användarnamnet (som du använde) tillhör. Om du väljer en annan kolumn, till exempel City (Ort) i tabellen DimGeography som radetikettfält, visas endast orter i försäljningsområdet som den gällande användaren tillhör.  
  
    Den här användaren kan inte visa eller fråga på Internetförsäljningsdata för andra områden än det som han eller hon tillhör. Den här begränsningen beror på att radfiltret som definierats för tabellen DimSalesTerritory i användarrollen Säljpersonal per område skyddar alla data för andra försäljningsområden.  
  
## <a name="see-also"></a>Se även  
[Funktionen USERNAME (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[Funktionen LOOKUPVALUE (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[Funktionen CUSTOMDATA (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  
