---
title: "Kopiera data från SQL Server till Blob Storage med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från ett lokalt datalager till Azure-molnet med hjälp av IR med egen värd i Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: ca8e664ff1fd509d0461b6d167f28743d2e1e69c
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Självstudier: Kopiera data från SQL Server lokalt till Azure Blob Storage
I den här självstudien använder du Azure PowerShell för att skapa en Data Factory-pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage. Du skapar och använder en lokal installation av Integration Runtime som flyttar data mellan lokala datalager och datalager i molnet. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md). 

I den här självstudiekursen får du göra följande:

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
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen. I Azure-portalen klickar du på ditt **användarnamn** i det övre högra hörnet och väljer **Behörigheter** för att visa de behörigheter du har i prenumerationen. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-201420162017"></a>SQL Server 2014/2016/2017
Du använder en lokal SQL Server-databas som **källdata** i den här självstudien. Pipelinen du skapar på en datafabrik kopierar data från den här lokala SQL Server-databasen (källa) till Azure Blob Storage (mottagare). Skapa en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen. 

1. Öppna **SQL Server Management Studio** på datorn. Om du inte har SQL Server Management Studio på din dator installerar du det via [Download Center](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Anslut till SQL-servern med hjälp av dina autentiseringsuppgifter. 
3. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och klicka på **Ny databas**. Gå till dialogrutan **Ny databas**, ange ett **namn** för databasen och klicka på **OK**. 
4. Kör följande frågeskript mot databasen, vilket skapar tabellen **emp** och infogar lite exempeldata i den. I trädvyn högerklickar du på **databasen** du har skapat och klickar på **Ny fråga**. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )

    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure-lagringskonto
I den här självstudiekursen använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för **destination/mottagare**. Om du inte har något allmänt Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). Pipelinen du skapar på en datafabrik kopierar data från den här lokala SQL Server-databasen (källa) till Azure Blob Storage (mottagare). 

