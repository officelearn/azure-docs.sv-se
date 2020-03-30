---
title: Migrera data till Azure Cosmos DB SQL API-konto med Striim
description: Lär dig hur du använder Striim för att migrera data från en Oracle-databas till ett Azure Cosmos DB SQL API-konto.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 36ba9e2d3385184f32876a6d067b58f7c21a90bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71003286"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrera data till Azure Cosmos DB SQL API-konto med Striim
 
Striim-avbildningen på Azure-marknadsplatsen erbjuder kontinuerlig dataförflyttning i realtid från informationslager och databaser till Azure. När du flyttar data kan du utföra in-line denormalisering, dataomvandling, aktivera realtidsanalys och scenarier för datarapportering. Det är enkelt att komma igång med Striim för att kontinuerligt flytta företagsdata till Azure Cosmos DB SQL API. Azure tillhandahåller ett marketplace-erbjudande som gör det enkelt att distribuera Striim och migrera data till Azure Cosmos DB. 

Den här artikeln visar hur du använder Striim för att migrera data från en **Oracle-databas** till ett **Azure Cosmos DB SQL API-konto**.

## <a name="prerequisites"></a>Krav

* Om du inte har en [Azure-prenumeration](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

* En Oracle-databas som körs lokalt med vissa data i den.

## <a name="deploy-the-striim-marketplace-solution"></a>Distribuera Striim-marknadsplatsen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Skapa en resurs** och sök efter **Striim** på Azure-marknadsplatsen. Välj det första alternativet och **Skapa**.

   ![Hitta Striim marknadsplatsobjekt](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Ange sedan konfigurationsegenskaperna för Striim-instansen. Striim-miljön distribueras på en virtuell dator. I fönstret **Grunderna** anger du **vm-användarnamnet**, **VM-lösenord** (det här lösenordet används för att SSH i den virtuella datorn). Välj **information** **om prenumeration,** **resursgrupp**och plats där du vill distribuera Striim. När du är klar väljer du **OK**.

   ![Konfigurera grundläggande inställningar för Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. I **fönstret Inställningar för Striim-kluster** väljer du typ av Striim-distribution och storleken på den virtuella datorn.

   |Inställning | Värde | Beskrivning |
   | ---| ---| ---|
   |Distributionstyp för Striim |Fristående | Striim kan köras i en **fristående** eller klusterdistributionstyper. **Cluster** Fristående läge distribuerar Striim-servern på en enda virtuell dator och du kan välja storleken på de virtuella datorerna beroende på din datavolym. Klusterläget distribuerar Striim-servern på två eller flera virtuella datorer med den valda storleken. Klustermiljöer med mer än 2 noder erbjuder automatisk hög tillgänglighet och redundans.</br></br> I den här självstudien kan du välja Fristående alternativ. Använd den virtuella standarddatorn för "Standard_F4s".Use the default "Standard_F4s" size VM.  | 
   | Namnet på Striim-klustret|    <Striim_cluster_Name>|  Namn på Striim-klustret.|
   | Striim-klusterlösenord|   <Striim_cluster_password>|  Lösenord för klustret.|

   När du har fyllt i formuläret väljer du **OK** för att fortsätta.

1. I fönstret **Inställningar för Striim-åtkomst** konfigurerar du den **offentliga IP-adressen** (välj standardvärden), **Domännamn för Striim**, **Admin-lösenord** som du vill använda för att logga in på Striim-användargränssnittet. Konfigurera ett VNET och undernät (välj standardvärden). När du har fyllt i informationen väljer du **OK** för att fortsätta.

   ![Inställningar för Striim-åtkomst](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure validerar distributionen och ser till att allt ser bra ut. validering tar några minuter att slutföra. När valideringen är klar väljer du **OK**.
  
1. Slutligen granska användningsvillkoren och välj **Skapa** för att skapa din Striim-instans. 

## <a name="configure-the-source-database"></a>Konfigurera källdatabasen 

I det här avsnittet konfigurerar du Oracle-databasen som källa för dataflyttning.  Du behöver [Oracle JDBC-drivrutinen](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) för att ansluta till Oracle. Om du vill läsa ändringar från oracle-databasen kan du antingen använda [LogMiner-](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) eller [XStream-API:erna](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Oracle JDBC-drivrutinen måste finnas i Striims Java-klassväg för att läsa, skriva eller bevara data från Oracle-databasen.

Ladda ner [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) föraren på din lokala maskin. Du kommer att installera det i Striim-klustret senare.

## <a name="configure-the-target-database"></a>Konfigurera måldatabasen

I det här avsnittet konfigurerar du Azure Cosmos DB SQL API-kontot som mål för dataflyttning.

1. Skapa ett [Azure Cosmos DB SQL API-konto](create-cosmosdb-resources-portal.md) med Azure-portalen.

1. Navigera till **fönstret Data Explorer** i ditt Azure Cosmos-konto. Välj **Ny behållare** om du vill skapa en ny behållare. Anta att du migrerar *produkter* och *beställer* data från Oracle-databasen till Azure Cosmos DB. Skapa en ny databas med namnet **StriimDemo** med en behållare med namnet **Order**. Etablera behållaren med **1000 ru:er** (i det här exemplet används 1 000 ru: er, men du bör använda dataflödet som beräknas för din arbetsbelastning) och **/ORDER_ID** som partitionsnyckel. Dessa värden varierar beroende på källdata. 

   ![Skapa ett SQL API-konto](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurera Oracle till Azure Cosmos DB-dataflöde

1. Nu går vi tillbaka till Striim. Innan du interagerar med Striim installerar du Oracle JDBC-drivrutinen som du hämtade tidigare.

1. Navigera till Striim-instansen som du distribuerade i Azure-portalen. Välj knappen **Anslut** i den övre menyraden och kopiera URL:en i **fältet Logga in med det lokala kontofältet för virtuella** datorer på fliken **SSH.**

   ![Hämta SSH-URL:en](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Öppna ett nytt terminalfönster och kör SSH-kommandot som du kopierade från Azure-portalen. Den här artikeln använder terminal i en MacOS, kan du följa liknande instruktioner med PuTTY eller en annan SSH-klient på en Windows-dator. När du uppmanas till det skriver du **ja** för att fortsätta och anger det **lösenord** som du har angett för den virtuella datorn i föregående steg.

   ![Ansluta till Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Nu, öppen en ny slutlig tab till kopia den **ojdbc8.jar** arkivera du dataöverföring tidigare. Använd följande SCP-kommando för att kopiera jar-filen från den lokala datorn till tmp-mappen för Striim-instansen som körs i Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Kopiera jar-filen från platsdator till Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Därefter navigera tillbaka till fönstret där du gjorde SSH till Striim-instansen och Logga in som sudo. Flytta **filen ojdbc8.jar** från katalogen **/tmp** till **lib-katalogen** för din Striim-förekomst med följande kommandon:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Flytta jar-filen till lib-mappen](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Starta om Striim-servern från samma terminalfönster genom att köra följande kommandon:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim kommer att ta en minut att starta upp. Om du vill se status kör du följande kommando: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Nu navigera tillbaka till Azure och kopiera den offentliga IP-adressen för din Striim VM. 

   ![Kopiera Striim VM-IP-adress](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Om du vill navigera till Striims webbgränssnitt öppnar du en ny flik i en webbläsare och kopierar den offentliga IP-adressen följt av: 9080. Logga in med **admin** administratörsanvändarnamnet tillsammans med det administratörslösenord som du angav i Azure-portalen.

   ![Logga in på Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Nu kommer du till Striims hemsida. Det finns tre olika fönsterrutor – **Instrumentpaneler,** **Appar**och **SourcePreview**. Med fönstret Instrumentpaneler kan du flytta data i realtid och visualisera dem. Fönstret Appar innehåller dina strömmande datapipelpipelar eller dataflöden. Till höger på sidan finns SourcePreview där du kan förhandsgranska dina data innan du flyttar dem.

1. Välj **fönstret Appar,** vi fokuserar på den här rutan för tillfället. Det finns en mängd olika exempelappar som du kan använda för att lära dig mer om Striim, men i den här artikeln skapar du våra egna. Välj knappen **Lägg till app** i det övre högra hörnet.

   ![Lägga till Striim-appen](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Det finns några olika sätt att skapa Striim-program. Välj **Börja med mall för** att börja med en befintlig mall.

   ![Starta appen med mallen](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. I fältet **Sökmallar** skriver du "Cosmos" och väljer **Mål: Azure Cosmos DB** och väljer sedan **Oracle CDC till Azure Cosmos DB**.

   ![Välj Oracle CDC till Cosmos DB](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. På nästa sida namnger du programmet. Du kan ange ett namn som **oraToCosmosDB** och sedan välja **Spara**.

1. Ange sedan källkonfigurationen för din källa Oracle-instans. Ange ett värde för **källnamnet**. Källnamnet är bara en namngivningskonvention för Striim-programmet, du kan använda något i stil med **src_onPremOracle**. Ange värden för resten av källparametrarnas **URL**, **Användarnamn**, **Lösenord**, välj **LogMiner** som läsare för att läsa data från Oracle. Välj **Nästa** för att fortsätta.

   ![Konfigurera källparametrar](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. Striim kommer att kontrollera din miljö och se till att den kan ansluta till din källa Oracle-instans, har rätt behörighet och att CDC har konfigurerats korrekt. När alla värden har validerats väljer du **Nästa**.

   ![Validera källparametrar](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. Markera de tabeller från Oracle-databasen som du vill migrera. Låt oss till exempel välja tabellen Order och välja **Nästa**. 

   ![Välj källtabeller](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. När du har valt källtabellen kan du utföra mer komplicerade åtgärder som mappning och filtrering. I det här fallet skapar du bara en replik av källtabellen i Azure Cosmos DB. Välj därför **Nästa** för att konfigurera målet

1. Nu ska vi konfigurera målet:

   * **Målnamn** - Ange ett eget namn för målet. 
   * **Indata från** - Välj indataströmmen från den som du skapade i oracle-källkonfigurationen i listrutan. 
   * **Samlingar**- Ange konfigurationsegenskaperna för Azure Cosmos DB. Samlingssyntaxen är **SourceSchema.SourceTable, TargetDatabase.TargetContainer**. I det här exemplet skulle värdet vara "SYSTEM. ORDER, StriimDemo.Orders". 
   * **AccessKey** - PrimaryKey för ditt Azure Cosmos-konto.
   * **ServiceEndpoint** – URI för ditt Azure Cosmos-konto, de finns under avsnittet **Nycklar** i Azure-portalen. 

   Välj **Spara** och **nästa**.

   ![Konfigurera målparametrar](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. Därefter kommer du fram till flödesdesignern, där du kan dra och hoppa ut ur boxkopplingarna för att skapa dina strömmande program. Du kommer inte att göra några ändringar i flödet på denna punkt. så gå vidare och distribuera programmet genom att välja knappen **Distribuera app.**
 
   ![Distribuera appen](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. I distributionsfönstret kan du ange om du vill köra vissa delar av programmet på specifika delar av distributionstopologin. Eftersom vi kör i en enkel distributionstopologi via Azure använder vi standardalternativet.

   ![Använda standardalternativet](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. När du har distribuerat kan du förhandsgranska dataströmmen för att se data flödar igenom. Välj **vågikonen** och ögongloben bredvid den. Välj knappen **Distribuerad** i den övre menyraden och välj **Start app**.

   ![Starta appen](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. Genom att använda en **CDC(Change Data Capture)** läsare, Striim kommer att plocka upp endast nya ändringar i databasen. Om du har data som flödar genom källtabellerna visas den. Men eftersom detta är en demotabell är källan inte ansluten till något program. Om du använder en exempeldatagenerator kan du infoga en händelsekedja i Oracle-databasen.

1. Du ser data flödar genom Striim-plattformen. Striim plockar upp alla metadata som är associerade med din tabell också, vilket är bra att övervaka data och se till att data landar på rätt mål.

   ![Konfigurera CDC-pipeline](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. Slutligen ska vi logga in på Azure och navigera till ditt Azure Cosmos-konto. Uppdatera Data Explorer och du kan se att data har kommit.  

   ![Verifiera migrerade data i Azure](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

Genom att använda Striim-lösningen i Azure kan du kontinuerligt migrera data till Azure Cosmos DB från olika källor som Oracle, Cassandra, MongoDB och flera andra till Azure Cosmos DB. Om du vill veta mer besök [Striim webbplats,](https://www.striim.com/) [ladda ner en gratis 30-dagars testversion av Striim](https://go2.striim.com/download-free-trial), och för eventuella problem när du ställer in migreringsvägen med Striim, lämna in en [supportbegäran.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Nästa steg

* Om du migrerar data till Azure Cosmos DB SQL API läser du [hur du migrerar data till Cassandra API-konto med Striim](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Övervaka och felsöka dina data med Azure Cosmos DB-mått](use-metrics.md)
