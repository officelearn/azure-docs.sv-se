---
title: Kopiera data från SQL Server till Blob Storage med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från ett lokalt datalager till Azure-molnet med hjälp av en lokal Integration Runtime i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 5d362d8167cdfb772c70b02cc57bb49d3c2eb01d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Självstudie: Kopiera data från en lokal SQL Server-databas till Azure Blob Storage
I den här självstudien använder du Azure PowerShell för att skapa en Data Factory-pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage. Du skapar och använder en lokal installation av Integration Runtime som flyttar data mellan lokala datalager och datalager i molnet. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Azure Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Den här artikeln ger inte någon detaljerad introduktion till Data Factory-tjänsten. Mer information finns i [Introduktion till Azure Data Factory](introduction.md). 

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage. 
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

## <a name="prerequisites"></a>Krav
### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara tilldelad en *deltagare* eller *ägare*, eller vara en *administratör* för Azure-prenumerationen. 

Om du vill visa vilka behörigheter du har i prenumerationen öppnar du Azure-portalen, väljer användarnamnet i det övre högra hörnet och sedan väljer du **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 och 2017
I den här självstudien använder du en lokal SQL Server-databas som *källdatalager*. Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Azure Blob Storage (mottagare). Skapa sedan en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen. 

1. Starta SQL Server Management Studio. Om det inte redan är installerat på datorn öppnar du [Ladda ner SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Anslut till SQL Server-instansen med hjälp av dina autentiseringsuppgifter. 

3. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och sedan väljer du **Ny databas**. 
 
4. I fönstret **Ny databas** anger du ett namn för databasen och sedan väljer du **OK**. 

5. Skapa tabellen **emp** och infoga lite exempeldata i den genom att köra följande frågeskript mot databasen:

   ```
       INSERT INTO emp VALUES ('John', 'Doe')
       INSERT INTO emp VALUES ('Jane', 'Doe')
       GO
   ```

6. I trädvyn högerklickar du på databasen du skapade och sedan väljer du **Ny fråga**.

### <a name="azure-storage-account"></a>Azure-lagringskonto
I den här självstudien använder du ett allmänt Azure Storage-konto (Azure Blob Storage för att vara exakt) som datalager för destination/mottagare. Om du inte har något allmänt Azure Storage-konto kan du läsa [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Azure Blob Storage (mottagare). 

#### <a name="get-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för Azure Storage-kontot i den här självstudien. Hämta namnet och nyckeln till lagringskontot genom att göra följande: 

1. Logga in på [Azure Portal](https://portal.azure.com) med användarnamnet och lösenordet för Azure. 

2. I det vänstra fönstret väljer du **Fler tjänster**, filtrerar genom att använda nyckelordet **Lagring** och sedan väljer du **Lagringskonton**.

    ![Sök efter lagringskontot](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton och välj sedan ditt lagringskonto. 

4. I fönstret **Lagringskonto** väljer du **Åtkomstnycklar**.

    ![Hämta lagringskontots namn och nyckel](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. I rutorna **Lagringskontonamn** och **key1** kopierar du värdena och klistrar sedan in dem i Anteckningar eller annat redigeringsprogram så att du har dem när du behöver dem senare i självstudien. 

#### <a name="create-the-adftutorial-container"></a>Skapa behållaren adftutorial 
I det här avsnittet skapar du en blobbehållare med namnet **adftutorial** i Azure Blob Storage. 

1. I fönstret **Lagringskonto** växlar du till **Översikt** och klickar sedan på **Blobar**. 

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. I fönstret **Blobtjänst** väljer du **Behållare**. 

    ![Lägga till behållarknapp](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. I fönstret **Ny behållare**, i rutan **Namn**, anger du **adftutorial** och väljer **OK**. 

    ![Ange namn på behållare](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Välj **adftutorial** i listan över behållare.  

    ![Välja behållaren](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Låt **behållarfönstret** för **adftutorial** vara öppet. Du kommer att använda den för att bekräfta utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här behållaren, så du behöver inte skapa en.

    ![Behållarfönster](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Installera Azure PowerShell
Installera den senaste versionen av Azure PowerShell om du inte redan har den på din dator. 

1. Öppna [Azure SDK Downloads](https://azure.microsoft.com/downloads/). 

2. Under **Kommandoradsverktyg**, i avsnittet **PowerShell** väljer du **Windows-installation**. 

3. Kör MSI-filen för att installera Azure PowerShell. 

Mer detaljerade anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Logga in på PowerShell

1. Starta PowerShell på datorn och låt det vara öppet tills den här självstudien har slutförts. Om du stänger och öppnar det igen behöver du köra dessa kommandon en gång till.

    ![Starta PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)

2. Kör följande kommando och ange sedan användarnamnet och lösenordet för Azure som du använder för att logga in på Azure-portalen:
       
    ```powershell
    Connect-AzureRmAccount
    ```        

3. Om du har flera Azure-prenumerationer kör du följande kommando för att välja den prenumeration du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommando till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md) (sätt dubbla citattecken omkring namnet; till exempel `"adfrg"`) och kör sedan kommandot. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

2. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.

3. Definiera en variabel för datafabrikens namn som du kan använda senare i PowerShell-kommandon. Namnet måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).

    > [!IMPORTANT]
    >  Uppdatera datafabrikens namn med ett unikt globalt namn. Ett exempel är ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

4. Definiera en variabel för datafabrikens plats: 

    ```powershell
    $location = "East US"
    ```  

5. Skapa datafabriken genom att köra följande `Set-AzureRmDataFactoryV2`-cmdlet: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * Namnet på datafabriken måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara tilldelad en *deltagare* eller *ägare*, eller vara en *administratör* för Azure-prenumerationen.
> * För närvarande kan du endast skapa datafabriker med Data Factory version 2 i regionerna USA, östra, USA östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (Azure HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime

I det här avsnittet kan du skapa en lokal Integration Runtime och koppla den till en lokal dator med SQL Server-databasen. En lokal Integration Runtime är den komponent som kopierar data från SQL Server-databasen på din dator till Azure Blob Storage. 

1. Skapa en variabel för namnet på din Integration Runtime. Använd ett unikt namn och notera namnet. Du använder det senare i den här självstudien. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

2. Skapa Integration Runtime med egen värd. 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 
    Här är exempel på utdata:

    ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       : selfhosted IR description
    ```

3. Kör följande kommando för att hämta statusen för din skapade Integration Runtime:

    ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Här är exempel på utdata:
    
    ```json
    Nodes                     : {}
    CreateTime                : 9/14/2017 10:01:21 AM
    InternalChannelEncryption :
    Version                   :
    Capabilities              : {}
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    AutoUpdate                :
    ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
    ResourceGroupName         : <ResourceGroup name>
    DataFactoryName           : <DataFactory name>
    Name                      : <Integration Runtime name>
    State                     : NeedRegistration
    ```

4. Kör följande kommando för att hämta *autentiseringsnycklarna* för att registrera en lokal Integration Runtime med Data Factory-tjänsten i molnet. Kopiera en av nycklarna (uteslut de dubbla citattecknen) för att registrera den lokala installation av Integration Runtime som du installerar på datorn i nästa steg. 

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```
    
    Här är exempel på utdata:
    
    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Installera Integration Runtime
1. Ladda ned [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) på en lokal Windows-dator och kör sedan installationen. 

2. På **välkomstskärmen till installationsguiden för Microsoft Integration Runtime** klickar du på **Nästa**.  

3. I fönstret med **licensavtalet** godkänner du villkoren och licensavtalet och väljer **Nästa**. 

4. I fönstret **Målmapp** väljer du **Nästa**. 

5. I fönstret **Klar att installera Microsoft Integration Runtime** väljer du **Installera**. 

6. Om du ser ett varningsmeddelande om att datorn håller på att konfigureras för att sedan övergå i strömsparläge eller viloläge när den inte används, väljer du **OK**. 

7. Om fönstret **Energialternativ** öppnas stänger du det och växlar till konfigurationsfönstret. 

8. I **installationsguiden för Microsoft Integration Runtime**  väljer du **Slutför**.

9. Klistra in den nyckel som du sparade i föregående avsnitt i fönstret **Registrera Integration Runtime (lokal)** och klicka sedan på **Registrera**. 

    ![Registrera Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

    När den lokala installationen av Integration Runtime har registrerats ser du följande meddelande: 

    ![Registered successfully (Registrerat)](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

10. I fönstret **Ny nod för Integration Runtime (lokal)** väljer du **Nästa**. 

    ![Fönstret Ny nod för Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

11. I fönstret **Kommunikationskanal för intranät** väljer du **Hoppa över**.  
    Du kan välja ett TLS/SSL-certifikat för att skydda kommunikationen mellan noderna i en miljö med flera noder för Integration Runtime.

    ![Fönstret Kommunikationskanal för intranät](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)

12. I fönstret **Registrera Integration Runtime (lokal)** väljer du **Starta Konfigurationshanteraren**. 

13. Följande meddelande visas när noden är ansluten till molntjänsten:

    ![Node is connected (Noden är ansluten)](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

14. Testa anslutningen till din SQL Server-databas genom att göra följande:

    ![Fliken Diagnostik](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    a. I fönstret **Configuration Manager** växlar du till fliken **Diagnostik**.

    b. I rutan **Typ av datakälla** väljer du **SqlServer**.

    c. Ange servernamnet.

    d. Ange namnet på databasen. 

    e. Välj autentiseringsläge. 

    f. Ange användarnamnet. 

    g. Ange lösenordet som är kopplat till användarnamnet.

    h. Klicka på **Test** för att bekräfta att Integration Runtime kan ansluta till SQL Server.  
    Du ser en grön bockmarkering om anslutningen är upprättad. Annars ser du ett felmeddelande som är kopplat till felet. Åtgärda eventuella problem och se till att Integration Runtime kan ansluta till din SQL Server-instans.

    Notera alla föregående värden. Du behöver dem senare i den här självstudien.
    
## <a name="create-linked-services"></a>Skapa länkade tjänster
Skapa länkade tjänster i datafabriken för att länka dina datalager och beräkningstjänster till datafabriken. I den här självstudien länkar du ditt Azure Storage-konto och din lokala SQL Server-instans till datalagret. De länkade tjänsterna har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till dem. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Skapa en länkad Azure Storage-tjänst (destination/mottagare)
I det här steget länkar du ditt Azure Storage-konto till datafabriken.

1. Skapa en JSON-fil med namnet *AzureStorageLinkedService.json* i mappen *C:\ADFv2Tutorial* med följande kod. Skapa mappen *ADFv2Tutorial* om den inte redan finns.  

    > [!IMPORTANT]
    > Innan du sparar filen ersätter du \<accountName> och \<accountKey med namnet och nyckeln för ditt Azure Storage-konto. Du noterade dem i avsnittet [Krav](#get-storage-account-name-and-account-key).

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. I PowerShell växlar du till mappen *C:\ADFv2Tutorial*.

3. För att skapa den länkade tjänsten, AzureStorageLinkedService, kör du följande `Set-AzureRmDataFactoryV2LinkedService`-cmdlet: 

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Här är exempel på utdata:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

    Om du får felmeddelandet ”Filen hittades inte” kontrollerar du att filen finns genom att köra `dir`-kommandot. Om filnamnet har tillägget *.txt* (till exempel AzureStorageLinkedService.json.txt) tar du bort den och kör sedan PowerShell-kommandot igen. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Skapa och kryptera en SQL Server som är länkad tjänst (källa)
I det här steget länkar du din lokala SQL Server-instans till datafabriken.

1. Skapa en JSON-fil med namnet *SqlServerLinkedService.json* i mappen *C:\ADFv2Tutorial* genom att använda följande kod:

    > [!IMPORTANT]
    > Välj rätt avsnitt baserat på vilken autentisering du använder när du ansluter till SQL Server.

    **Anslut med SQL-autentisering (sa):**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    

    **Använd Windows-autentisering:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```

    > [!IMPORTANT]
    > - Välj avsnitt baserat på vilken autentisering du använder när du ansluter till din SQL Server-instans.
    > - Ersätt  **\<integration runtime name>** med namnet på din Integration Runtime.
    > - Ersätt **\<servername>**, **\<databasename>**, **\<username>** och **\<password>** med värdena för din SQL Server-instans innan du sparar filen.
    > - Om du behöver använda ett omvänt snedstreck (\\) i ditt användarkonto eller servernamn infogar du escape-tecknet framför det (\\). Använd till exempel *mydomain\\\\myuser*. 

2. Kör `New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential`-cmdlet för att kryptera känsliga data (användarnamn, lösenord och så vidare).  
    Den här krypteringen ser till att autentiseringsuppgifterna krypteras med Data Protection Application Programming Interface (DPAPI). Krypterade autentiseringsuppgifter lagras lokalt på en IR-nod med egen värd (lokal dator). Nyttolasten i utdata kan omdirigeras till en annan JSON-fil (i det här fallet *encryptedLinkedService.json*) som innehåller krypterade autentiseringsuppgifter.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Kör följande kommando, vilket skapar EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar för indata och utdata. De representerar indata och utdata för kopieringsåtgärden, som kopierar data från den lokala SQL Server-databasen till Azure Blob Storage.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Skapa en datauppsättning för SQL Server-databaskällan
I det här steget definierar du en datauppsättning som representerar data i SQL Server-databasen. Datauppsättningen är av typen SqlServerTable. Den refererar till den SQL Server-länkade tjänst som du skapade i föregående steg. De länkade tjänsterna har anslutningsinformation som Data Factory-tjänsten använder för att ansluta till din SQL Server-instans vid körning. Den här datauppsättningen anger den SQL-tabell i databasen som innehåller data. I den här självstudien är det tabellen **emp** som innehåller datakällan. 

1. Skapa en JSON-fil med namnet *SqlServerDataset.json* i mappen *C:\ADFv2Tutorial* med följande kod:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. För att skapa datauppsättningen SqlServerDataset kör du `Set-AzureRmDataFactoryV2Dataset`-cmdlet.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Här är exempel på utdata:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Skapa en datauppsättning för Azure Blob Storage (mottagare)
I det här steget definierar du en datauppsättning som representerar data som ska kopieras till Azure Blob Storage. Datauppsättningen är av typen AzureBlob. Den refererar till den Azure Storage-länkade tjänst som du skapade tidigare i den här självstudien. 

Den länkade tjänsten har anslutningsinformationen som datafabriken använder vid körning för att ansluta till ditt Azure Storage-konto. Den här datauppsättningen anger den mapp i Azure-lagringen till vilken data kopieras från SQL Server-databasen. I den här självstudien är mappen *adftutorial/fromonprem*, där `adftutorial` är blobbehållaren och `fromonprem` är mappen. 

1. Skapa en JSON-fil med namnet *AzureBlobDataset.json* i mappen *C:\ADFv2Tutorial* med följande kod:

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. För att skapa datauppsättningen AzureBlobDataset kör du `Set-AzureRmDataFactoryV2Dataset`-cdmlet.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Här är exempel på utdata:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Skapa en pipeline
I den här självstudien kan du skapa en pipeline med en kopieringsaktivitet. Kopieringsaktiviteten använder SqlServerDataset som inkommande datamängd och AzureBlobDataset som utgående datamängd. Källtypen är inställd på *SqlSource* och mottagartypen är inställd på *BlobSink*.

1. Skapa en JSON-fil med namnet *SqlServerToBlobPipeline.json* i mappen *C:\ADFv2Tutorial* med följande kod:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Kör `Set-AzureRmDataFactoryV2Pipeline`-cmdlet för att skapa pipelinen SqlServerToBlobPipeline.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Här är exempel på utdata:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning
Starta en pipelinekörning för pipelinen SQLServerToBlobPipeline och avbilda pipelinekörningens ID för framtida övervakning.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Kör följande skript i PowerShell för att kontinuerligt kontrollera körningsstatusen för pipelinen SQLServerToBlobPipeline, och skriv ut slutresultatet:

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Här är utdata från exempelkörningen:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

2. Du kan få körnings-ID:t för pipelinen SQLServerToBlobPipeline, och kontrollera aktivitetens detaljerade körningsresultat genom att köra följande kommando: 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Här är utdata från exempelkörningen:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```

## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen med namnet *fromonprem* i `adftutorial`-blobbehållaren. Bekräfta att filen *dbo.emp.txt* finns i utdatamappen. 

1. I behållarfönstret **adftutorial** i Azure-portalen väljer du **Uppdatera** för att se utdatamappen.

    ![Utdatamapp skapad](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. Välj `fromonprem` i listan över mappar. 
3. Bekräfta att du ser en fil med namnet `dbo.emp.txt`.

    ![Utdatafil](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage. 
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Se [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) för att få en lista över datalager som stöds av datafabriken.

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data i molnet](tutorial-bulk-copy.md)