#### <a name="get-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för Azure Storage-kontot i den här självstudien. Följande procedur innehåller steg för att få fram namnet och nyckeln för ditt lagringskonto. 

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com). Logga in med ditt Azure-användarnamn och lösenord. 
2. Klicka på **Fler tjänster >** i den vänstra menyn, filtrera på nyckelordet **Lagring** och välj **Lagringskonton**.

    ![Sök efter lagringskontot](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton och välj sedan **ditt lagringskonto**. 
4. Gå till sidan **Lagringskonto** väljer du **Åtkomstnycklar** i menyn.

    ![Hämta lagringskontots namn och nyckel](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Kopiera värdena från fälten med **lagringskontots namn** och **nyckel 1** till Urklipp. Klistra in dem i Anteckningar eller något annat redigeringsprogram och spara. Du kan använda lagringskontots namn och nyckel i självstudiekursen. 

#### <a name="create-the-adftutorial-container"></a>Skapa behållaren adftutorial 
Det här avsnittet förutsätter att du har en blobbehållare med namnet **adftutorial** i Azure Blob Storage. 

1. På sidan **Lagringskonto** växlar du till **Översikt** och klickar på **Blobbar**. 

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)
1. På sidan **Blob Service** klickar du på **+ Behållare** i verktygsfältet. 

    ![Lägga till behållarknapp](media/tutorial-hybrid-copy-powershell/add-container-button.png)
3. I dialogrutan **Ny behållare** anger du **adftutorial** som namn och klickar på **OK**. 

    ![Ange namn på behållare](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)
4. Klicka på **adftutorial** i listan över behållare.  

    ![Välja behållaren](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)
5. Låt **behållarsidan** för **adftutorial** vara öppen. Du kommer att använda den för att bekräfta utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här behållaren, så du behöver inte skapa en.

    ![Sidan Container (Behållare)](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>Installera PowerShell
Installera den senaste versionen av PowerShell om du inte redan har den på din dator. 

1. Öppna webbläsaren och gå till sidan [Ladda ned Azure-SDK:er och verktyg](https://azure.microsoft.com/downloads/). 
2. Klicka på **Windows-installation** i avsnittet **Kommandoradsverktyg** -> **PowerShell**. 
3. Kör **MSI**-filen för att installera Azure PowerShell. 

Mer detaljerade anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Logga in på PowerShell

1. Starta **PowerShell** på din dator. Låt PowerShell-fönstret vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

    ![Starta PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)
1. Kör följande kommando och ange användarnamnet och lösenordet som du använder för att logga in på Azure-portalen:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Om du har flera Azure-prenumerationer kör du följande kommando för att visa alla prenumerationer kopplade till det här kontot:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Om du har flera Azure-prenumerationer kör du följande kommando för att välja den prenumeration du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"adfrg"`. 
   
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
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt. Till exempel ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```
1. Definiera en variabel för datafabrikens plats: 

    ```powershell
    $location = "East US"
    ```  
5. Kör följande cmdlet av typen **Set-AzureRmDataFactoryV2** för att skapa en datafabrik: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFv2TutorialDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* För närvarande kan du endast skapa datafabriker i Data Factory version 2 i regionerna USA, östra, USA östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

## <a name="create-a-self-hosted-ir"></a>Skapa IR med egen värd

I det här avsnittet kan du skapa en Integration Runtime med egen värd och koppla den till en lokal dator med SQL Server-databasen. Den lokala installationen av Integration Runtime är den komponent som kopierar data från SQL Server på din dator till Azure Blob Storage. 

1. Skapa en variabel för namnet på din Integration Runtime. Använd ett unikt namn och notera namnet. Du använder det senare i den här självstudien. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Skapa Integration Runtime med egen värd. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Här är exempel på utdata:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Kör följande kommando för att hämta status för din skapade integration runtime.

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

3. Kör följande kommando för att hämta **autentiseringsnycklarna** för att registrera Integration Runtime med tjänsten Data Factory i molnet. 

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
4. Kopiera en av nycklarna (uteslut dubbla citattecken) för att registrera den lokala installation av Integration Runtime som du installerar på datorn i nästa steg.  

## <a name="install-integration-runtime"></a>Installera Integration Runtime
1. [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) Integration Runtime med egen värd på en lokal Windows-dator och kör installationen. 
2. På **välkomstskärmen till installationsguiden för Microsoft Integration Runtime** klickar du på **Nästa**.  
3. På sidan med **licensavtalet** godkänner du villkoren och klickar på **Nästa**. 
4. På sidan **Målmapp** klickar du på **Nästa**. 
5. På sidan **Klar att installera Microsoft Integration Runtime** klickar du på **Installera**. 
6. Om du ser ett varningsmeddelande om att datorn håller på att konfigureras för att övergå i strömsparläge eller viloläge när den inte används, klickar du på **OK**. 
7. Om du ser fönstret **Energialternativ** stänger du det och växlar till konfigurationsfönstret. 
8. På sidan för att **installationsguiden för Microsoft Integration Runtime har slutförts** klickar du på **Slutför**.
9. Klistra in den nyckel som du sparade i föregående avsnitt på sidan **Registrera Integration Runtime (lokal)** och klicka på **Registrera**. 

   ![Registrera Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. När integration runtime med egen värd har registrerats ser du följande meddelande:

   ![Registered successfully (Registrerat)](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. På sidan **Ny nod för Integration Runtime (lokal)** klickar du på **Nästa**. 

    ![Sidan Ny nod för Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. På sidan **Kommunikationskanal för intranät** klickar du på **Hoppa över**. Du kan välja ett TLS/SSL-certifikat för att skydda kommunikationen mellan noderna i en miljö med flera noder för Integration Runtime. 

    ![Sidan Kommunikationskanal för intranät](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. På sidan **Registrera Integration Runtime (lokal)** klickar du på **Starta Konfigurationshanteraren**. 
6. Du ser följande sida när noden är ansluten till molntjänsten:

   ![Node is connected (Noden är ansluten)](media/tutorial-hybrid-copy-powershell/node-is-connected.png)
7. Testa nu anslutningen till din SQL Server-databasen.

    ![Fliken Diagnostik](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    - I fönstret **Configuration Manager** växlar du till fliken **Diagnostik**.
    - Välj**SqlServer** som **typ av datakälla**.
    - Ange **servernamnet**.
    - Ange namnet på **databasen**. 
    - Välj **autentiseringsmetod**. 
    - Ange **användarnamn**. 
    - Ange **lösenord** för användarnamnet.
    - Klicka på **Test** för att bekräfta att Integration Runtime kan ansluta till SQL Server. Du ser en grön bockmarkering om anslutningen är klar. Annars ser du ett felmeddelande som är kopplat till felet. Åtgärda eventuella problem och se till att Integration Runtime kan ansluta till din SQL Server.
    - Anteckna värdena (autentiseringstyp, server, databas, användare, lösenord). Du använder det senare i den här självstudien. 
    
      
## <a name="create-linked-services"></a>Skapa länkade tjänster
Skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här självstudien länkar du ditt Azure Storage-konto och din lokala SQL Server till datalagret. De länkade tjänsterna har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till dem. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Skapa en länkad Azure Storage-tjänst (destination/mottagare)
I det här steget länkar du ditt Azure-lagringskonto till datafabriken.

1. Skapa en JSON-fil med namnet **AzureStorageLinkedService.json** i mappen **C:\ADFv2Tutorial** med följande innehåll: skapa mappen ADFv2Tutorial om den inte redan finns.  

    > [!IMPORTANT]
    > Ersätt &lt;accountName&gt; och &lt;accountKey&gt; med namnet och nyckeln för ditt **Azure Storage-konto** innan du sparar filen. Du skrev ned dem som en del av [kraven](#get-storage-account-name-and-account-key).

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

    Om du använder Anteckningar ska du välja **Alla filer** för det **filformat** som anges i dialogrutan **Spara som**. Annars kan tillägget `.txt` läggas till för filen. Till exempel `AzureStorageLinkedService.json.txt`. Om du skapar en fil i Utforskaren innan du öppnar den i Anteckningar kanske du inte ser tillägget `.txt` eftersom alternativet för att **dölja tillägg för alla kända filtyper** är valt som standard. Ta bort tillägget `.txt` innan du fortsätter till nästa steg. 
2. I **Azure PowerShell** växlar du till mappen **C:\ADFv2Tutorial**.

   Kör cmdleten **Set-AzureRmDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureStorageLinkedService**. 

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

    Gör så här om du ser felmeddelandet ”Filen hittades inte”. Kör kommandot `dir` för att bekräfta att filen finns. Om filnamnet har tillägget `.txt` (till exempel AzureStorageLinkedService.json.txt) tar du bort det och kör sedan PowerShell-kommandot igen. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Skapa och kryptera en SQL Server som är länkad tjänst (källa)
I det här steget länkar du din lokala SQL Server till datafabriken.

1. Skapa en JSON-fil med namnet **SqlServerLinkedService.json** i mappen **C:\ADFv2Tutorial** med följande innehåll: Välj rätt avsnitt baserat på vilken **autentisering** du använder när du ansluter till SQL Server.  

    > [!IMPORTANT]
    > Välj rätt avsnitt baserat på vilken **autentisering** du använder när du ansluter till SQL Server.

    **Om du använder SQL-autentisering (sa) kopierar du följande JSON-definition:**

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
    **Om du använder Windows-autentisering kopierar du följande JSON-definition:**

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
    > - Välj rätt avsnitt baserat på vilken **autentisering** du använder när du ansluter till SQL Server.
    > - Ersätt **&lt;integration** **runtime** **name>** med namnet på din Integration Runtime.
    > - Ersätt **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, och **&lt;password>** med värdena för din SQL Server innan du sparar filen.
    > - Om du behöver använda ett snedstreck (`\`) i ditt användarkonto eller användarnamn använder du escape-tecknet (`\`). Till exempel `mydomain\\myuser`. 

2. För att kryptera känsliga data (användarnamn, lösenord osv.) kör du cmdleten **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**. Den här krypteringen ser till att autentiseringsuppgifterna krypteras med Data Protection Application Programming Interface (DPAPI). Krypterade autentiseringsuppgifter lagras lokalt på en IR-nod med egen värd (lokal dator). Nyttolasten i utdata kan omdirigeras till en annan JSON-fil (i det här fallet ”encryptedLinkedService.json”) som innehåller krypterade autentiseringsuppgifter.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Kör följande kommando, vilket skapar **EncryptedSqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget kan du skapa indata och utdata datauppsättningar som representerar inkommande och utgående data för kopieringen (lokal SQL Server-databas = > Azure Blob Storage).

### <a name="create-a-dataset-for-source-sql-database"></a>Skapa en datauppsättning för SQL Database-källan
I det här steget definierar du en datamängd som representerar data i SQL Server-databasen. Datamängden är av typen **SqlServerTable**. Den refererar till den **SQL Server-länkade tjänst** som du skapade i föregående steg. De länkade tjänsterna har **anslutningsinformationen** som Data Factory-tjänsten använder för att ansluta till din SQL Server vid körning. Den här datamängden anger den **SQL-tabell** i databasen som innehåller data. I den här självstudien är det tabell `emp` som innehåller datakällan. 

1. Skapa en JSON-fil med namnet **SqlServerDataset.json** i mappen **C:\ADFv2Tutorial** med följande innehåll:  

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

2. Så här skapar du datauppsättningen: **SqlServerDataset**, kör cmdleten **Set-AzureRmDataFactoryV2Dataset**.

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

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Skapa en datauppsättning för Azure Blob Storage-mottagaren
I det här steget definierar du en datamängd som representerar data som ska kopieras till Azure Blob Storage. Datamängden är av typen **AzureBlob**. Den refererar till den **Azure Storage-länkade tjänst** som du skapade tidigare i den här självstudien. Den länkade tjänsten har **anslutningsinformationen** som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure Storage-konto. Den här **datamängden** anger den **mapp** i Azure Storage till vilken data kopieras från SQL Server-databasen. I den här självstudien är mappen:  `adftutorial/fromonprem` varav `adftutorial` är blobbehållaren och `fromonprem` är mappen. 

1. Skapa en JSON-fil med namnet **AzureBlobDataset.json** i mappen **C:\ADFv2Tutorial** med följande innehåll:

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

2. Så här skapar du datauppsättningen: **AzureBlobDataset**, kör cmdleten **Set-AzureRmDataFactoryV2Dataset**.

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
I den här självstudien kan du skapa en pipeline med en kopieringsaktivitet. Kopieringsaktiviteten använder **SqlServerDataset** som inkommande datamängd och **AzureBlobDataset** som utgående datamängd. Källtypen är inställt på **SqlSource** och mottagartyp är inställt på **BlobSink**.

1. Skapa en JSON-fil med namnet **SqlServerToBlobPipeline.json** i mappen **C:\ADFv2Tutorial** med följande innehåll:

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

2. Så här skapar du en pipeline: **SQLServerToBlobPipeline**, kör cmdleten **Set-AzureRmDataFactoryV2Pipeline**.

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
Starta en pipelinekörning för "SQLServerToBlobPipeline" och avbilda pipelinekörningens ID för framtida övervakning.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Kör följande skript för att kontrollera körningsstatusen kontinuerligt för pipelinen **SQLServerToBlobPipeline**, och skriv ut slutresultatet. Kopiera/klistra in följande skript i PowerShell-fönstret och tryck på RETUR.

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

3. Du kan få körnings-ID för pipelinen **SQLServerToBlobPipeline**, och kontrollera aktivitetens detaljerade körningsresultat genom att köra följande kommando: 

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
Pipelinen skapar automatiskt utdatamappen med namnet `fromonprem` i blobbehållaren `adftutorial`. Bekräfta att filen **dbo.emp.txt** finns i utdatamappen. 

1. På behållarsidan **adftutorial** i Azure-portalen klickar du på **Uppdatera** för att se utdatamappen.

    ![utdatamapp skapad](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. Klicka på `fromonprem` i listan över mappar. 
3. Bekräfta att du ser en fil med namnet `dbo.emp.txt`.

    ![utdatafil](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage. 
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Se [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) för att få en lista över datalager som stöds av Azure Data Factory.

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data i molnet](tutorial-bulk-copy.md)
