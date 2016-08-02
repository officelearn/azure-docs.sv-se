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
    ms.date="05/17/2016"
    ms.author="douglasl"/>

# Kom igång genom att köra guiden aktivera databas för Stretch

Kör guiden Aktivera databas för Stretch för att konfigurera en Stretch Database.  Det här ämnet går igenom den information du behöver fylla i och de val du behöver göra i guiden.

Läs mer om Stretch Database i [Stretch Database](sql-server-stretch-database-overview.md).

## Starta guiden

1.  I SQL Server Management Studio, i Object Explorer, väljer du databaserna som du vill aktivera Stretch på.

2.  Höger\-klicka och välj **Åtgärder** och välj sedan **Stretch** och sedan **Aktivera** för att starta guiden.

## <a name="Intro"></a>Introduktion
Kolla igenom syftet med guiden och förutsättningarna.

![Introduktionssida för Stretch Database-guiden][StretchWizardImage1]

## <a name="Tables"></a>Välj tabeller
Markera de tabeller som du vill aktivera för Stretch.

![Sidan välj tabeller i Stretch Database-guiden][StretchWizardImage2]

|Kolumn|Beskrivning|
|----------|---------------|
|(ingen rubrik)|Markera kryssrutan i den här kolumnen om du vill aktivera Stretch för den markerade tabellen.|
|**Namn**|Anger namn på kolumnen i tabellen.|
|(ingen rubrik)|En symbol i den här kolumnen indikerar vanligtvis att du inte kan aktivera den valda tabellen för Stretch på grund av ett blockeringsfel. Det kan bero på att tabellen använder en datatyp som inte stöds. Om du hovrar över symbolen så får du mer information i en knappbeskrivning. Mer information finns i [Begränsningar i ytområde och blockeringsproblem för Stretch Database](sql-server-stretch-database-limitations.md).|
|**Stretched**|Anger om tabellen redan är aktiverad.|
|**Migrera**|Du kan migrera en hel tabell (**Hela tabellen**) eller så kan ange ett datumbaserat filterpredikat i guiden. Om du vill använda ett annat filterpredikat för att markera rader att migrera, kör du ALTER TABLE-uttrycket för att ange filterpredikat efter att du avslutat guiden. Mer information om filterpredikat finns i [Använd ett filterpredikat för att välja rader att migrera (Stretch Database)](sql-server-stretch-database-predicate-function.md). Mer information om hur du tillämpar predikatet finns i [Aktivera Stretch Database för en tabell](sql-server-stretch-database-enable-table.md) eller [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Rader**|Anger antalet rader i tabellen.|
|**Storlek (KB)**|Anger storleken på tabellen i KB.|

## <a name="Filter"></a>Du kan också alternativt ange ett datumbaserat filterpredikat

Om du vill ange ett datumbaserat filterpredikat för att välja rader att migrera, kan du göra följande på **Välj tabeller**-sidan.

1.  I listan **Välj de tabeller som du vill stretcha**, klickar du på **Hela tabellen** i tabellens rad. Dialogrutan **Välj rader att stretcha** öppnas.

    ![Definiera ett datumbaserat filterpredikat][StretchWizardImage2a]

2.  I dialogrutan **Välj rader att stretcha**, väljer du **Välj rader**.

3.  I **Namnfältet**, anger du ett namn för filterpredikatet.

4.  Som **Var**-sats väljer du en datumkolumn från tabellen, väljer en operatör och anger ett datumvärde.

5. Klicka på **Kontrollera** för att testa predikatet. Om predikatet returnerar resultat från tabellen, om det finns rader att migrera som uppfyller villkoren, rapporterar testet **Lyckades**.

6.  Klicka på klar för att återgå till sidan **Välj tabeller**.

    ![Välj tabeller-sidan efter att filterpredikat har definierats][StretchWizardImage2b]

## <a name="Configure"></a>Konfigurera Azure-distribution

1.  Logga in på Microsoft Azure med ett Microsoft-konto.

    ![Logga in på Azure – Stretch Database-guiden][StretchWizardImage3]

2.  Välj Azure-prenumerationen för att använda för Stretch Database.

3.  Välj en Azure-region. Om du skapar en ny server, skapas servern i den här regionen.

    För att minimera svarstider, bör du välja den Azure-region där din SQL Server befinner sig. För mer information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).

