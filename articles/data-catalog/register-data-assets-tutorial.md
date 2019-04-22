---
title: Registrera datatillgångar i Azure Data Catalog
description: Så här registrerar du datatillgångar i Azure Data Catalog
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: tutorial
ms.date: 04/08/2019
ms.openlocfilehash: e89bd4806e2bb2369c100e948be51dcf32f2e123
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59362506"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Självstudier: Registrera datatillgångar i Azure Data Catalog

I den här självstudien använder du registreringsverktyget registrera datatillgångar från Azure SQL database-exempel i katalogen. Registreringen är den process där du extraherar viktiga strukturella metadata, t.ex. namn, typer och platser, från datakällan och dess tillgångar och kopierar dem till katalogen. Datakällan och datatillgångarna blir kvar där de är, men katalogen använder deras metadata för att göra det lättare att identifiera dem och förstå hur de kan användas.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Registrera datatillgångar 
> * Sök efter datatillgångar
> * Kommentera datatillgångar
> * Ansluta till datatillgångar
> * Hantera datatillgångar
> * Ta bort datatillgångar

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom igång genom att slutföra den [snabbstarten](register-data-assets-tutorial.md).

* En [Microsoft Azure](https://azure.microsoft.com/) prenumeration.
* Du måste ha en egen [Azure Active Directory-klient](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Om du vill konfigurera Data Catalog, måste du vara ägare eller Medägare av en Azure-prenumeration.

## <a name="register-data-assets"></a>Registrera datatillgångar

### <a name="register-a-data-source"></a>Registrera en datakälla

Registrerar du datatillgångar (tabeller) från en [Azure SQL database exemplet](../sql-database/sql-database-single-database-get-started.md), men du kan använda valfri datakälla som stöds om du föredrar att arbeta med data som är vanliga och relevanta för din roll. En lista över datakällor som stöds finns i [Datakällor som stöds](data-catalog-dsr.md).

Azure SQL-databasnamnet som vi använder i den här självstudien är *RLSTest*.

Nu kan du registrera datatillgångar från Azure SQL database-exempel med hjälp av Azure Data Catalog.

1. Gå till den [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och välj **publicera Data**.

   ![Azure Data Catalog – knappen Publicera data](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Välj **starta program** för att hämta, installera och köra registreringsverktyget på datorn.

   ![Azure Data Catalog – knappen Starta](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. På den **Välkommen** väljer **logga in** och ange dina autentiseringsuppgifter.

    ![Azure Data Catalog – välkomstsida](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. På den **Microsoft Azure Data Catalog** väljer **SQL Server** och **nästa**.

    ![Azure Data Catalog – datakällor](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Ange SQL Server-anslutningsegenskaperna för din Azure SQL database-exemplet och välj **CONNECT**.

   ![Azure Data Catalog – SQL Server-anslutningsinställningar](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Registrera metadata för din datatillgång. I det här exemplet registrerar du **produkten** objekt från Azure SQL database exempel namnområde:

    1. I den **Serverhierarki** konsolträdet, expandera din Azure SQL database-exemplet och välj **SalesLT**.

    2. Välj **produkten**, **ProductCategory**, **ProductDescription**, och **ProductModel** genom att använda Ctrl + Välj.

    3. Välj den **Flytta valda pilen** (**>**). Denna åtgärd flyttar alla markerade objekt till listan **Objekt som ska registreras**.

          ![Självstudiekurs om Azure Data Catalog – bläddra bland och välj objekt](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Välj **Inkludera förhandsgranskning** om du vill ta med en ögonblicksbild av data. Ögonblicksbilden innehåller upp till 20 poster från varje tabell och kopieras till katalogen.

    5. Välj **Inkludera dataprofil** om du vill ta med en ögonblicksbild av objektstatistiken för dataprofilen (till exempel lägsta, högsta och genomsnittliga värden för en kolumn, antalet rader osv.).

    6. I den **lägga till taggar** anger **försäljning, produkt, azure sql**. Denna åtgärd lägger till söktaggar för dessa datatillgångar. Taggar är ett bra sätt att hjälpa användarna att hitta en registrerad datakälla.

    7. Ange namnet på en **expert** här (valfritt).

          ![Självstudiekurs om Azure Data Catalog – objekt som ska registreras](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Välj **registrera**. De objekt som du har valt registreras i Azure Data Catalog. I den här övningen registreras de markerade objekten från din Azure SQL database-exemplet. Registreringsverktyget extraherar metadata från datatillgången och kopierar dessa data till Azure Data Catalog-tjänsten. Dina data blir kvar där för tillfället förblir den. Data förblir under kontrollen av administratörer och principer för ursprung-system.

          ![Azure Data Catalog – registrerade objekt](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Om du vill se din registrerade datakällobjekt **visa Portal**. Bekräfta att du ser alla fyra tabeller och databasen i rutnätsvyn på Azure Data Catalog-portalen (Kontrollera att sökfältet är tydligt).

        ![Objekt på Azure Data Catalog-portalen](media/register-data-assets-tutorial/data-catalog-view-portal.png)

I den här övningen registrerade du objekt från Azure SQL database-exempel så att de enkelt kan identifieras av användare i din organisation.

I nästa övning kommer du att lära dig att identifiera registrerade datatillgångar.

## <a name="discover-data-assets"></a>Identifiera datatillgångar

Azure Data Catalog-identifieringen använder två primära mekanismer: sökning och filtrering.

Sökningen har utformats att vara både intuitiv och kraftfull. Som standard matchas sökvillkor mot en egenskap i katalogen, inklusive kommentarer av användaren.

Filtreringen är avsedd att komplettera sökningen. Du kan välja specifika egenskaper som experter, typ av datakälla, objekttyp och taggar för att visa matchande datatillgångar och för att begränsa sökresultaten till matchande tillgångar.

Genom att använda en kombination av sökning och filtrering kan navigera du snabbt i datakällorna som är registrerade med Azure Data Catalog.

I den här övningen använder du Azure Data Catalog-portalen för att identifiera de datatillgångar som du registrerade i den föregående övningen. Mer information om söksyntaxen finns i [referens för söksyntax i Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

Följande är några få exempel på hur du kan identifiera datatillgångar i katalogen.  

### <a name="discover-data-assets-with-basic-search"></a>Identifiera datatillgångar med en enkel sökning

Du kan använda en enkel sökning för att söka igenom en katalog med hjälp av ett eller flera sökvillkor. Resultatet returnerar tillgångar där ett eller flera av de angivna villkoren matchar en egenskap.

1. Välj **Start** i Azure Data Catalog-portalen. Om du har stängt webbläsaren går du till den [startsidan för Azure Data Catalog](https://www.azuredatacatalog.com).

2. Skriv `product` i sökrutan och tryck på **Retur**.

    ![Azure Data Catalog – enkel textsökning](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Kontrollera att du ser alla fyra tabeller och databasen i resultaten. Du kan växla mellan **rutnätsvy** och **listvy** genom att välja knappar i verktygsfältet, som visas i följande bild. Observera att sökordet markeras i sökresultatet eftersom alternativet **Markera** har inställningen **På**. Du kan också ange antalet **resultat per sida** i sökresultatet.

    ![Azure Data Catalog – resultat från en enkel textsökning](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    Panelen **Sökningar** visas till vänster och panelen **Egenskaper** till höger. På panelen **Sökningar** kan du ändra sökvillkoren och filtrera resultatet. Panelen **Egenskaper** innehåller egenskaperna för ett valt objekt i listan eller rutnätet.

4. Välj **produkten** i sökresultatet. Välj den **förhandsversion**, **kolumner**, **Dataprofil**, och **dokumentation** tabbar och välj pilen för att expandera det nedre fönstret.  

    ![Azure Data Catalog – det nedre fönstret](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    På fliken **Förhandsgranska** ser du en förhandsgranskning av data i tabellen **Product**.  
5. Välj den **kolumner** fliken information om kolumner (till exempel **namn** och **datatypen**) i datatillgången.

6. Välj den **Dataprofil** fliken för att se dataprofilen (till exempel: antalet rader, storleken på data eller det minsta värdet i en kolumn) i datatillgången.

### <a name="discover-data-assets-with-property-scoping"></a>Identifiera datatillgångar med egenskapsomfång

Egenskapsomfång hjälper dig att identifiera datatillgångar där sökvillkoret matchar den angivna egenskapen.

1. Rensa **tabellfiltret** under **Objekttyp** i **Filter**.  

2. Skriv `tags:product` i sökrutan och tryck på **Retur**. En lista med alla egenskaper som du kan använda för att söka i datakatalogen finns i [Data Catalog Search-syntaxreferensen](/rest/api/datacatalog/#search-syntax-reference).

3. Kontrollera att du ser tabellerna och databasen i resultaten.  

    ![Data Catalog – resultat från sökning med egenskapsomfång](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Spara sökningen

1. I den **sökningar** fönstret i den **aktuell sökning** , ange ett namn för sökningen och väljer **spara**.

    ![Azure Data Catalog – spara sökning](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Bekräfta att den sparade sökningen visas under **Sparade sökningar**.

3. Välj en av de åtgärder som du kan utföra med den sparade sökningen (**Byt namn**, **Ta bort** eller **Spara som standard**).

### <a name="grouping-with-parentheses"></a>Gruppera med parenteser

Genom att gruppera med parenteser kan du gruppera delar av frågan för att skapa en logisk isolering, särskilt tillsammans med booleska operatorer.

1. Skriv `name:product AND (tags:product AND objectType:table)` i sökrutan och tryck på **Retur**.

2. Kontrollera att endast tabellen **Product** visas i sökresultatet.

    ![Azure Data Catalog – gruppera sökning](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Jämförelseoperatorer

Med jämförelseoperatorer kan du använda andra jämförelser än ”lika med” för egenskaper som har numeriska datatyper och datum.

1. Skriv `lastRegisteredTime:>"06/09/2016"` i sökrutan.

2. Rensa **tabellfiltret** under **Objekttyp**.

3. Tryck på **RETUR**.

4. Kontrollera att du ser den **produkten**, **ProductCategory**, och **ProductDescription** tabeller och Azure SQL-databasen som du registrerade i sökresultat.

    ![Azure Data Catalog – resultat från en jämförelsesökning](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Se [identifiera datatillgångar](data-catalog-how-to-discover.md) detaljerad information om hur du identifierar datatillgångar. Mer information om Söksyntaxen finns i [söksyntax för Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Kommentera datatillgångar

I den här övningen ska du använda Azure Data Catalog-portalen för att kommentera (lägga till information som beskrivningar, taggar eller experter) befintliga datatillgångar i katalogen. Dina kommentarer kompletterar strukturella metadata som extraherats från datakällan under registreringen. Anteckningens gör datatillgångarna mycket lättare att identifiera och förstå.

I den här övningen kommenterar du en enskild datatillgång (ProductPhoto). Du lägger till ett eget namn och en beskrivning till ProductPhoto-datatillgången.  

1. Gå till den [startsidan för Azure Data Catalog](https://www.azuredatacatalog.com) och Sök med `tags:product` att hitta de dataresurser som du har registrerat.

2. Välj **ProductModel** i sökresultaten.  

3. Skriv **Product images** som **Eget namn** och **Product photos for marketing materials** som **Beskrivning**.

    ![Azure Data Catalog – beskrivning för ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    **Beskrivningen** hjälper andra att identifiera och förstå varför och hur den valda datatillgången ska användas. Du kan också lägga till fler taggar och visningskolumner. Du kan söka och filtrera datakällor med beskrivande metadata som du har lagt till katalogen.

Du kan också göra följande steg på den här sidan:

* Lägg till experter för datatillgången. Välj **Lägg till** i den **experter** området.

* Lägg till taggar på datauppsättningsnivå. Välj **Lägg till** i den **taggar** området. En tagg kan vara en användartagg eller en ordlistetagg. Standard Edition av Data Catalog innehåller en företagsordlista som hjälper katalogadministratörer att definiera en central taxonomi för företaget. Katalogens användare kan sedan lägga till anteckningar i datatillgångar med termer från ordlistan. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md)

* Lägg till taggar på kolumnnivå. Välj **Lägg till** under **taggar** för den kolumn som du vill kommentera.

* Lägg till en beskrivning på kolumnnivå. Skriv en **beskrivning** för en kolumn. Du kan också visa beskrivningens metadata som extraherats från datakällan.

* Lägg till information för **Begär åtkomst** som visar användare hur de ska begära åtkomst till datatillgången.
  
* Välj fliken **Dokumentation** och ange dokumentation för datatillgången. Med Azure Data Catalog-dokumentation kan du använda datakatalogen som en innehållsdatabas med komplett information om dina datatillgångar.
  
Du kan också lägga till en anteckning till flera datatillgångar. Du kan till exempel välja alla datatillgångar som du har registrerat och ange en expert för dem.

![Azure Data Catalog – kommentera flera datatillgångar](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog stöder kommentarer via crowdsourcing. Alla Data Catalog-användare kan lägga till taggar (användare eller ordlista), beskrivningar och andra metadata. Då kan användarna lägga till perspektiv på en datatillgång och dess användning och dela det perspektivet med andra användare.

Detaljerad information om hur du kommenterar datatillgångar finns i [Kommentera datatillgångar](data-catalog-how-to-annotate.md).

## <a name="connect-to-data-assets"></a>Ansluta till datatillgångar

I den här övningen öppnar du datatillgångar i ett integrerat klientverktyg (Excel) och ett icke-integrerat verktyg (SQL Server Management Studio) med hjälp av anslutningsinformation.

> [!NOTE]
> Det är viktigt att komma ihåg att Azure Data Catalog inte ger åtkomst till själva datakällan. Tjänsten gör det bara enklare för dig att identifiera och förstår den. När du ansluter till en datakälla så använder det klientprogram som du väljer dina Windows-autentiseringsuppgifter eller uppmanar dig att ange dina autentiseringsuppgifter om det behövs. Om du inte tidigare har beviljats åtkomst till datakällan måste du beviljas åtkomst innan du kan ansluta.

### <a name="connect-to-a-data-asset-from-excel"></a>Ansluta till en datatillgång från Excel

1. Välj **Product** i sökresultatet. Välj **öppna i** i verktygsfältet och väljer **Excel**.

    ![Azure Data Catalog – anslut till datatillgång](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Välj **öppna** i popup-fönstret för nedladdningen. Hur detta ser ut kan variera något beroende på webbläsaren.

3. I den **Microsoft Excel-säkerhetsmeddelande** väljer **aktivera**.

    ![Azure Data Catalog – popup-fönster med säkerhetsmeddelande i Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Behåll standardinställningarna den **importdata** dialogrutan och välj **OK**.

    ![Azure Data Catalog – importera data i Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Visa datakällan i Excel.

    ![Azure Data Catalog – produkttabell i Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

I den här övningen anslöt du till datatillgångar som identifierades med hjälp av Azure Data Catalog. På Azure Data Catalog-portalen kan du ansluta direkt med hjälp av klientprogrammen som är integrerade i **Öppna i**-menyn. Du kan också ansluta med valfria program som du väljer med hjälp av informationen om anslutningsplatsen som finns i datatillgångarnas metadata. Du kan till exempel använda SQL Server Management Studio för att ansluta till Azure SQL-databas för att komma åt data i datatillgångarna som registrerats i den här självstudien.

1. Öppna **SQL Server Management Studio**.

2. I dialogrutan **Anslut till server** anger du servernamnet från rutan **Egenskaper** på Azure Data Catalog-portalen.

3. Använd lämplig autentisering och relevanta autentiseringsuppgifter för åtkomst till datatillgången. Om du inte har åtkomst använder du informationen i fältet **Begär åtkomst** för att få det.

    ![Azure Data Catalog – begär åtkomst](media/register-data-assets-tutorial/data-catalog-request-access.png)

Välj **visa anslutningssträngar** att visa och kopiera ADO.NET-, ODBC- och OLEDB-anslutningssträngar till Urklipp för användning i dina program.

## <a name="manage-data-assets"></a>Hantera datatillgångar

I det här steget konfigurerar du säkerheten för dina datatillgångar. Data Catalog ger inte användarna åtkomst till själva informationen. Datakällans ägare styr dataåtkomsten.

Med Data Catalog kan du identifiera datakällor och visa metadata relaterade till de datakällor som registrerats i katalogen. Det kan dock finnas situationer då datakällor endast bör visas för särskilda användare eller för medlemmar i vissa grupper. Dessa scenarier kan du använda Data Catalog för att bli ägare av registrerade datatillgångar och styra visningen av de resurser som du äger.

> [!NOTE]
> De hanteringsfunktioner som beskrivs i den här övningen är bara tillgängliga i Standard Edition av Azure Data Catalog, inte i den kostnadsfria versionen.
> I Azure Data Catalog kan du överta ägarskapet för datatillgångar, lägga till medägare till datatillgångar och ange datatillgångars synlighet.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Bli ägare av datatillgångar och begränsa synligheten

1. Gå till [startsidan för Azure Data Catalog](https://www.azuredatacatalog.com). I **sökrutan** skriver du `tags:cycles` och trycker på **Retur**.

2. Markera ett objekt i resultatlistan och välj **bli ägare** i verktygsfältet.

3. I den **Management** delen av den **egenskaper** fönstret väljer **bli ägare**.

    ![Azure Data Catalog – bli ägare](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Om du vill begränsa synligheten väljer **ägare och dessa användare** i den **synlighet** och väljer **Lägg till**. Ange användarnas e-postadresser i textrutan och tryck på **Retur**.

    ![Azure Data Catalog – begränsa åtkomst](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Ta bort datatillgångar

I den här övningen tar du bort förhandsgranskningsdata från registrerade datatillgångar samt datatillgångar från katalogen med hjälp av Azure Data Catalog-portalen.

I Azure Data Catalog kan du ta bort enstaka eller flera tillgångar.

1. Gå till [startsidan för Azure Data Catalog](https://www.azuredatacatalog.com).

2. I den **Search** text anger `tags:cycles` och välj **RETUR**.

3. Markera ett objekt i resultatlistan och välj **ta bort** i verktygsfältet, som visas i följande bild:

    ![Azure Data Catalog – ta bort rutnätsobjekt](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Om du använder listvyn är kryssrutan till vänster om objektet, som visas i följande bild:

    ![Azure Data Catalog – ta bort objekt i lista](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Du kan också markera flera datatillgångar och ta bort dem samtidigt, som du ser i följande bild:

    ![Azure Data Catalog – ta bort flera datatillgångar](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Som standard tillåter katalogen att alla användare registrerar valfri datakälla och att alla användare tar bort alla datatillgångar som har registrerats. Hanteringsfunktionerna i standardutgåvan av Azure Data Catalog tillhandahåller ytterligare alternativ för ägarskap av tillgångar samt för att begränsa vem som kan identifiera och ta bort tillgångar.

## <a name="summary"></a>Sammanfattning

I den här självstudiekursen har du utforskat grundläggande funktioner i Azure Data Catalog, till exempel registrering, kommentarer, identifiering och hantering av datatillgångar på ett företag. Nu har du slutfört självstudiekursen, så det är dags att komma igång. Du kan börja i dag genom att registrera de datakällor som du och ditt team använder och bjuda in kollegor att använda katalogen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Datakällor som stöds](data-catalog-dsr.md)