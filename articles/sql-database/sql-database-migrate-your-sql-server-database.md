---
title: Migrera SQLServer-databas till Azure SQL Database | Microsoft Docs
description: "Lär dig att migrera din SQL Server-databas till Azure SQL Database."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,migrate
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 09/01/2017
ms.author: carlrab
ms.openlocfilehash: 526222944974c08f92aec2a8418e9b42401bc4d3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrera din SQL Server-databas till Azure SQL Database

Flytta SQL Server-databasen till Azure SQL Database är så enkelt som att skapa en tom SQL-databas i Azure och sedan använda den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) för att importera databasen till Azure. I kursen får du lära dig att:

> [!div class="checklist"]
> * Skapa en tom Azure SQL-databas på Azure-portalen (med en ny eller befintlig Azure SQL Database-server)
> * Skapa en brandvägg på servernivå i Azure-portalen (om inte tidigare har skapat)
> * Använd den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) för att importera SQL Server-databasen till tom Azure SQL-databas 
> * Använd [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att ändra egenskaperna för databasen.

Om du inte har en Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Installerat den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).  
- Installerat den senaste versionen av den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- Du har identifierat och har åtkomst till en databas för att migrera. Den här kursen använder den [SQL Server 2008R2 AdventureWorks OLTP-databasen](https://msftdbprodsamples.codeplex.com/releases/view/59211) på en instans av SQL Server 2008R2 eller senare, men du kan använda alla databaser som du väljer. Åtgärda kompatibilitetsproblem genom att använda [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas

Azure SQL-databasen skapas med en definierad uppsättning [beräknings-och lagringsresurser](sql-database-service-tiers.md). Databasen skapas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) och i en [logisk Azure SQL Database-server](sql-database-features.md). 

Följ dessa steg om du vill skapa en tom SQL-databas. 

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

2. Välj **Databaser** på sidan **Nytt** och välj **Skapa** under **SQL Database** på sidan **Nytt**.

   ![Skapa en tom databas](./media/sql-database-design-first-database/create-empty-database.png)

3. Fyll i följande information i SQL Database-formuläret (se föregående bild):   

   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Databasnamn** | mySampleDatabase | För giltiga databasnamn, se [databasidentifierare](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). | 
   | **Prenumeration** | Din prenumeration  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Välj källa** | Tom databas | Anger att en tom databas ska skapas. |

4. Klicka på **Server** för att skapa och konfigurera en ny server för den nya databasen. Fyll i den **nytt serverformulär** med följande information: 

   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
   | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
   | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och specialtecken. |
   | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

   ![skapa databas-server](./media/sql-database-design-first-database/create-database-server.png)

5. Klicka på **Välj**.

6. Klicka på **Prisnivå** för att ange tjänstnivå, antalet DTU:er och mängden lagring. Utforska alternativ för antalet dtu: er och lagring som är tillgängliga för varje tjänstnivå. 