4.  Ange om du vill använda en befintlig server eller skapa en ny Azure-server.

    Om Active Directory på din SQL Server är federerad med Azure Active Directory, kan du alternativt använda ett federerat servicekonto för SQL Server för att kommunicera med Azure-fjärsservern. Mer information om kraven för det här alternativet finns i [ALTER DATABASE SET-alternativ (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Skapa en ny server**

        1.  Skapa ett inloggningsnamn och lösenord för serveradministratören.

        2.  Alternativt kan du använda ett federerat servicekonto för SQL Server för att kommunicera med Azure-fjärrservern.

        ![Skapa ny Azure server – Stretch Database-guiden][StretchWizardImage4]

    -   **Befintlig server**

        1.  Välj eller ange namnet på den befintliga Azure-servern.

        2.  Välj autentiseringsmetod.

            -   Om du väljer **SQL Server-autentisering**, skapar du en ny inloggning och lösenord.

            -   Välj **Active Directory-integrerad autentisering** för att använda ett federerat servicekonto för SQL Server för att kommunicera med Azure-fjärrservern.

        ![Välj en befintlig Azure-server – Stretch Database-guiden][StretchWizardImage5]

## <a name="Credentials"></a>Säkra autentiseringsuppgifter
Ange ett starkt lösenord för att skapa en huvudnyckel för databasen, eller om det redan finns en huvudnyckel, anger du lösenordet för den.

Du måste ha en huvudnyckel för databasen för att skydda autentiseringsuppgifterna som Stretch Database använder för att ansluta till fjärrdatabasen.

![Sidan säkra autentiseringsuppgifter i Stretch Database-guiden][StretchWizardImage6]

Mer information om huvudnyckeln för databasen finns i [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) och [Skapa en huvudnyckel för en databas](https://msdn.microsoft.com/library/aa337551.aspx). Mer information om autentiseringsuppgifterna som skapas av guiden finns i [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Välj IP-adress
Använd den offentliga IP-adressen för din SQL Server eller ange ett IP-adressintervall för att skapa en brandväggsregel i Azure som låter SQL Server kommunicera med Azure-fjärrservern.

IP-adressen eller adresserna som du anger på den här sidan, säger till Azure-server att låta inkommande data, frågor och hanteringsåtgärder som initierats av SQL Server att passera genom Azure-brandväggen. Guiden ändrar inte någonting i brandväggsinställningarna på SQL Server.

![Sidan välj IP-adress i Stretch Database-guiden][StretchWizardImage7]

## <a name="Summary"></a>Sammanfattning
Granska värdena som du har angett och de alternativ som du valt i guiden. Välj sedan **Slutför** för att aktivera Stretch.

![Sammanfattningssida för Stretch Database-guiden][StretchWizardImage8]

## <a name="Results"></a>Resultat
Granska resultaten.

Alternativt kan du välja **Övervakaren**, för att starta övervakning av datamigreringens förlopp i Stretch Database-övervakaren. Mer information finns i [Övervaka och felsök datamigrering (Stretch Database)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Felsökning av guiden
**Stretch Database-guiden misslyckades.**
Om Stretch Database inte ännu har aktiverats på servernivå och du kör guiden utan systemadministratörs-behörighet, kommer guiden att misslyckas. Be systemadministratören att aktivera Stretch Database på den lokala serverinstansen och kör sedan guiden igen. Mer information finns i [Förutsättningar: Behörighet att aktivera Stretch Database på servern](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Nästa steg
Aktivera ytterligare tabeller för Stretch Database. Övervaka datamigrering och hantera Stretch\-aktiverade databaser och tabeller.

-   [Aktivera Stretch Database för en tabell](sql-server-stretch-database-enable-table.md) för att aktivera ytterligare tabeller.

-   [Övervaka Stretch Database](sql-server-stretch-database-monitor.md) för att se status för datamigrering.

-   [Pausa och återuppta Stretch Database](sql-server-stretch-database-pause.md)

-   [Hantera och felsök Stretch Database](sql-server-stretch-database-manage.md)

-   [Säkerhetskopiera och återställ Stretch-aktiverade databaser](sql-server-stretch-database-backup.md)

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
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png



<!--HONumber=Jun16_HO2-->


