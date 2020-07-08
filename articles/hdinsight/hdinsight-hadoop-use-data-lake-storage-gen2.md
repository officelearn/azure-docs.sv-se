---
title: Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster
description: Lär dig hur du använder Azure Data Lake Storage Gen2 med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 4a9a3aaecc854ddb710f19bcb3d455c63b3a8bef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84706179"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster

Azure Data Lake Storage Gen2 är en moln lagrings tjänst avsedd för stor data analys som bygger på Azure Blob Storage. Data Lake Storage Gen2 kombinerar funktionerna i Azure Blob Storage och Azure Data Lake Storage Gen1. Den resulterande tjänsten erbjuder funktioner från Azure Data Lake Storage Gen1. Dessa funktioner är: semantiska fil system, säkerhet på katalog nivå och filnivå samt anpassnings förmåga. Tillsammans med låg kostnad, nivå lagring, hög tillgänglighet och katastrof återställning från Azure Blob Storage.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 tillgänglighet

Data Lake Storage Gen2 är tillgängligt som ett lagrings alternativ för nästan alla Azure HDInsight-kluster typer som både standard och ytterligare lagrings konton. HBase kan dock ha endast ett Data Lake Storage Gen2-konto.

En fullständig jämförelse av alternativen för att skapa kluster med hjälp av Data Lake Storage Gen2 finns i [jämföra lagrings alternativ för användning med Azure HDInsight-kluster](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> När du har valt Data Lake Storage Gen2 som **primär lagrings typ**kan du inte välja ett data Lake Storage gen1 konto som ytterligare lagrings utrymme.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Skapa ett kluster med Data Lake Storage Gen2 via Azure Portal

Om du vill skapa ett HDInsight-kluster som använder Data Lake Storage Gen2 för lagring, följer du dessa steg för att konfigurera ett Data Lake Storage Gen2 konto.

### <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användardefinierad hanterad identitet om du inte redan har en.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **skapa en resurs**längst upp till vänster.
1. I rutan Sök skriver du **tilldelade användare** och klickar på **användare tilldelad hanterad identitet**.
1. Klicka på **Skapa**.
1. Ange ett namn för din hanterade identitet, Välj rätt prenumeration, resurs grupp och plats.
1. Klicka på **Skapa**.

Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md).