7. Den här självstudiekursen, Välj den **Standard** servicenivå och Använd sedan skjutreglaget för att välja **100 dtu: er (S3)** och **400** GB lagringsutrymme.

   ![skapa databas-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Godkänn förhandsgranskningsvillkoren för att använda alternativet **Lägg till mer lagringsutrymme**. 

   > [!IMPORTANT]
   > \*Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\*I Premium-nivån finns för närvarande mer än 1 TB lagringsutrymme i följande regioner: Östra USA 2, USA, västra, Virginia (USA-förvaltad region), Europa, västra, Centrala Tyskland, Sydostasien, Östra Japan, Australien, östra, Centrala Kanada och Östra Kanada. Se [sidan 11-15 i Aktuella begränsningar](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. När du har valt tjänstenivå, antalet DTU:er och mängden lagring klickar du på **Apply** (Använd).  

10. Välj en **sorteringen** för tom databas (använda standardvärdet för den här självstudiekursen). Mer information om sorteringar finns [sorteringar](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Nu när du har fyllt i SQL Database-formuläret klickar du på **Skapa** så att databasen etableras. Etableringen tar några minuter. 

12. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.
    
     ![avisering](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

SQL Database-tjänsten skapar en brandvägg på servernivå som hindrar externa program och verktyg från att ansluta till servern eller databaser på servern, såvida inte en brandväggsregel konfigureras som öppnar brandväggen för specifika IP-adresser. Följ de här stegen om du vill skapa en [brandväggsregel på SQL Database-servernivå](sql-database-firewall-configure.md) för din klients IP-adress och aktivera extern anslutning genom SQL Database-brandväggen endast för din IP-adress. 

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 1433.
>

1. När distributionen är klar klickar du på **SQL-databaser** på menyn till vänster och klickar sedan på **mySampleDatabase** på sidan **SQL-databaser**. Översiktssidan för databasen öppnas, där du kan se det fullständigt kvalificerade servernamnet (som **mynewserver-20170824.database.windows.net**) och alternativ för ytterligare konfiguration. 

2. Kopiera det här fullständiga servernamnet för anslutning till servern och databaserna i efterföljande snabbstarter. 

   ![servernamn](./media/sql-database-get-started-portal/server-name.png) 

3. Klicka på **ange serverbrandvägg** i verktygsfältet. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

   ![brandväggsregler för server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Klicka på **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

5. Klicka på **Spara**. En brandväggsregel på servernivå för att öppna port 1433 på den logiska servern skapas för din aktuella IP-adress.

6. Klicka på **OK** och stäng sedan sidan **Brandväggsinställningar**.

Du kan nu ansluta till SQL Database-servern och dess databaser med SQL Server Management Studio, Data Migration Assistant eller ett annat verktyg som helst från den här IP-adressen med hjälp av server-administratörskonto som skapats i föregående procedur.

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Klicka på **AV** på den här sidan om du vill inaktivera åtkomsten för alla Azure-tjänster.

## <a name="sql-server-connection-information"></a>Anslutningsinformation för en SQL-server

Hämta det fullständigt kvalificerade servernamnet för Azure SQL Database-servern i Azure Portal. Du kan använda det fullständiga servernamnet för att ansluta till din Azure SQL-server med klientverktyg, inklusive stöd för migrering av Data och SQL Server Management Studio.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 
3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**.

   ![anslutningsinformation](./media/sql-database-get-started-portal/server-name.png)

## <a name="migrate-your-database"></a>Migrera databasen

Följ dessa steg för att använda den  **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)**  att kontrollera om databasen för migrering till Azure SQL Database och slutföra migreringen.

1. Öppna den **Data Migration Assistant**. Du kan köra DMA på alla datorer med anslutning till SQL Server-instans som innehåller den databas som du planerar att migrera och anslutning till internet. Du behöver inte installera den på den dator där SQL Server-instansen som du migrerar. Brandväggsregeln som du skapade i föregående procedur måste vara för den dator som du kör Data Migration Assistant.

     ![Öppna assistenten för migrering av data](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. I den vänstra menyn klickar du på **+ ny** att skapa en **Assessment** projekt. Fyll i de begärda värdena och klicka sedan på **skapa**:

   | Inställning      | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Projekttypen | Migrering | Välja att bedöma din databas för migrering eller välja att utvärdera och migrering som en del av samma arbetsflöde |
   |Projektets namn|Vägledning för migrering| Ett beskrivande namn |
   |Typ av server| SQL Server | Detta är den enda källan som stöds för närvarande |
   |Måltypen för server| Azure SQL Database| Alternativen är: Azure SQL Database, SQL Server, SQL Server på virtuella Azure-datorer |
   |Migrering omfång| Schemat och data| Alternativen är: Schema- och data, schemat endast data |
   
   ![nya data migrering assistenten projektet](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3.  På den **Välj källa** , Fyll i de begärda värdena och klickar sedan på **Anslut**:

    | Inställning      | Föreslaget värde | Beskrivning | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | servernamn | Din servernamn eller IP-adress | Din servernamn eller IP-adress |
    | Autentiseringstyp | Önskad autentiseringstyp| Alternativ: Windows-autentisering, SQL Server-autentisering, Active Directory-integrerad autentisering, Active Directory-lösenordsautentisering |
    | Användarnamn | Ditt inloggningsnamn | Din inloggning måste ha **KONTROLLSERVERN** behörigheter |
    | Lösenord| ditt lösenord | ditt lösenord |
    | Anslutningsegenskaper| Välj **kryptera anslutning för** och **förtroende servercertifikat** som passar din miljö. | Välj egenskaperna som är lämpliga för Anslut till servern |

    ![ny migreringen Välj datakälla](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-source.png)

5. Välj en databas från en källserver för att migrera till Azure SQL Database och klicka sedan på **nästa**. För den här kursen finns bara en enskild databas.

6. På den **väljer mål** , Fyll i de begärda värdena och klickar sedan på **Anslut**:

    | Inställning      | Föreslaget värde | Beskrivning | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | servernamn | Azure databasserverns fullständigt kvalificerat namn | Fullständigt kvalificerade Azure Database servernamnet i föregående procedur |
    | Autentiseringstyp | SQL Server-autentisering | SQL Server-autentisering är det enda alternativet som skrivs den här kursen, men Active Directory-integrerad autentisering och Active Directory-lösenordsautentisering stöds också av Azure SQL Database |
    | Användarnamn | Ditt inloggningsnamn | Din inloggning måste ha **CONTROL DATABASE** källplatsens databas |
    | Lösenord| ditt lösenord | ditt lösenord |
    | Anslutningsegenskaper| Välj **kryptera anslutning för** och **förtroende servercertifikat** som passar din miljö. | Välj egenskaperna som är lämpliga för Anslut till servern |

    ![nya data migreringen Välj mål](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-target.png)

7. Markera databasen från målservern som du skapade i föregående procedur och klicka sedan på **nästa** att starta källa databasen schemat assessment. För den här kursen finns bara en enskild databas. Observera att kompatibilitetsnivån för den här databasen är inställd på 140, vilket är standard kompatibilitetsnivån för alla nya databaser i Azure SQL Database.

   > [!IMPORTANT] 
   > När du migrerar din databas till Azure SQL Database måste välja du att databasen på en angiven kompatibilitetsnivå för bakåtkompatibilitet. Mer information om effekterna och alternativ för att driva en databas på en specifik kompatibilitetsnivå finns [ändra DATABASENS kompatibilitetsnivån](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Se även [ALTER OMFÅNG DATABASKONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) information om ytterligare databasnivå inställningar som rör kompatibilitetsnivåer.
   >

8. På den **Välj objekt** , när källan databasen schemat assessment processen är klar granskar du de objekt som valts för migrering och granska de objekt som innehåller problem. Granska till exempel den **dbo.uspSearchCandidateResumes** objekt för **SERVERPROPERTY('LCID')** funktionsändringar och **HumanResourcesJobCandidate** objekt för Fulltextsökning har ändrats. 

   > [!IMPORTANT] 
   > Beroende på databasens design och programmets design när du migrerar källdatabasen kan du behöva ändra antingen eller både databasen eller ditt program efter migreringen (och i vissa fall innan migreringen). Mer information om Transact-SQL-skillnader som kan påverka din migrering finns [lösa Transact-SQL-skillnader under migreringen till SQL Database](sql-database-transact-sql-information.md).

     ![ny migrering bedömning och objektet dataurval](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results.png)

9. Klicka på **generera SQL-skript** skripta schemaobjekt i källdatabasen. 
10. Granska det här genererade skriptet och klicka sedan på **bredvid utfärda** som behövs för att granska de identifierade assessment problemen och rekommendationer. Exempelvis för fulltext-sökning rekommenderas det när du uppgraderar att testa dina program som utnyttjar funktionerna Full-Text. Du kan spara eller kopiera skript om du vill.

     ![nya data skapas migreringsskriptet](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-generated-script.png)

11. Klicka på **distribuera schemat** och titta på schemat migreringsprocessen.

     ![ny migrering schemat datamigrering](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-schema-migration.png)

12. När schemat migreringen är klar granskar du resultaten efter fel och klicka sedan, under förutsättning att det inte finns några, på **migrera data**.
13. På den **Välj tabeller** granskar du de tabeller som valts för migrering och klicka sedan på **starta datamigrering**.

     ![datamigrering för migrering till nya data](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration.png)

14. Titta på migreringsprocessen.

     ![nya data migrering migrerar data](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration-process.png)

## <a name="connect-to-the-database-with-ssms"></a>Ansluta till databasen med SSMS

Använd [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) att upprätta en anslutning till din Azure SQL Database-server.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Servertyp | Databasmotor | Det här värdet krävs |
   | servernamn | Fullständigt kvalificerat servernamn | Namnet ska vara ungefär så här: **mynewserver20170824.database.windows.net**. |
   | Autentisering | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi har konfigurerat i den här kursen. |
   | Inloggning | Serveradministratörskontot | Detta är det konto som du angav när du skapade servern. |
   | Lösenord | Lösenordet för serveradministratörskontot | Detta är det lösenord som du angav när du skapade servern. |

   ![Anslut till server](./media/sql-database-connect-query-ssms/connect.png)

3. Klicka på **Alternativ** i dialogrutan **Anslut till server**. I avsnittet **Anslut till databas** anger du **mySampleDatabase** så att du ansluter till den här databasen.

   ![ansluta till databas på server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Klicka på **Anslut**. Fönstret Object Explorer öppnas i SSMS. 

5. I Object Explorer expanderar du **Databaser** och sedan **mySampleDatabase** för att visa objekten i exempeldatabasen.

   ![objekt i databasen](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="change-database-properties"></a>Ändra egenskaper för databas

Du kan ändra den tjänstnivå och prestandanivå kompatibilitetsnivå med hjälp av SQL Server Management Studio. Under importen rekommenderar vi att du importerar till en högre nivå-databas för prestanda för bästa prestanda, men skala när importen är klar för att spara pengar tills du är redo att använda den importerade databasen aktivt. Ändra kompatibilitetsnivån kan ge bättre prestanda och åtkomst till de senaste funktionerna för Azure SQL Database-tjänsten. När du migrerar en äldre databas bevaras dess databasens kompatibilitetsnivå vid den lägsta stödda nivån som är kompatibel med databasen som importeras. Mer information finns i [förbättrad prestanda för frågor med nivå 130 i Azure SQL Database](sql-database-compatibility-level-query-performance-130.md).

1. Högerklicka i Object Explorer **mySampleDatabase** och klicka sedan på **ny fråga**. Ett frågefönster öppnas ansluten till din databas.

2. Kör följande kommando för att ange tjänstnivån till **Standard** och prestandanivån uppdateras till **S1**.

    ```sql
    ALTER DATABASE mySampleDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

## <a name="next-steps"></a>Nästa steg 
I den här kursen har du lärt dig att:

> * Skapa en tom Azure SQL-databas på Azure-portalen 
> * Skapa en brandvägg på servernivå i Azure-portalen 
> * Använd den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) för att importera SQL Server-databasen till tom Azure SQL-databas 
> * Använd [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) för att ändra egenskaperna för databasen.

Gå vidare till nästa kurs att lära dig att skydda databasen.

> [!div class="nextstepaction"]
> [Skydda din Azure SQL-databas](sql-database-security-tutorial.md).


