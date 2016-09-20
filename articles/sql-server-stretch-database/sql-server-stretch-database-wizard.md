<properties
    pageTitle="Kom igång genom att köra guiden aktivera databas för Stretch | Microsoft Azure"
    description="Lär dig hur du konfigurerar en databas för Stretch Database genom att köra guiden aktivera databas för Stretch."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager=""
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# Kom igång genom att köra guiden aktivera databas för Stretch

Kör guiden Aktivera databas för Stretch för att konfigurera en Stretch Database.  Det här ämnet går igenom den information du behöver fylla i och de val du behöver göra i guiden.

Läs mer om Stretch Database i [Stretch Database](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Senare, om du inaktiverar Stretch Database, måste du komma ihåg att fjärrobjektet inte tas bort om du inaktiverar Stretch Database för en tabell eller för en databas. Om du vill ta bort fjärrtabellen eller fjärrdatabasen måste du göra det via Azure-hanteringsportalen. Fjärrobjekten fortsätter att ackumulera Azure-kostnader tills du tar bort dem manuellt. 

## Starta guiden

1.  I SQL Server Management Studio, i Object Explorer, väljer du databaserna som du vill aktivera Stretch på.

2.  Höger\-klicka och välj **Åtgärder** och välj sedan **Stretch** och sedan **Aktivera** för att starta guiden.

## <a name="Intro"></a>Introduktion
Kolla igenom syftet med guiden och förutsättningarna.

De viktigaste kraven är som följer:

-   Du måste vara administratör för att kunna ändra databasinställningar.
-   Du måste ha en Microsoft Azure-prenumeration.
-   SQL Server måste kunna kommunicera med Azure-fjärrservern.

![Introduktionssida för Stretch Database-guiden][StretchWizardImage1]

## <a name="Tables"></a>Välj tabeller
Markera de tabeller som du vill aktivera för Stretch.

Tabeller med många rader visas överst i den sorterade listan. Innan guiden visar listan över tabeller genomsöks den efter datatyper som inte stöds av Stretch Database.

![Sidan välj tabeller i Stretch Database-guiden][StretchWizardImage2]

|Kolumn|Beskrivning|
|----------|---------------|
|(ingen rubrik)|Markera kryssrutan i den här kolumnen om du vill aktivera Stretch för den markerade tabellen.|
|**Namn**|Anger namn på kolumnen i tabellen.|
|(ingen rubrik)|En symbol i den här kolumnen kan representera en varning som inte förhindrar att du aktiverar den valda tabellen för Stretch. Den kan också representera ett blockeringsproblem som hindrar dig från att aktivera den valda tabellen för Stretch, till exempel om tabellen använder en datatyp som inte stöds. Om du hovrar över symbolen så får du mer information i en knappbeskrivning. Mer information finns i [Begränsningar för Stretch Database](sql-server-stretch-database-limitations.md).|
|**Stretched**|Anger om tabellen redan har aktiverats för Stretch.|
|**Migrera**|Du kan migrera en hel tabell (**Hela tabellen**) eller ange ett filter för en befintlig kolumn i tabellen. Om du vill använda en annan filterfunktion för att markera rader som ska migreras anger du filterfunktionen genom att köra ALTER TABLE-instruktionen när du har avslutat guiden. Mer information om filterfunktionen finns i [Välja rader som ska migreras med hjälp av en filterfunktion](sql-server-stretch-database-predicate-function.md). Mer information om hur du tillämpar funktionen finns i [Aktivera Stretch Database för en tabell](sql-server-stretch-database-enable-table.md) och [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Rader**|Anger antalet rader i tabellen.|
|**Storlek (KB)**|Anger storleken på tabellen i KB.|

## <a name="Filter"></a>Du kan också lägga till ett radfilter

Om du vill lägga till en filterfunktion för att välja de rader som ska migreras gör du följande på sidan **Välj tabeller**.

1.  I listan **Välj de tabeller som du vill stretcha**, klickar du på **Hela tabellen** i tabellens rad. Dialogrutan **Välj rader att stretcha** öppnas.

    ![Definiera en filterfunktion][StretchWizardImage2a]

2.  I dialogrutan **Välj rader att stretcha**, väljer du **Välj rader**.

3.  I **namnfältet**, anger du ett namn för filterfunktionen.

4.  För **Where**-satsen väljer du en kolumn från tabellen, väljer en operatör och anger ett värde.

5. Testa funktionen genom att klicka på **Kontrollera**. Om funktionen returnerar resultat från tabellen, dvs. om det finns rader att migrera som uppfyller villkoren, så rapporterar testet **Lyckades**.

    >   [AZURE.NOTE] Textrutan som visar filterfrågan är skrivskyddad. Du kan inte redigera frågan i textrutan.

6.  Klicka på klar för att återgå till sidan **Välj tabeller**.

Filterfunktionen skapas i SQL Server när du slutför guiden. Tills dess kan du gå tillbaka till sidan **Välj tabeller** för att ändra eller byta namn på filterfunktionen.

![Sidan Välj tabeller när du har definierat en filterfunktion][StretchWizardImage2b]

Om du vill använda en annan typ av filterfunktion för att välja rader som ska migreras gör du något av följande:  

-   Avsluta guiden och kör ALTER TABLE-instruktionen för att aktivera Stretch för tabellen och för att ange en filterfunktion. Mer information finns i [Aktivera Stretch Database för en tabell](sql-server-stretch-database-enable-table.md).  

-   Kör ALTER TABLE-instruktionen för att ange en filterfunktion när du har avslutat guiden. Information om de steg du följer finns i [Lägga till en filterfunktion när du har kört guiden](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Konfigurera Azure-distribution

1.  Logga in på Microsoft Azure med ett Microsoft-konto.

    ![Logga in på Azure – Stretch Database-guiden][StretchWizardImage3]

2.  Välj den befintliga Azure-prenumeration som du vill använda för Stretch Database.

3.  Välj en Azure-region.
    -   Om du skapar en ny server, skapas servern i den här regionen.  
    -   Om du har befintliga servrar i det valda regionen visas de i guiden när du väljer **Befintlig server**.

    För att minimera svarstider, bör du välja den Azure-region där din SQL Server befinner sig. För mer information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).

4.  Ange om du vill använda en befintlig server eller skapa en ny Azure-server.

    Om Active Directory på din SQL Server är federerad med Azure Active Directory, kan du alternativt använda ett federerat servicekonto för SQL Server för att kommunicera med Azure-fjärsservern. Mer information om kraven för det här alternativet finns i [ALTER DATABASE SET-alternativ (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Skapa en ny server**

        1.  Skapa ett inloggningsnamn och lösenord för serveradministratören.

        2.  Alternativt kan du använda ett federerat servicekonto för SQL Server för att kommunicera med Azure-fjärrservern.

        ![Skapa ny Azure server – Stretch Database-guiden][StretchWizardImage4]

    -   **Befintlig server**

        1.  Välj den befintliga Azure-servern.

        2.  Välj autentiseringsmetod.

            -   Om du väljer **SQL Server-autentisering** anger du inloggningsuppgifterna och lösenordet för administratörer.

            -   Välj **Active Directory-integrerad autentisering** för att använda ett federerat servicekonto för SQL Server för att kommunicera med Azure-fjärrservern. Det här alternativet visas inte om den valda servern inte är integrerad med Azure Active Directory.

        ![Välj en befintlig Azure-server – Stretch Database-guiden][StretchWizardImage5]

## <a name="Credentials"></a>Säkra autentiseringsuppgifter
Du måste ha en huvudnyckel för databasen för att skydda autentiseringsuppgifterna som Stretch Database använder för att ansluta till fjärrdatabasen.  

Om det redan finns en huvudnyckel för databasen anger du lösenordet för den.  

![Sidan säkra autentiseringsuppgifter i Stretch Database-guiden][StretchWizardImage6b]

Om databasen inte redan har en huvudnyckel anger du ett starkt lösenord för att skapa en huvudnyckel för databasen.  

![Sidan säkra autentiseringsuppgifter i Stretch Database-guiden][StretchWizardImage6]

Mer information om huvudnyckeln för databasen finns i [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) och [Skapa en huvudnyckel för en databas](https://msdn.microsoft.com/library/aa337551.aspx). Mer information om autentiseringsuppgifterna som skapas av guiden finns i [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Välj IP-adress
Använd undernätets IP-adressintervall (rekommenderas) eller den offentliga IP-adressen för din SQL-server och skapa en brandväggsregel för Azure som låter SQL Server kommunicera med Azure-fjärrservern.

IP-adressen eller adresserna som du anger på den här sidan, säger till Azure-server att låta inkommande data, frågor och hanteringsåtgärder som initierats av SQL Server att passera genom Azure-brandväggen. Guiden ändrar inte någonting i brandväggsinställningarna på SQL Server.

![Sidan välj IP-adress i Stretch Database-guiden][StretchWizardImage7]

## <a name="Summary"></a>Sammanfattning
Gå igenom de värden som du har angett, de alternativ som du valde i guiden och de uppskattade kostnaderna i Azure. Välj sedan **Slutför** för att aktivera Stretch.

![Sammanfattningssida för Stretch Database-guiden][StretchWizardImage8]

## <a name="Results"></a>Resultat
Granska resultaten.

Information om hur du övervakar datamigreringens status finns i [Övervaka och felsöka datamigrering (Stretch Database)](sql-server-stretch-database-monitor.md).

![Resultatsida i Stretch Database-guiden][StretchWizardImage9]

## <a name="KnownIssues"></a>Felsökning av guiden
**Stretch Database-guiden misslyckades.**
Om Stretch Database inte ännu har aktiverats på servernivå och du kör guiden utan systemadministratörs-behörighet, kommer guiden att misslyckas. Be systemadministratören att aktivera Stretch Database på den lokala serverinstansen och kör sedan guiden igen. Mer information finns i [Förutsättningar: Behörighet att aktivera Stretch Database på servern](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Nästa steg
Aktivera ytterligare tabeller för Stretch Database. Övervaka datamigrering och hantera Stretch\-aktiverade databaser och tabeller.

-   [Aktivera Stretch Database för en tabell](sql-server-stretch-database-enable-table.md) för att aktivera ytterligare tabeller.

-   [Övervaka och felsöka datamigrering](sql-server-stretch-database-monitor.md) innehåller information om hur du visar datamigreringens status.

-   [Pausa och återuppta Stretch Database](sql-server-stretch-database-pause.md)

-   [Hantera och felsök Stretch Database](sql-server-stretch-database-manage.md)

-   [Säkerhetskopiera Stretch-aktiverade databaser](sql-server-stretch-database-backup.md)

## Se även

[Aktivera Stretch Database för en databas](sql-server-stretch-database-enable-database.md)

[Aktivera Stretch Database för en tabell](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png



<!--HONumber=sep16_HO1-->


