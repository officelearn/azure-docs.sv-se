---
title: 'Självstudie: Utföra ETL-åtgärder med Azure Databricks'
description: Lär dig mer om hur du extraherar data från Data Lake Store till Azure Databricks, transformerar dessa data och läser in dem i Azure SQL Data Warehouse.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 11/19/2018
ms.openlocfilehash: 5a6d3265fde3b7633036ddc4cae0a5ea7d246957
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265278"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Självstudier: Extrahera, transformera och läsa in data med Azure Databricks

I den här självstudien får du utföra en ETL-åtgärd (extrahera, transformera och läsa in data) med Azure Databricks. Du extraherar data från Azure Data Lake Store till Azure Databricks, kör transformationer av data i Azure Databricks och läser sedan in dessa transformerade data i Azure SQL Data Warehouse.

Stegen i den här självstudiekursen använder SQL Data Warehouse-anslutningsappen så att Azure Databricks kan överföra data till Azure Databricks. Den här anslutningsappen använder i sin tur Azure Blob Storage som temporär lagring för de data som överförs mellan ett Azure Databricks-kluster och Azure SQL Data Warehouse.

Följande bild visar programflödet:

![Azure Databricks med Data Lake Store och SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks med Data Lake Store och SQL Data Warehouse")

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta
> * Skapa ett Spark-kluster i Azure Databricks
> * Skapa ett Azure Data Lake Store-konto
> * Överför data till Azure Data Lake Store
> * Skapa en anteckningsbok i Azure Databricks
> * Extrahera data från Data Lake Store
> * Transformera data med Azure Databricks
> * Läs in data till Azure SQL Data Warehouse

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar med den här självstudien måste du uppfylla följande krav:
- Skapa ett Azure SQL Data Warehouse, skapa en brandväggsregel på servernivå och anslut till servern som en serveradministratör. Följ anvisningarna i [Snabbstart: Skapa ett Azure SQL Data Warehouse](../sql-data-warehouse/create-data-warehouse-portal.md)
- Skapa en databashuvudnyckel för Azure SQL Data Warehouse. Följ anvisningarna i [Skapa en databashuvudnyckel](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
- Skapa ett Azure Blob Storage-konto och en container i det. Få dessutom åtkomst till lagringskontot genom att hämta åtkomstnyckeln. Följ anvisningarna i [Snabbstart: Skapa ett Azure SQL Blob-lagringskonto](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Azure-portalen. 

1. Välj **Skapa en resurs** > **Data och analys** > **Azure Databricks** i Azure Portal.

    ![Databricks på Azure-portalen](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks på Azure-portalen")

3. Under **Azure Databricks-tjänst** anger du värden för att skapa en Databricks-arbetsyta.

    ![Skapa en arbetsyta för Azure Databricks](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Skapa en arbetsyta för Azure Databricks")

    Ange följande värden:
     
    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../azure-resource-manager/resource-group-overview.md). |
    |**Plats**     | Välj **USA, östra 2**. För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/).        |
    |**Prisnivå**     |  Välj mellan **Standard** och **Premium**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.

4. Det tar några minuter att skapa kontot. När kontot skapas i portalen visas panelen för att **skicka distribution för Azure Databricks** på höger sida. Du kan behöva rulla åt höger på instrumentpanelen för att se panelen. En förloppsindikator visas även längst upp på skärmen. Båda dessa områden visar förloppet.

    ![Distributionspanel för Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Distributionspanel för Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

1. I Azure-portalen går du till Databricks-arbetsytan som du skapade. Välj sedan **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. I portalen väljer du **Kluster**.

    ![Databricks på Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Godkänn alla övriga standardvärden förutom följande värden:

    * Ange ett namn för klustret.
    * För den här artikeln skapar du ett kluster med körningen **4.0**.
    * Se till att markera kryssrutan **Avsluta efter \_\_ minuters inaktivitet**. Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.
    
    Välj **Skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

## <a name="create-an-azure-data-lake-store-account"></a>Skapa ett Azure Data Lake Store-konto

I det här avsnittet skapar du ett Azure Data Lake Store-konto och associerar ett tjänstens huvudnamn för Azure Active Directory med det. Senare i den här självstudiekursen kommer du att använda det här tjänstens huvudnamn i Azure Databricks för att få åtkomst till Azure Data Lake Store.

1. Välj **Skapa en resurs** > **Storage** > **Data Lake Store** i [Azure Portal](https://portal.azure.com).
3. På bladet **Ny Data Lake Store**, anger du de värden som visas på följande skärmbild:
   
    ![Skapa ett nytt Azure Data Lake Store-konto](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "Skapa ett nytt Azure Data Lake-konto")

    Ange följande värden: 
     
    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn**     | Ange ett unikt namn Data Lake Store-kontot.        |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Välj samma resursgrupp för den här självstudiekursen som du använde när du skapade Azure Databricks-arbetsytan.  |
    |**Plats**     | Välj **USA, östra 2**.  |
    |**Prissättningspaket**     |  Välj **Betala per användning**. |
    | **Krypteringsinställningar** | Behåll standardinställningarna. |

    Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.

Du skapar nu ett tjänstens huvudnamn för Azure Active Directory och associerar det till det Data Lake Store-konto som du har skapat.

### <a name="create-an-azure-active-directory-service-principal"></a>Skapa ett tjänstens huvudnamn för Azure Active Directory
   
1. Välj **Alla tjänster** i [Azure Portal](https://portal.azure.com) och sök sedan efter **Azure Active Directory**.

2. Välj **Appregistreringar**.

   ![välj appregistreringar](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. Välj **Ny programregistrering**.

   ![lägg till app](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. Ange ett namn och en URL för programmet. Välj **Webbapp/API** för den programtyp som du vill skapa. Ange en inloggnings-URL och välj sedan **Skapa**.

   ![namnprogram](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

Om du vill få åtkomst till Data Lake Store-kontot från Azure Databricks måste du ha följande värden för det tjänstens huvudnamn för Azure Active Directory som du har skapat:
- Program-ID:t
- Autentiseringsnyckel
- Klient-ID:t

I följande avsnitt hämtar du dessa värden för det tjänstens huvudnamn för Azure Active Directory som du skapade tidigare.

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>Hämta program-ID och autentiseringsnyckel för tjänstens huvudnamn

När du loggar in med programmet behöver du programmets ID och en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Välj ditt program i **Appregistreringar** i Azure Active Directory.

   ![välj program](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. Kopiera **Program-ID:t** och lagra det i din programkod. Vissa [programexempel](#log-in-as-the-application) refererar till det här värdet som klient-ID:t.

   ![klient-ID](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. Välj **Inställningar** om du vill generera en autentiseringsnyckel.

   ![välj inställningar](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. Välj **Nycklar** om du vill generera en autentiseringsnyckel.

   ![välj nycklar](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

   ![spara nyckel](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   När du har sparat nyckeln visas nyckelns värde. Kopiera det här värdet eftersom det inte går att hämta nyckeln senare. Logga in som programmet genom att ange nyckelvärdet med program-ID:t. Lagra nyckelvärdet där programmet kan hämta det.

   ![sparad nyckel](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>Hämta klientorganisations-ID

När du loggar in med programmet måste du skicka klientorganisations-ID:t med din autentiseringsbegäran.

1. Välj **Azure Active Directory**.

   ![välj azure active directory](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. Om du vill hämta klientorganisations-ID:t väljer du **Egenskaper** för din Microsoft Azure Active Directory-klientorganisation.

   ![välj Azure AD-egenskaper](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. Kopiera **katalog-ID:t**. Det här värdet är ditt klientorganisations-ID.

   ![klientorganisations-ID](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png)

## <a name="upload-data-to-data-lake-store"></a>Ladda upp data till Data Lake Store

I det här avsnittet kommer du att överföra en exempeldatafil till Data Lake Store. Du kan använda den här filen senare i Azure Databricks om du vill köra vissa transformationer. De exempeldata (**small_radio_json.json**) som du använder i den här kursen är tillgängliga på den här [Github-lagringsplatsen](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json).

1. Välj det Data Lake Store-konto som du skapade i [Azure Portal](https://portal.azure.com).

2. Klicka på **Datautforskaren** på fliken **Översikt**.

    ![Öppna Datautforskaren](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "Öppna Datautforskaren")

3. Klicka på **Överför** i Datautforskaren.

    ![Alternativet Överför](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "Alternativet Överför")

4. Bläddra fram till platsen för din exempeldatafil i **Överför filer** och välj sedan **Lägg till markerade filer**.

    ![Alternativet Överför](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "Alternativet Överför")

5. I den här självstudiekursen får du överföra en datafil till Data Lake Store-roten. Så, nu är filen tillgänglig på `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`.

## <a name="associate-service-principal-with-azure-data-lake-store"></a>Associera tjänstens huvudnamn med Azure Data Lake Store

I det här avsnittet associerar du data i Azure Data Lake Store-kontot med det tjänsthuvudnamn för Azure Active Directory som du har skapat. Detta säkerställer att du har åtkomst till Data Lake Store-kontot från Azure Databricks. För scenariot i den här artikeln kan du läsa data i Data Lake Store för att fylla i en tabell i SQL Data Warehouse. Enligt [översikten över åtkomstkontroll i Data Lake Store](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions) kräver läsbehörighet för en fil i Data Lake Store följande:

- **Köra**-behörighet för alla mappar i den mappstruktur som leder upp till filen.
- **Läsa**-behörighet på själva filen.

Utför följande steg för att bevilja dessa behörigheter.

1. Från [Azure-portalen](https://portal.azure.com) väljer du det Data Lake Store-konto som du skapade och väljer sedan **Datautforskaren**.

    ![Starta Datautforskaren](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-data-explorer.png "Starta Datautforskaren")

2. Eftersom exempeldatafilen är i roten på mappstrukturen i det här exemplet behöver du bara tilldela **Köra**-behörighet vid rotmappen. Om du vill göra det väljer du **Åtkomst** från roten i datautforskaren.

    ![Lägga till ACL:er för mappen](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-1.png "Lägga till ACL:er för mappen")

3. Under **Åtkomst** väljer du **Lägg till**.

    ![Lägga till ACL:er för mappen](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-2.png "Lägga till ACL:er för mappen")

4. Under **Tilldela behörigheter** klickar du på **Välj användare eller grupp** och söker efter det Azure Active Directory-tjänsthuvudnamn som du skapade tidigare.

    ![Lägg till Data Lake Store-åtkomst](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Lägg till Data Lake Store-åtkomst")

    Välj det AAD-tjänsthuvudnamn som du vill tilldela och klicka på **Välj**.

5. Under **Tilldela behörigheter** klickar du på **Välj behörigheter** > **Köra**. Behåll de övriga standardvärdena och välj **OK** under **Välj behörigheter** och sedan under **Tilldela behörigheter**.

    ![Lägg till Data Lake Store-åtkomst](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-4.png "Lägg till Data Lake Store-åtkomst")

6. Gå tillbaka till Datautforskaren och klicka på den fil som du vill tilldela läsbehörigheten. Under **Filförhandsgranskning** väljer du **Åtkomst**.

    ![Lägg till Data Lake Store-åtkomst](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-1.png "Lägg till Data Lake Store-åtkomst")

7. Under **Åtkomst** väljer du **Lägg till**. Under **Tilldela behörigheter** klickar du på **Välj användare eller grupp** och söker efter det Azure Active Directory-tjänsthuvudnamn som du skapade tidigare.

    ![Lägg till Data Lake Store-åtkomst](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Lägg till Data Lake Store-åtkomst")

    Välj det AAD-tjänsthuvudnamn som du vill tilldela och klicka på **Välj**.

8. Under **Tilldela behörigheter** klickar du på **Välj behörigheter** > **Läsa**. Välj **OK** under **Välj behörigheter** och sedan under **Tilldela behörigheter**.

    ![Lägg till Data Lake Store-åtkomst](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-2.png "Lägg till Data Lake Store-åtkomst")

    Tjänstens huvudnamn har nu tillräcklig behörighet att läsa exempeldatafilen från Azure Data Lake Store.

## <a name="extract-data-from-data-lake-store"></a>Extrahera data från Data Lake Store

I det här avsnittet skapar du en anteckningsbok på arbetsytan Azure Databricks och kör sedan kodfragment för att extrahera data från Data Lake Store till Azure Databricks.

1. Gå till arbetsytan Azure Databricks som du skapat i [Azure-portalen](https://portal.azure.com). Välj sedan **Starta arbetsyta**.

2. Välj **Arbetsyta** i det vänstra fönstret. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skapa anteckningsbok i Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Skapa anteckningsbok i Databricks")

2. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Scala** som språk och välj sedan det Spark-kluster som du skapade tidigare.

    ![Skapa anteckningsbok i Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Skapa anteckningsbok i Databricks")

    Välj **Skapa**.

3. Lägg till följande kodfragment i en tom kodcell och ersätt platshållarvärdena med de värden som du sparade tidigare för tjänstens huvudnamn för Azure Active Directory.

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    Kör kodcellen genom att trycka på **SKIFT + RETUR**.

4. Du kan nu läsa in json-exempelfilen i Data Lake Store som en dataram i Azure Databricks. Klistra in följande kodfragment i en ny kodcell, ersätt platshållarvärdet och tryck sedan på **SKIFT + RETUR**.

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. Kör följande kodfragment om du vill se dataramens innehåll.

        df.show()

    Du bör se utdata som liknar följande fragment:

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Du har nu extraherat data från Azure Data Lake Store till Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Transformera data med Azure Databricks

Exempelrådata **small_radio_json.json** fångar målgruppen för en radiostation och har flera olika kolumner. I det här avsnittet kommer du att transformera data så att endast specifika kolumner hämtas från datamängden.

1. Börja med att endast hämta kolumnerna *förnamn*, *efternamn*, *kön*, *plats* och *nivå* från den dataram som du redan har skapat.

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
        specificColumnsDf.show()

    Du får utdata som liknar följande kodfragment:

        +---------+----------+------+--------------------+-----+
        |firstname|  lastname|gender|            location|level|
        +---------+----------+------+--------------------+-----+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        +---------+----------+------+--------------------+-----+

2. Du kan transformera dessa data ytterligare genom att t.ex. ändra namnet på kolumnen **nivå** till **prenumerationstyp**.

        val renamedColumnsDf = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDf.show()

    Du får utdata som liknar följande kodfragment.

        +---------+----------+------+--------------------+-----------------+
        |firstname|  lastname|gender|            location|subscription_type|
        +---------+----------+------+--------------------+-----------------+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        +---------+----------+------+--------------------+-----------------+

## <a name="load-data-into-azure-sql-data-warehouse"></a>Läs in data till Azure SQL Data Warehouse

I det här avsnittet kommer du att överföra de data du transformerade till Azure SQL Data Warehouse. Om du använder Azure SQL Data Warehouse-anslutningen för Azure Databricks kan du överföra en dataram direkt som en tabell i SQL Data Warehouse.

Som tidigare nämnts använder SQL Data Warehouse-anslutningen Azure Blob Storage som temporär lagringsplats vid överföring av data mellan Azure Databricks och Azure SQL Data Warehouse. Så du börjar med att tillhandahålla den konfiguration som ska ansluta till lagringskontot. Du måste redan ha skapat kontot som en del av de nödvändiga förutsättningarna för den här artikeln.

1. Ange konfigurationen så att du får åtkomst till Azure Storage-kontot från Azure Databricks. Om du kopierar webbadressen för din bloblagring från portalen måste du ta bort *https://* i början. 

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey = "<ACCESS KEY>"

2. Ange en tillfällig mapp som ska användas när du flyttar data mellan Azure Databricks och Azure SQL Data Warehouse.

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. Kör följande fragment om du vill lagra åtkomstnycklar för Azure Blob Storage i konfigurationen. Detta säkerställer att du inte behöver behålla åtkomstnyckeln i anteckningsboken i oformaterad text.

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Ange värdena för att ansluta till Azure SQL Data Warehouse-instansen. Du måste ha skapat ett SQL Data Warehouse som en del av förutsättningarna.

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>"
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort = "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. Kör följande fragment om du vill läsa in den transformerande dataramen **renamedColumnsDf** som en tabell i SQL Data Warehouse. Det här kodfragmentet skapar en tabell med namnet **SampleTable** i SQL-databasen. Observera att Azure SQL DW kräver en huvudnyckel. Du kan skapa en huvudnyckel genom att köra kommandot ”CREATE MASTER KEY;” i SQL Server Management Studio.

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDf.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall)
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. Anslut till SQL-databasen och verifiera att du ser en **SampleTable**.

    ![Verifiera exempeltabellen](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Verifiera exempeltabellen")

7. Verifiera tabellens innehåll genom att köra en urvalsfråga. Den bör ha samma data som dataramen **renamedColumnsDf**.

    ![Verifiera exempeltabellens innehåll](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Verifiera exempeltabellens innehåll")

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med självstudien kan du avsluta klustret. Detta gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**.

![Stoppa ett Databricks-kluster](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Stoppa ett Databricks-kluster")

Om du inte manuellt avslutar klustret kommer det att stoppas automatiskt, förutsatt att du har markerat kryssrutan **Avsluta efter \_\_ minuters inaktivitet** när klustret skapades. I sådant fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Azure Databricks-arbetsyta
> * Skapa ett Spark-kluster i Azure Databricks
> * Skapa ett Azure Data Lake Store-konto
> * Överför data till Azure Data Lake Store
> * Skapa en anteckningsbok i Azure Databricks
> * Extrahera data från Data Lake Store
> * Transformera data med Azure Databricks
> * Läs in data till Azure SQL Data Warehouse

Gå vidare till nästa självstudiekurs och lär dig mer om att strömma realtidsdata i Azure Databricks med Azure Event Hubs.

> [!div class="nextstepaction"]
>[Strömma data i Azure Databricks med Event Hubs](databricks-stream-from-eventhubs.md)
