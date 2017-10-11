---
title: "Komma igång med Data Catalog | Microsoft Docs"
description: "Självstudiekurs med en presentation av scenarier och funktioner i Azure Data Catalog."
documentationcenter: 
services: data-catalog
author: steelanddata
manager: jhubbard
editor: 
tags: 
ms.assetid: 03332872-8d84-44a0-8a78-04fd30e14b18
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/03/2017
ms.author: spelluru
ms.openlocfilehash: 5a3445aee7722579405b67830ca49ef8c0b29d0e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-azure-data-catalog"></a>Kom igång med Azure Data Catalog
Azure Data Catalog är en helt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för datatillgångar på ett företag. En detaljerad översikt finns i [Vad är Azure Data Catalog?](data-catalog-what-is-data-catalog.md)

Den här självstudiekursen hjälper dig att komma igång med Azure Data Catalog. I de här självstudierna går du igenom följande steg:

| Procedur | Beskrivning |
|:--- |:--- |
| [Etablera en datakatalog](#provision-data-catalog) |I det här steget etablerar eller konfigurerar du Azure Data Catalog. Du utför endast det här steget om katalogen inte redan har konfigurerats. Du kan bara ha en datakatalog per organisation (Microsoft Azure Active Directory-domän) även om flera prenumerationer är kopplade till kontot. |
| [Registrera datatillgångar](#register-data-assets) |I det här steget registrerar du datatillgångar från AdventureWorks2014-exempeldatabasen i datakatalogen. Registreringen är den process där du extraherar viktiga strukturella metadata, t.ex. namn, typer och platser, från datakällan och kopierar dem till katalogen. Datakällan och datatillgångarna blir kvar där de är, men katalogen använder deras metadata för att göra det lättare att identifiera dem och förstå hur de kan användas. |
| [Identifiera datatillgångar](#discover-data-assets) |I det här steget använder du Azure Data Catalog-portalen för att identifiera datatillgångar som registrerades i det föregående steget. När en datakälla har registrerats med Azure Data Catalog indexeras dess metadata av tjänsten så att användarna enkelt kan söka efter de data som de behöver. |
| [Kommentera datatillgångar](#annotate-data-assets) |I den här proceduren anger du anteckningar (information, till exempel beskrivningar, taggar, dokumentation eller experter) för datatillgångarna. Denna information kompletterar metadata som extraherats från datakällan och gör datakällan lättare att förstå för fler personer. |
| [Ansluta till datatillgångar](#connect-to-data-assets) |I det här steget öppnar du datatillgångar i integrerade klientverktyg (t.ex. Excel och SQL Server Data Tools) och ett icke-integrerat verktyg (SQL Server Management Studio). |
| [Hantera datatillgångar](#manage-data-assets) |I det här steget konfigurerar du säkerheten för dina datatillgångar. Data Catalog ger inte användare åtkomst till själva informationen. Datakällans ägare styr dataåtkomsten. <br/><br/> Med Data Catalog kan du identifiera datakällor och visa **metadata** relaterade till datakällor som registrerats i katalogen. Det kan dock finnas situationer då datakällor endast bör visas för särskilda användare eller för medlemmar i vissa grupper. I dessa scenarier kan du använda Data Catalog för att bli ägare av registrerade datatillgångar i katalogen och styra visningen av de resurser som du äger. |
| [Ta bort datatillgångar](#remove-data-assets) |I det här steget lär du dig hur du tar bort datatillgångar från datakatalogen. |

## <a name="tutorial-prerequisites"></a>Förutsättningar för självstudiekursen
### <a name="azure-subscription"></a>Azure-prenumeration
Du måste vara ägare eller medägare av en Azure-prenumeration för att kunna konfigurera Azure Data Catalog.

Med en Azure-prenumeration kan du styra åtkomsten till resurser i molntjänsten som Azure Data Catalog, samt styra hur resursanvändningen rapporteras, faktureras och betalas. Olika prenumerationer kan ha olika fakturerings- och betalningskonfiguration, vilket betyder att du kan ha olika faktureringsplaner beroende på avdelning, projekt, lokalkontor och så vidare. Varje molntjänst tillhör en prenumeration och du måste ha en prenumeration innan du konfigurerar Azure Data Catalog. Mer information finns i [Hantera konton, prenumerationer och administrativa roller](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Mer information finns i [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

### <a name="azure-active-directory"></a>Azure Active Directory
Du måste vara inloggad med ett Azure AD-användarkonto (Azure Active Directory) för att kunna konfigurera Azure Data Catalog. Du måste vara ägaren eller medägaren av en Azure-prenumeration.  

Azure AD tillhandahåller ett enkelt sätt för ditt företag att hantera identitet och åtkomst, både i molnet och lokalt. Du kan använda ett arbets- eller skolkonto för att logga in i valfri webbapp i molnet eller den lokala infrastrukturen. Azure Data Catalog använder Azure AD för att autentisera inloggningen. Mer information finns i [Vad är Azure Active Directory?](../active-directory/active-directory-whatis.md)

### <a name="azure-active-directory-policy-configuration"></a>Konfiguration av Azure Active Directory-principer
Det kan uppstå en situation då du kan logga in på Azure Data Catalog-portalen, men då du får ett felmeddelande när du försöker logga in till registreringsverktyget för datakällan. Detta fel kan inträffa när du är ansluten till företagets nätverk eller när du ansluter utanför företagsnätverket.

Registreringsverktyget använder *formulärautentisering* för att validera användarinloggningar mot Azure Active Directory. För att inloggningen ska lyckas måste en Azure Active Directory-administratör aktivera formulärautentisering i *den globala autentiseringsprincipen*.

Med den globala autentiseringsprincipen kan du aktivera autentisering separat för intranät- och extranätsanslutningar, som du ser i följande bild. Inloggningsfel kan uppstå om formulärautentisering inte är aktiverat för det nätverk som du ansluter från.

 ![Global Azure Active Directory-autentiseringsprincip](./media/data-catalog-prerequisites/global-auth-policy.png)

Mer information finns i [Konfigurera autentiseringsprinciper](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Etablera en datakatalog
Du kan bara etablera en datakatalog per organisation (Azure Active Directory-domän). Om ägaren eller medägaren av en Azure-prenumeration som tillhör den här Azure Active Directory-domänen redan har skapat en katalog kan du därför inte skapa en katalog igen, även om du har flera Azure-prenumerationer. Du kan testa om en katalog har skapats av en användare i din Azure Active Directory-domän genom att gå till [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och kontrollera om du ser katalogen. Om en katalog redan har skapats för dig hoppar du över följande procedur och går till nästa avsnitt.    

1. Gå till [sidan för Data Catalog-tjänsten](https://azure.microsoft.com/services/data-catalog) och klicka på **Kom igång**.
   
    ![Azure Data Catalog – landningssida för marknadsföring](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Logga in med ett användarkonto som är ägare eller medägare av en Azure-prenumeration. Följande sida visas när du har loggat in.
   
    ![Azure Data Catalog – etablering av datakatalog](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Ange ett **namn** för datakatalogen, den **prenumeration** som du vill använda och **platsen** för katalogen.
4. Expandera **Priser** och välj en **version** (kostnadsfri eller Standard) för Azure Data Catalog.
    ![Azure Data Catalog – välj version](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Expandera **Kataloganvändare** och klicka på **Lägg till** för att lägga till användare för datakatalogen. Du läggs automatiskt till i den här gruppen.
    ![Azure Data Catalog – användare](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Expandera **Katalogadministratörer** och klicka på **Lägg till** för att lägga till ytterligare administratörer för datakatalogen. Du läggs automatiskt till i den här gruppen.
    ![Azure Data Catalog – administratörer](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Klicka på **Skapa katalog** för att skapa datakatalogen för din organisation. Startsidan för datakatalogen visas när katalogen har skapats.
    ![Azure Data Catalog – skapa katalogen](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Hitta en datakatalog på Azure-portalen
1. Öppna en separat flik i webbläsaren eller ett separat webbläsarfönster, gå till [Azure-portalen](https://portal.azure.com) och logga in med samma konto som du använde för att skapa datakatalogen i föregående steg.
2. Välj **Bläddra** och klicka sedan på **Datakatalog**.
   
    ![Azure Data Catalog – bläddra i Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) Datakatalogen som du har skapat visas.
   
    ![Azure Data Catalog – visa katalog i lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
3. Klicka på den katalog som du skapade. Bladet **Datakatalog** visas på portalen.
   
   ![Azure Data Catalog – blad på portalen ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
4. Du kan visa egenskaperna för datakatalogen och uppdatera dem. Klicka till exempel **Prisnivå** och ändra versionen.
   
    ![Azure Data Catalog – prisnivå](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Exempeldatabas för Adventure Works
I de här självstudierna registrerar du datatillgångar (tabeller) från AdventureWorks2014-exempeldatabasen för SQL Server Database Engine, men du kan använda valfri datakälla som stöds om du föredrar att arbeta med data som är vanliga och relevanta för din roll. En lista över datakällor som stöds finns i [Datakällor som stöds](data-catalog-dsr.md).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Installera Adventure Works 2014 OLTP-databasen
Adventure Works-databasen stöder standardscenarier för webbaserad transaktionsbearbetning för en fiktiv cykeltillverkare (Adventure Works Cycles) som omfattar produkter, försäljning och inköp. I de här självstudierna registrerar du information om produkter i Azure Data Catalog.

Så här installerar du Adventure Works-exempeldatabasen:

1. Ladda ned [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) på CodePlex.
2. Om du vill återställa databasen på din dator följer du anvisningarna i [Återställa en säkerhetskopia av databasen med hjälp av SQL Server Management Studio](http://msdn.microsoft.com/library/ms177429.aspx). Du kan också följa dessa steg:
   1. Öppna SQL Server Management Studio och anslut till SQL Server Database Engine.
   2. Högerklicka på **Databaser** och klicka på **Återställ databas**.
   3. Under **Återställ databas** klickar du på alternativet **Enhet** för **Källa** och klickar på **Bläddra**.
   4. Under **Select backup devices** (Välj säkerhetskopieringsenheter) klickar du på **Lägg till**.
   5. Gå till mappen som innehåller filen **AdventureWorks2014.bak**, markera filen och klicka på **OK** för att stänga dialogrutan **Locate Backup File** (Leta upp säkerhetskopia).
   6. Stäng dialogrutan **Select backup devices** (Välj säkerhetskopieringsenheter) genom att klicka på **OK**.    
   7. Stäng dialogrutan **Restore Database** (Återställ databas) genom att klicka på **OK**.

Nu kan du registrera datatillgångar från Adventure Works-exempeldatabasen med hjälp av Azure Data Catalog.

## <a name="register-data-assets"></a>Registrera datatillgångar
I den här övningen använder du registreringsverktyget för att registrera datatillgångar från Adventure Works-databasen i katalogen. Registreringen är den process där du extraherar viktiga strukturella metadata, t.ex. namn, typer och platser, från datakällan och dess tillgångar och kopierar dem till katalogen. Datakällan och datatillgångarna blir kvar där de är, men katalogen använder deras metadata för att göra det lättare att identifiera dem och förstå hur de kan användas.

### <a name="register-a-data-source"></a>Registrera en datakälla
1. Gå till [startsidan för Azure Data Catalog](http://azuredatacatalog.com) och klicka på **Publicera data**.
   
   ![Azure Data Catalog – knappen Publicera data](media/data-catalog-get-started/data-catalog-publish-data.png)
2. Klicka på **Starta program** för att ladda ned, installera och köra registreringsverktyget på datorn.
   
   ![Azure Data Catalog – knappen Starta](media/data-catalog-get-started/data-catalog-launch-application.png)
3. På **välkomstsidan** klickar du på **Logga in** och anger dina inloggningsuppgifter.     
   
    ![Azure Data Catalog – välkomstsida](media/data-catalog-get-started/data-catalog-welcome-dialog.png)
4. På sidan **Microsoft Azure Data Catalog** klickar du på **SQL Server** och sedan på **Nästa**.
   
    ![Azure Data Catalog – datakällor](media/data-catalog-get-started/data-catalog-data-sources.png)
5. Ange SQL Server-anslutningsegenskaperna för **AdventureWorks2014** (se följande exempel) och klicka på **Anslut**.
   
   ![Azure Data Catalog – SQL Server-anslutningsinställningar](media/data-catalog-get-started/data-catalog-sql-server-connection.png)
6. Registrera metadata för din datatillgång. I det här exemplet ska du registrera **produktions-/produktobjekt** från produktionsnamnområdet för AdventureWorks:
   
   1. I trädet **Serverhierarki** expanderar du **AdventureWorks2014** och klickar på **Production**.
   2. Markera **Product**, **ProductCategory**, **ProductDescription** och **ProductPhoto** genom att trycka på Ctrl samtidigt som du klickar.
   3. Klicka på **pilen för att flytta markerade objekt** (**>**). Denna åtgärd flyttar alla markerade objekt till listan **Objekt som ska registreras**.
      
      ![Självstudiekurs om Azure Data Catalog – bläddra bland och välj objekt](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
   4. Välj **Inkludera förhandsgranskning** om du vill ta med en ögonblicksbild av data. Ögonblicksbilden innehåller upp till 20 poster från varje tabell och kopieras till katalogen.
   5. Välj **Inkludera dataprofil** om du vill ta med en ögonblicksbild av objektstatistiken för dataprofilen (till exempel lägsta, högsta och genomsnittliga värden för en kolumn, antalet rader osv.).
   6. I fältet **Lägg till taggar** skriver du **adventure works, cycles**. Denna åtgärd lägger till söktaggar för dessa datatillgångar. Taggar är ett bra sätt att hjälpa användarna att hitta en registrerad datakälla.
   7. Ange namnet på en **expert** här (valfritt).
      
      ![Självstudiekurs om Azure Data Catalog – objekt som ska registreras](media/data-catalog-get-started/data-catalog-objects-register.png)
   8. Klicka på **REGISTRERA**. De objekt som du har valt registreras i Azure Data Catalog. I den här övningen registreras de markerade objekten från Adventure Works. Registreringsverktyget extraherar metadata från datatillgången och kopierar dessa data till Azure Data Catalog-tjänsten. Informationen ligger kvar där den är och är under kontroll av det aktuella systemets administratörer och principer.
      
      ![Azure Data Catalog – registrerade objekt](media/data-catalog-get-started/data-catalog-registered-objects.png)
   9. Klicka på **Visa portal** om du vill visa registrerade datakällobjekt. Kontrollera att du ser alla fyra tabeller och databasen i rutnätsvyn på Azure Data Catalog-portalen.
      
      ![Objekt på Azure Data Catalog-portalen ](media/data-catalog-get-started/data-catalog-view-portal.png)

I den här övningen registrerade du objekt från exempeldatabasen för Adventure Works så att de enkelt kan identifieras av användare i organisationen. I nästa övning kommer du att lära dig att identifiera registrerade datatillgångar.

## <a name="discover-data-assets"></a>Identifiera datatillgångar
Azure Data Catalog-identifieringen använder två primära mekanismer: sökning och filtrering.

Sökningen har utformats att vara både intuitiv och kraftfull. Som standard matchas sökvillkor mot en egenskap i katalogen, inklusive kommentarer av användaren.

Filtreringen är avsedd att komplettera sökningen. Du kan välja specifika egenskaper som experter, typ av datakälla, objekttyp och taggar för att visa matchande datatillgångar och för att begränsa sökresultaten till matchande tillgångar.

Genom att kombinera sökning och filtrering kan du snabbt navigera i datakällorna som har registrerats i Azure Data Catalog för att identifiera de datatillgångar som du behöver.

I den här övningen använder du Azure Data Catalog-portalen för att identifiera de datatillgångar som du registrerade i den föregående övningen. Mer information om söksyntaxen finns i [referens för söksyntax i Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).

Följande är några få exempel på hur du kan identifiera datatillgångar i katalogen.  

### <a name="discover-data-assets-with-basic-search"></a>Identifiera datatillgångar med en enkel sökning
Du kan använda en enkel sökning för att söka igenom en katalog med hjälp av ett eller flera sökvillkor. Resultatet returnerar tillgångar där ett eller flera av de angivna villkoren matchar en egenskap.

1. Klicka på **Start** på Azure Data Catalog-portalen. Om du har stängt webbläsaren går du till [startsidan för Azure Data Catalog](https://www.azuredatacatalog.com).
2. Skriv `cycles` i sökrutan och tryck på **Retur**.
   
    ![Azure Data Catalog – enkel textsökning](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Kontrollera att alla fyra tabeller och databasen (AdventureWorks2014) visas i resultatet. Du kan växla mellan **rutnätsvy** och **listvy** genom att klicka på knappar i verktygsfältet, som du ser i följande bild. Observera att sökordet markeras i sökresultatet eftersom alternativet **Markera** har inställningen **På**. Du kan också ange antalet **resultat per sida** i sökresultatet.
   
    ![Azure Data Catalog – resultat från en enkel textsökning](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
   
    Panelen **Sökningar** visas till vänster och panelen **Egenskaper** till höger. På panelen **Sökningar** kan du ändra sökvillkoren och filtrera resultatet. Panelen **Egenskaper** innehåller egenskaperna för ett valt objekt i listan eller rutnätet.
4. Klicka på **Product** i sökresultatet. Klicka på flikarna **Förhandsgranska**, **Kolumner**, **Dataprofil** och **Dokumentation** eller klicka på pilen för att expandera det nedre fönstret.  
   
    ![Azure Data Catalog – det nedre fönstret](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
   
    På fliken **Förhandsgranska** ser du en förhandsgranskning av data i tabellen **Product**.  
5. Klicka på fliken **Kolumner** om du vill visa information om kolumner (t.ex. **namn** och **datatyp**) i datatillgången.
6. Klicka på fliken **Dataprofil** om du vill visa dataprofilen (t.ex. antalet rader, storleken på data eller det minsta värdet i en kolumn) i datatillgången.
7. Filtrera resultatet med hjälp av **Filter** till vänster. Klicka till exempel på **Tabell** för **Objekttyp** så ser du bara de fyra tabellerna, inte databasen.
   
    ![Azure Data Catalog – filtrera sökresultat](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Identifiera datatillgångar med egenskapsomfång
Egenskapsomfång hjälper dig att identifiera datatillgångar där sökvillkoret matchar den angivna egenskapen.

1. Rensa **tabellfiltret** under **Objekttyp** i **Filter**.  
2. Skriv `tags:cycles` i sökrutan och tryck på **Retur**. En lista med alla egenskaper som du kan använda för att söka i datakatalogen finns i [Data Catalog Search-syntaxreferensen](https://msdn.microsoft.com/library/azure/mt267594.aspx).
3. Kontrollera att alla fyra tabeller och databasen (AdventureWorks2014) visas i resultatet.  
   
    ![Data Catalog – resultat från sökning med egenskapsomfång](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Spara sökningen
1. I rutan **Sökningar** i avsnittet **Aktuell sökning** skriver du ett namn för sökningen och klickar på **Spara**.
   
    ![Azure Data Catalog – spara sökning](media/data-catalog-get-started/data-catalog-save-search.png)
2. Bekräfta att den sparade sökningen visas under **Sparade sökningar**.
   
    ![Azure Data Catalog – sparade sökningar](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Välj en av de åtgärder som du kan utföra med den sparade sökningen (**Byt namn**, **Ta bort** eller **Spara som standard**).
   
    ![Azure Data Catalog – alternativ för sparade sökningar](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Booleska operatorer
Du kan utöka eller begränsa sökningen med booleska operatorer.

1. Skriv `tags:cycles AND objectType:table` i sökrutan och tryck på **Retur**.
2. Kontrollera att endast tabeller (inte databasen) visas i resultatet.  
   
    ![Azure Data Catalog – boolesk operator i sökning](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Gruppera med parenteser
Genom att gruppera med parenteser kan du gruppera delar av frågan för att skapa en logisk isolering, särskilt tillsammans med booleska operatorer.

1. Skriv `name:product AND (tags:cycles AND objectType:table)` i sökrutan och tryck på **Retur**.
2. Kontrollera att endast tabellen **Product** visas i sökresultatet.
   
    ![Azure Data Catalog – gruppera sökning](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Jämförelseoperatorer
Med jämförelseoperatorer kan du använda andra jämförelser än ”lika med” för egenskaper som har numeriska datatyper och datum.

1. Skriv `lastRegisteredTime:>"06/09/2016"` i sökrutan.
2. Rensa **tabellfiltret** under **Objekttyp**.
3. Tryck på **RETUR**.
4. Kontrollera att du ser tabellerna **Product**, **ProductCategory**, **ProductDescription** and **ProductPhoto** och AdventureWorks2014-databasen som du registrerade i sökresultatet.
   
    ![Azure Data Catalog – resultat från en jämförelsesökning](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Detaljerad information om hur du identifierar datatillgångar finns i [Identifiera datatillgångar](data-catalog-how-to-discover.md) och söksyntaxen finns i [Referens för söksyntaxen i Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="annotate-data-assets"></a>Kommentera datatillgångar
I den här övningen ska du använda Azure Data Catalog-portalen för att kommentera (lägga till information som beskrivningar, taggar eller experter) datatillgångar som du tidigare har registrerat i katalogen. Dina kommentarer kompletterar och förbättrar strukturella metadata som extraherats från datakällan under registreringen och gör datatillgångarna mycket lättare att identifiera och förstå.

I den här övningen kommenterar du en enskild datatillgång (ProductPhoto). Du lägger till ett eget namn och en beskrivning till ProductPhoto-datatillgången.  

1. Gå till [startsidan för Azure Data Catalog](https://www.azuredatacatalog.com) och sök med `tags:cycles` för att hitta de dataresurser som du har registrerat.  
2. Klicka på **ProductPhoto** i sökresultatet.  
3. Skriv **Product images** som **Eget namn** och **Product photos for marketing materials** som **Beskrivning**.
   
    ![Azure Data Catalog – beskrivning för ProductPhoto](media/data-catalog-get-started/data-catalog-productphoto-description.png)
   
    **Beskrivningen** hjälper andra att identifiera och förstå varför och hur den valda datatillgången ska användas. Du kan också lägga till fler taggar och visningskolumner. Nu kan du testa att söka och filtrera för att identifiera datatillgångar med de beskrivande metadata som du har lagt till i katalogen.

Du kan göra mer på den här sidan:

* Lägg till experter för datatillgången. Klicka på **Lägg till** i området **Experter**.
* Lägg till taggar på datauppsättningsnivå. Klicka på **Lägg till** i området **Taggar**. En tagg kan vara en användartagg eller en ordlistetagg. Standard Edition av Data Catalog innehåller en företagsordlista som hjälper katalogadministratörer att definiera en central taxonomi för företaget. Katalogens användare kan sedan lägga till anteckningar i datatillgångar med termer från ordlistan. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md)
* Lägg till taggar på kolumnnivå. Klicka på **Lägg till** under **Taggar** för den kolumn som du vill kommentera.
* Lägg till en beskrivning på kolumnnivå. Skriv en **beskrivning** för en kolumn. Du kan också visa beskrivningens metadata som extraherats från datakällan.
* Lägg till information för **Begär åtkomst** som visar användare hur de ska begära åtkomst till datatillgången.
  
    ![Azure Data Catalog – lägg till taggar, beskrivningar](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
* Välj fliken **Dokumentation** och ange dokumentation för datatillgången. Med Azure Data Catalog-dokumentation kan du använda datakatalogen som en innehållsdatabas med komplett information om dina datatillgångar.
  
    ![Azure Data Catalog – fliken Dokumentation](media/data-catalog-get-started/data-catalog-documentation.png)

Du kan också lägga till en anteckning till flera datatillgångar. Du kan till exempel välja alla datatillgångar som du har registrerat och ange en expert för dem.

![Azure Data Catalog – kommentera flera datatillgångar](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Azure Data Catalog stöder kommentarer via crowdsourcing. Alla Data Catalog-användare kan lägga till taggar (användare eller ordlista), beskrivningar och andra metadata, så att alla användare som har ett visst perspektiv av en datatillgång och dess användning kan fånga det perspektivet och göra det tillgängligt för andra användare.

Detaljerad information om hur du kommenterar datatillgångar finns i [Kommentera datatillgångar](data-catalog-how-to-annotate.md).

## <a name="connect-to-data-assets"></a>Ansluta till datatillgångar
I den här övningen öppnar du datatillgångar i ett integrerat klientverktyg (Excel) och ett icke-integrerat verktyg (SQL Server Management Studio) med hjälp av anslutningsinformation.

> [!NOTE]
> Det är viktigt att komma ihåg att Azure Data Catalog inte ger åtkomst till själva datakällan. Tjänsten gör det bara enklare för dig att identifiera och förstår den. När du ansluter till en datakälla så använder det klientprogram som du väljer dina Windows-autentiseringsuppgifter eller uppmanar dig att ange dina autentiseringsuppgifter om det behövs. Om du inte tidigare har beviljats åtkomst till datakällan måste du beviljas åtkomst innan du kan ansluta.
> 
> 

### <a name="connect-to-a-data-asset-from-excel"></a>Ansluta till en datatillgång från Excel
1. Välj **Product** i sökresultatet. Klicka på **Öppna i** i verktygsfältet och klicka sedan på **Excel**.
   
    ![Azure Data Catalog – anslut till datatillgång](media/data-catalog-get-started/data-catalog-connect1.png)
2. Klicka på **Öppna** i popup-fönstret för nedladdningar. Hur detta ser ut kan variera något beroende på webbläsaren.
   
    ![Azure Data Catalog – nedladdad Excel-anslutningsfil](media/data-catalog-get-started/data-catalog-download-open.png)
3. I fönstret **Säkerhetsmeddelande om Microsoft Excel** klickar du på **Aktivera**.
   
    ![Azure Data Catalog – popup-fönster med säkerhetsmeddelande i Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Lämna standardvärdena i dialogrutan **Importera data** och klicka på **OK**.
   
    ![Azure Data Catalog – importera data i Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Visa datakällan i Excel.
   
    ![Azure Data Catalog – produkttabell i Excel](media/data-catalog-get-started/data-catalog-connect2.png)

I den här övningen anslöt du till datatillgångar som identifierades med hjälp av Azure Data Catalog. På Azure Data Catalog-portalen kan du ansluta direkt med hjälp av klientprogrammen som är integrerade i **Öppna i**-menyn. Du kan också ansluta med valfria program som du väljer med hjälp av informationen om anslutningsplatsen som finns i datatillgångarnas metadata. Du kan till exempel använda SQL Server Management Studio för att ansluta till AdventureWorks2014-databasen för att komma åt data i datatillgångarna som registrerats i den här självstudiekursen.

1. Öppna **SQL Server Management Studio**.
2. I dialogrutan **Anslut till server** anger du servernamnet från rutan **Egenskaper** på Azure Data Catalog-portalen.
3. Använd lämplig autentisering och relevanta autentiseringsuppgifter för åtkomst till datatillgången. Om du inte har åtkomst använder du informationen i fältet **Begär åtkomst** för att få det.
   
    ![Azure Data Catalog – begär åtkomst](media/data-catalog-get-started/data-catalog-request-access.png)

Klicka på **Visa anslutningssträngar** för att visa och kopiera ADF.NET-, ODBC- och OLEDB-anslutningssträngar till Urklipp för användning i ditt program.

## <a name="manage-data-assets"></a>Hantera datatillgångar
I det här steget konfigurerar du säkerheten för dina datatillgångar. Data Catalog ger inte användare åtkomst till själva informationen. Datakällans ägare styr dataåtkomsten.

Med Data Catalog kan du identifiera datakällor och visa metadata relaterade till de datakällor som registrerats i katalogen. Det kan dock finnas situationer då datakällor endast bör visas för särskilda användare eller för medlemmar i vissa grupper. I dessa scenarier kan du använda Data Catalog för att bli ägare av registrerade datatillgångar i katalogen och för att sedan styra visningen av de resurser som du äger.

> [!NOTE]
> De hanteringsfunktioner som beskrivs i den här övningen är bara tillgängliga i Standard Edition av Azure Data Catalog, inte i den kostnadsfria versionen.
> I Azure Data Catalog kan du överta ägarskapet för datatillgångar, lägga till medägare till datatillgångar och ange datatillgångars synlighet.
> 
> 

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Bli ägare av datatillgångar och begränsa synligheten
1. Gå till [startsidan för Azure Data Catalog](https://www.azuredatacatalog.com). I **sökrutan** skriver du `tags:cycles` och trycker på **Retur**.
2. Klicka på ett objekt i resultatlistan och sedan på **Bli ägare** i verktygsfältet.
3. I avsnittet **Hantering** på panelen **Egenskaper** klickar du på **Bli ägare**.
   
    ![Azure Data Catalog – bli ägare](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Om du vill begränsa synligheten väljer du **Ägare och dessa användare** i avsnittet **Synlighet** och klickar sedan på **Lägg till**. Ange användarnas e-postadresser i textrutan och tryck på **Retur**.
   
    ![Azure Data Catalog – begränsa åtkomst](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Ta bort datatillgångar
I den här övningen tar du bort förhandsgranskningsdata från registrerade datatillgångar samt datatillgångar från katalogen med hjälp av Azure Data Catalog-portalen.

I Azure Data Catalog kan du ta bort enstaka eller flera tillgångar.

1. Gå till [startsidan för Azure Data Catalog](https://www.azuredatacatalog.com).
2. I **sökrutan** skriver du `tags:cycles` och klickar på **Retur**.
3. Markera ett objekt i resultatlistan och klicka på **Ta bort** i verktygsfältet, som du ser i följande bild:
   
    ![Azure Data Catalog – ta bort rutnätsobjekt](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
   
    Om du använder listvyn visas kryssrutan till vänster om objektet, som du ser i följande bild:
   
    ![Azure Data Catalog – ta bort objekt i lista](media/data-catalog-get-started/data-catalog-delete-list-item.png)
   
    Du kan också markera flera datatillgångar och ta bort dem samtidigt, som du ser i följande bild:
   
    ![Azure Data Catalog – ta bort flera datatillgångar](media/data-catalog-get-started/data-catalog-delete-assets.png)

> [!NOTE]
> Som standard tillåter katalogen att alla användare registrerar valfri datakälla och att alla användare tar bort alla datatillgångar som har registrerats. Hanteringsfunktionerna i standardutgåvan av Azure Data Catalog tillhandahåller ytterligare alternativ för ägarskap av tillgångar samt för att begränsa vem som kan identifiera och ta bort tillgångar.
> 
> 

## <a name="summary"></a>Sammanfattning
I den här självstudiekursen har du utforskat grundläggande funktioner i Azure Data Catalog, till exempel registrering, kommentarer, identifiering och hantering av datatillgångar på ett företag. Nu har du slutfört självstudiekursen, så det är dags att komma igång. Du kan börja i dag genom att registrera de datakällor som du och ditt team använder och bjuda in kollegor att använda katalogen.

## <a name="references"></a>Referenser
* [Registrera datatillgångar](data-catalog-how-to-register.md)
* [Identifiera datatillgångar](data-catalog-how-to-discover.md)
* [Kommentera datatillgångar](data-catalog-how-to-annotate.md)
* [Dokumentera datatillgångar](data-catalog-how-to-documentation.md)
* [Ansluta till datatillgångar](data-catalog-how-to-connect.md)
* [Hantera datatillgångar](data-catalog-how-to-manage.md)

