---
title: 'Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster '
description: Lär dig hur du använder Azure Data Lake Storage Gen2 med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272297"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster 

Azure Data Lake Storage Gen2 är en molnlagringstjänst som är dedikerad till stordataanalys, byggd på Azure Blob-lagring. Data Lake Storage Gen2 kombinerar funktionerna för Azure Blob storage och Azure Data Lake Storage Gen1. Den resulterande tjänsten erbjuder funktioner från Azure Data Lake Storage Gen1, till exempel filsystemssenmantik, säkerhet på katalognivå och filnivå och skalbarhet, tillsammans med lågkostnadslagring, nivåinställd lagring, hög tillgänglighet och katastrofåterställningsfunktioner från Azure Blob-lagring.

## <a name="data-lake-storage-gen2-availability"></a>Tillgänglighet för Lagring av datasjö

Data Lake Storage Gen2 är tillgängligt som ett lagringsalternativ för nästan alla Azure HDInsight-klustertyper som både standard- och ett ytterligare lagringskonto. HBase kan dock bara ha ett Data Lake Storage Gen2-konto.

En fullständig jämförelse av alternativ för att skapa kluster med Data Lake Storage Gen2 finns i [Jämför lagringsalternativ för användning med Azure HDInsight-kluster](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> När du har valt DataSjölagring Gen2 som **primär lagringstyp**kan du inte välja ett DataSjölagringsgenm1-konto som ytterligare lagringsutrymme.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Skapa ett kluster med Data Lake Storage Gen2 via Azure-portalen

Om du vill skapa ett HDInsight-kluster som använder Data Lake Storage Gen2 för lagring följer du dessa steg för att konfigurera ett Data Lake Storage Gen2-konto.

### <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användartilldelad hanterad identitet om du inte redan har en.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det övre vänstra klicka på **Skapa en resurs**.
1. Skriv **användaren tilldelade** i sökrutan och klicka på **Användartilldelad hanterad identitet**.
1. Klicka på **Skapa**.
1. Ange ett namn på din hanterade identitet, välj rätt prenumeration, resursgrupp och plats.
1. Klicka på **Skapa**.

Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns [i Hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md).

![Skapa en användartilldelad hanterad identitet](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Skapa ett Data Lake Storage Gen2-konto

Skapa ett Azure Data Lake Storage Gen2-lagringskonto.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det övre vänstra klicka på **Skapa en resurs**.
1. Skriv **lagring** i sökrutan och klicka på **Lagringskonto**.
1. Klicka på **Skapa**.
1. På skärmen **Skapa lagringskonto:**
    1. Välj rätt prenumerations- och resursgrupp.
    1. Ange ett namn på ditt Data Lake Storage Gen2-konto. Mer information om namngivningskonventioner för lagringskonto finns i [Namngivningskonventioner för Azure-resurser](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage).
    1. Klicka på fliken **Avancerat.**
    1. Klicka på **Aktiverad bredvid** **Hierarkiskt namnområde** under **Data Lake Storage Gen2**.
    1. Klicka på **Granska + skapa**.
    1. Klicka på **Skapa**

Mer information om andra alternativ när lagringskonto skapas finns i [Snabbstart: Skapa ett Azure Data Lake Storage Gen2-lagringskonto](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Skärmbild som visar hur lagringskonto skapas i Azure-portalen](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Ställ in behörigheter för den hanterade identiteten på Data Lake Storage Gen2-kontot

Tilldela den hanterade identiteten till rollen **Lagringsblobbdataägare** på lagringskontot.

1. Gå till ditt lagringskonto i [Azure-portalen.](https://portal.azure.com)
1. Välj ditt lagringskonto och välj sedan **Åtkomstkontroll (IAM)** för att visa åtkomstkontrollinställningarna för kontot. Välj fliken **Rolltilldelningar** om du vill visa listan över rolltilldelningar.

    ![Skärmbild som visar inställningar för kontroll av lagringsåtkomst](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Välj knappen **+ Lägg till rolltilldelning** om du vill lägga till en ny roll.
1. Välj rollen **Storage Blob Data Owner** i fönstret Lägg till **rolltilldelning.** Välj sedan den prenumeration som har det hanterade identitets- och lagringskontot. Sök sedan för att hitta den användartilldelade hanterade identitet som du skapade tidigare. Slutligen väljer du den hanterade identiteten och visas under **Valda medlemmar**.

    ![Skärmbild som visar hur du tilldelar en RBAC-roll](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Välj **Spara**. Den användartilldelade identitet som du har valt visas nu under den valda rollen.
1. När den första installationen är klar kan du skapa ett kluster via portalen. Klustret måste finnas i samma Azure-region som lagringskontot. Välj följande alternativ på fliken **Lagring** på menyn för att skapa kluster:

    * För **primär lagringstyp**väljer du **Azure Data Lake Storage Gen2**.
    * Under **Primär lagringskonto**söker du efter och väljer det nyligen skapade lagringsgend2-lagringskontot för DataSjölagring.

    * Under **Identitet**väljer du den nyligen skapade användartilldelade hanterade identiteten.

        ![Lagringsinställningar för att använda Data Lake Storage Gen2 med Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Om du vill lägga till ett sekundärt Data Lake Storage Gen2-konto, på lagringskontonivå, helt enkelt tilldela den hanterade identitet som skapats tidigare till den nya Data Lake Storage Gen2 lagringskonto som du vill lägga till. Observera att det inte stöds att lägga till ett sekundärt Data Lake Storage Gen2-konto via bladet "Ytterligare lagringskonton" på HDInsight.
    > * Du kan aktivera RA-GRS eller RA-ZRS på Azure-lagringskontot som HDInsight använder. Det går dock inte att skapa ett kluster mot den sekundära slutpunkten RA-GRS eller RA-ZRS.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Skapa ett kluster med Data Lake Storage Gen2 via Azure CLI

Du kan [hämta en exempelmallfil](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) och [hämta en exempelparametrarfil](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Innan du använder mallen och Azure CLI-kodavsnittet nedan ersätter du följande platshållare med rätt värden:

| Platshållare | Beskrivning |
|---|---|
| `<SUBSCRIPTION_ID>` | ID:et för din Azure-prenumeration |
| `<RESOURCEGROUPNAME>` | Den resursgrupp där du vill att det nya klustret och lagringskontot ska skapas. |
| `<MANAGEDIDENTITYNAME>` | Namnet på den hanterade identiteten som kommer att få behörighet för ditt Azure Data Lake Storage Gen2-konto. |
| `<STORAGEACCOUNTNAME>` | Det nya Azure Data Lake Storage Gen2-kontot som ska skapas. |
| `<CLUSTERNAME>` | Namnet på ditt HDInsight-kluster. |
| `<PASSWORD>` | Ditt valda lösenord för att logga in i klustret med SSH samt Ambari-instrumentpanelen. |

Kodavsnittet nedan gör följande första steg:

1. Loggar in på ditt Azure-konto.
1. Anger den aktiva prenumerationen där skapa-åtgärderna ska utföras.
1. Skapar en ny resursgrupp för de nya distributionsaktiviteterna.
1. Skapar en användartilldelad hanterad identitet.
1. Lägger till ett tillägg till Azure CLI för att använda funktioner för Data Lake Storage Gen2.
1. Skapar ett nytt Data Lake Storage Gen2-konto med hjälp av `--hierarchical-namespace true` flaggan.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Logga sedan in på portalen. Lägg till den nya användartilldelade hanterade identiteten i rollen **Storage Blob Data Contributor** på lagringskontot, enligt beskrivningen i steg 3 under Använda [Azure-portalen](hdinsight-hadoop-use-data-lake-storage-gen2.md).

När du har tilldelat rollen för den användartilldelade hanterade identiteten distribuerar du mallen med hjälp av följande kodavsnitt.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Skapa ett kluster med Data Lake Storage Gen2 via Azure PowerShell

Att använda PowerShell för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen2 stöds för närvarande inte.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Åtkomstkontroll för Data Lake Storage Gen2 i HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Vilka typer av behörigheter stöder Data Lake Storage Gen2?

Data Lake Storage Gen2 använder en åtkomstkontrollmodell som stöder både rollbaserad åtkomstkontroll (RBAC) och POSIX-liknande åtkomstkontrollistor (ACL). Data Lake Storage Gen1 stöder åtkomstkontrollistor endast för att kontrollera åtkomsten till data.

RBAC använder rolltilldelningar för att effektivt tillämpa behörighetsuppsättningar för användare, grupper och tjänsthuvudnamn för Azure-resurser. Vanligtvis är dessa Azure-resurser begränsade till resurser på den högsta nivån (till exempel Azure Storage-konton). För Azure Storage och även Data Lake Storage Gen2 har den här mekanismen utökats till filsystemresursen.

 Mer information om filbehörigheter med RBAC finns i [Azure Role-based Access Control (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Mer information om filbehörigheter med ACL finns i [Komma åt kontrolllistor för filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hur kontrollerar jag åtkomsten till mina data i Gen2 för lagring av datasjö?

HdInsight-klustrets möjlighet att komma åt filer i Data Lake Storage Gen2 styrs genom hanterade identiteter. En hanterad identitet är en identitet som registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänsthuvudnamn i Azure AD eller underhålla autentiseringsuppgifter som certifikat.

Azure-tjänster har två typer av hanterade identiteter: systemtilldelade och användartilldelade. HDInsight använder användartilldelade hanterade identiteter för att komma åt Data Lake Storage Gen2. En användartilldelad hanterad identitet skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure.

Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för de Azure-tjänstinstanser som den är tilldelad till. Mer information om hanterade identiteter finns i [Hur fungerar hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hur anger jag behörigheter för Azure AD-användare att fråga data i Data Lake Storage Gen2 med hjälp av Hive eller andra tjänster?

Om du vill ange behörigheter för användare att fråga data använder du Azure AD-säkerhetsgrupper som tilldelat huvudnamn i ACL:er. Tilldela inte direkt filåtkomstbehörigheter till enskilda användare eller tjänsthuvudnamn. När du använder Azure AD-säkerhetsgrupper för att styra flödet av behörigheter kan du lägga till och ta bort användare eller tjänsthuvudnamn utan att tillämpa ACL:er på nytt i en hel katalogstruktur. Du behöver bara lägga till eller ta bort användare från lämplig Azure AD-säkerhetsgrupp. ACL:er ärvs inte, så att återanvända åtkomstkontrollistor kräver att ACL uppdateras för varje fil och underkatalog.

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Det finns flera sätt att komma åt filerna i Data Lake Storage Gen2 från ett HDInsight-kluster.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till klusterroten med:

    ```
    abfs:///<file.path>/
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på dataåtkomst

Exempel baseras på en [ssh-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till huvudnoden för klustret. Exemplen använder alla tre URI-scheman. Ersätta `CONTAINERNAME` `STORAGEACCOUNT` och med relevanta värden

#### <a name="a-few-hdfs-commands"></a>Några hdfs-kommandon

1. Skapa en fil på lokal lagring.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger för klusterlagring.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till klusterlagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista kataloginnehåll på klusterlagring.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre filplatser visas för belysande ändamål. För faktisk körning använder du `LOCATION` bara en av posterna.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Nästa steg

* [Azure HDInsight-integrering med förhandsversionen av Data Lake Storage Gen2 – ACL och säkerhetsuppdatering](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Självstudiekurs: Extrahera, transformera och läsa in data med interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