![Skapa en användartilldelad hanterad identitet](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Skapa ett Data Lake Storage Gen2-konto

Skapa ett Azure Data Lake Storage Gen2-lagringskonto.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **skapa en resurs**längst upp till vänster.
1. Skriv **Storage** i sökrutan och klicka på **lagrings konto**.
1. Klicka på **Skapa**.
1. På skärmen **skapa lagrings konto** :
    1. Välj rätt prenumeration och resurs grupp.
    1. Ange ett namn för ditt Data Lake Storage Gen2-konto.
    1. Klicka på fliken **Avancerat** .
    1. Klicka på **aktive rad** bredvid **hierarkiskt namn område** under **data Lake Storage Gen2**.
    1. Klicka på **Granska + skapa**.
    1. Klicka på **skapa**

Mer information om andra alternativ när du skapar lagrings konton finns i [snabb start: skapa ett Azure Data Lake Storage Gen2 lagrings konto](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Skärm bild som visar hur du skapar lagrings konton i Azure Portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Konfigurera behörigheter för den hanterade identiteten på Data Lake Storage Gen2 kontot

Tilldela den hanterade identiteten till rollen **Storage BLOB data-ägare** på lagrings kontot.

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.
1. Välj ditt lagrings konto och välj sedan **åtkomst kontroll (IAM)** om du vill visa inställningarna för åtkomst kontroll för kontot. Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.

    ![Skärm bild som visar inställningar för åtkomst kontroll för lagring](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Lägg till en ny roll genom att klicka på knappen **+ Lägg till roll tilldelning** .
1. I fönstret **Lägg till roll tilldelning** väljer du rollen **Storage BLOB data-ägare** . Välj sedan den prenumeration som har den hanterade identitets-och lagrings kontot. Sedan söker du efter den användarspecifika hanterade identitet som du skapade tidigare. Välj slutligen den hanterade identiteten så visas den under **valda medlemmar**.

    ![Skärm bild som visar hur du tilldelar en RBAC-roll](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Välj **Spara**. Den användardefinierade identitet som du har valt visas nu under den valda rollen.
1. När den här inledande installationen är klar kan du skapa ett kluster via portalen. Klustret måste finnas i samma Azure-region som lagrings kontot. På fliken **lagring** på menyn skapa kluster väljer du följande alternativ:

    * För **primär lagrings typ**väljer du **Azure Data Lake Storage Gen2**.
    * Under **primärt lagrings konto**söker du efter och väljer det nyligen skapade data Lake Storage Gen2 lagrings kontot.

    * Under **identitet**väljer du den nyligen skapade hanterade identiteten som skapats av användaren.

        ![Lagrings inställningar för att använda Data Lake Storage Gen2 med Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Om du vill lägga till ett sekundärt Data Lake Storage Gen2 konto, på lagrings konto nivå, tilldelar du bara den hanterade identitet som skapats tidigare till det nya Data Lake Storage Gen2 lagrings konto som du vill lägga till. Vi rekommenderar att du lägger till ett sekundärt Data Lake Storage Gen2 konto via bladet "ytterligare lagrings konton" i HDInsight stöds inte.
    > * Du kan aktivera RA-GRS eller RA-ZRS på det Azure Storage-konto som HDInsight använder. Att skapa ett kluster mot den sekundära slut punkten RA-GRS eller RA-ZRS stöds dock inte.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Skapa ett kluster med Data Lake Storage Gen2 via Azure CLI

Du kan [Ladda ned en mall för exempel filen](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) och [Ladda ned en exempel parameter fil](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Innan du använder mallen och kod avsnittet för Azure CLI nedan ersätter du följande plats hållare med rätt värden:

| Platshållare | Beskrivning |
|---|---|
| `<SUBSCRIPTION_ID>` | ID för din Azure-prenumeration |
| `<RESOURCEGROUPNAME>` | Den resurs grupp där du vill att det nya klustret och lagrings kontot ska skapas. |
| `<MANAGEDIDENTITYNAME>` | Namnet på den hanterade identitet som får behörigheter på ditt Azure Data Lake Storage Gen2-konto. |
| `<STORAGEACCOUNTNAME>` | Det nya Azure Data Lake Storage Gen2 kontot som ska skapas. |
| `<CLUSTERNAME>` | Namnet på ditt HDInsight-kluster. |
| `<PASSWORD>` | Ditt lösen ord för att logga in på klustret med SSH och Ambari-instrumentpanelen. |

Kodfragmentet nedan visar följande inledande steg:

1. Loggar in på ditt Azure-konto.
1. Anger den aktiva prenumeration där åtgärderna för att skapa ska utföras.
1. Skapar en ny resurs grupp för de nya distributions aktiviteterna.
1. Skapar en användardefinierad hanterad identitet.
1. Lägger till ett tillägg i Azure CLI för att använda funktioner för Data Lake Storage Gen2.
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

Logga sedan in på portalen. Lägg till den nya användarspecifika hanterade identiteten i rollen **Storage BLOB data Contributor** på lagrings kontot. Det här steget beskrivs i steg 3 under [använda Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

När du har tilldelat rollen för den användarspecifika hanterade identiteten distribuerar du mallen med hjälp av följande kodfragment.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Skapa ett kluster med Data Lake Storage Gen2 via Azure PowerShell

Att använda PowerShell för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen2 stöds inte för närvarande.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Åtkomst kontroll för Data Lake Storage Gen2 i HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Vilka typer av behörigheter Data Lake Storage Gen2 stöd för?

Data Lake Storage Gen2 använder en modell för åtkomst kontroll som stöder både rollbaserad åtkomst kontroll (RBAC) och POSIX-liknande åtkomst kontrol listor (ACL: er). Data Lake Storage Gen1 stöder endast åtkomst kontrol listor för att kontrol lera åtkomst till data.

RBAC använder roll tilldelningar för att effektivt tillämpa uppsättningar av behörigheter för användare, grupper och tjänstens huvud namn för Azure-resurser. Normalt är dessa Azure-resurser begränsade till resurser på den översta nivån (till exempel Azure Storage konton). För Azure Storage och även Data Lake Storage Gen2 har den här mekanismen utökats till fil system resursen.

 Mer information om fil behörigheter med RBAC finns i [Azure rollbaserad åtkomst kontroll (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Mer information om fil behörigheter med ACL: er finns i [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hur gör jag för att kontrol lera åtkomst till mina data i Data Lake Storage Gen2?

Ditt HDInsight-klusters möjlighet att komma åt filer i Data Lake Storage Gen2 styrs genom hanterade identiteter. En hanterad identitet är en identitet som registrerats i Azure Active Directory (Azure AD) vars autentiseringsuppgifter hanteras av Azure. Med hanterade identiteter behöver du inte registrera tjänstens huvud namn i Azure AD. Eller Upprätthåll autentiseringsuppgifter, till exempel certifikat.

Azure-tjänster har två typer av hanterade identiteter: systemtilldelade och tilldelade användare. HDInsight använder användare tilldelade hanterade identiteter för att få åtkomst till Data Lake Storage Gen2. En `user-assigned managed identity` skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure.

Livscykeln för en användartilldelad identitet hanteras separat från livscykeln för de Azure-tjänstinstanser som den är tilldelad till. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hur gör jag för att ställer du in behörigheter för Azure AD-användare att fråga efter data i Data Lake Storage Gen2 med hjälp av Hive eller andra tjänster?

Om du vill ange behörigheter för användare för att fråga data använder du Azure AD-säkerhetsgrupper som tilldelat objekt i ACL: er. Tilldela inte fil åtkomst behörighet till enskilda användare eller tjänst huvud namn direkt. Med Azure AD-säkerhetsgrupper för att kontrol lera flödet av behörigheter kan du lägga till och ta bort användare eller tjänstens huvud namn utan att återställa ACL: er till en hel katalog struktur. Du behöver bara lägga till eller ta bort användarna från rätt Azure AD-säkerhetsgrupp. ACL: er ärvs inte, så om du tillämpar ACL: er igen måste du uppdatera ACL: en på alla filer och under kataloger.

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Det finns flera sätt som du kan använda för att komma åt filerna i Data Lake Storage Gen2 från ett HDInsight-kluster.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till kluster roten med:

    ```
    abfs:///<file.path>/
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på data åtkomst

Exempel baseras på en [ssh-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till klustrets huvud nod. I exemplen används alla tre URI-scheman. Ersätt `CONTAINERNAME` och `STORAGEACCOUNT` med relevanta värden

#### <a name="a-few-hdfs-commands"></a>Några HDFS-kommandon

1. Skapa en fil på lokal lagrings plats.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger i kluster lagringen.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till kluster lagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lista katalog innehåll i kluster lagringen.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre fil platser visas i syfte att illustrera. Använd endast en av posterna för faktisk körning `LOCATION` .

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

* [Azure HDInsight-integrering med Data Lake Storage Gen2 för hands version – ACL och säkerhets uppdatering](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Självstudie: extrahera, transformera och läsa in data med hjälp av interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
