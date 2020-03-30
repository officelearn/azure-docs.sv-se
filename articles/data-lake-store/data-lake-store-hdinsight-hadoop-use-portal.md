---
title: Skapa Azure HDInsight-kluster med Data Lake Storage Gen1 - portal
description: Använda Azure-portalen för att skapa och använda HDInsight-kluster med Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265576"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 med hjälp av Azure-portalen

> [!div class="op_single_selector"]
> * [Använda Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använda PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använda PowerShell (för ytterligare lagringsutrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använda Resurshanteraren](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure-portalen för att skapa ett HDInsight-kluster med ett Azure Data Lake Storage Gen1-konto som standardlagring eller ytterligare lagring. Även om ytterligare lagringsutrymme är valfritt för ett HDInsight-kluster rekommenderar vi att du lagrar dina affärsdata i de ytterligare lagringskontona.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du se till att du har uppfyllt följande krav:

* **En Azure-prenumeration**. Gå till [Hämta kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Data Lake Storage Gen1-konto**. Följ instruktionerna från [Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure-portalen](data-lake-store-get-started-portal.md). Du måste också skapa en rotmapp på kontot.  I den här artikeln används en rotmapp med namnet __/clusters.__
* **Ett huvudnamn för Tjänsten Azure Active Directory**. Den här instruktionsguiden innehåller instruktioner om hur du skapar ett tjänsthuvudnamn i Azure Active Directory (Azure AD). Om du vill skapa ett tjänsthuvudnamn måste du dock vara administratör för Azure AD. Om du är administratör kan du hoppa över den här förutsättningen och fortsätta.

>[!NOTE]
>Du kan bara skapa ett tjänsthuvudnamn om du är Azure AD-administratör. Din Azure AD-administratör måste skapa ett tjänsthuvudnamn innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Dessutom måste tjänstens huvudnamn skapas med ett certifikat, enligt beskrivningen på [Skapa ett huvudnamn för tjänsten med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Skapa ett HDInsight-kluster

I det här avsnittet skapar du ett HDInsight-kluster med Data Lake Storage Gen1-konton som standard eller ytterligare lagring. Den här artikeln fokuserar bara på den del av konfigurera Data Lake Storage Gen1-konton. Information och procedurer för att skapa det allmänna klustret finns [i Skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Skapa ett kluster med Data Lake Storage Gen1 som standardlagring

Så här skapar du ett HDInsight-kluster med ett Data Lake Storage Gen1-konto som standardlagringskonto:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Följ [Skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) för allmän information om hur du skapar HDInsight-kluster.
3. Välj **Azure Data Lake Storage Gen1**under Primär **lagringstyp**på **bladet Lagring** och ange sedan följande information:

    ![Lägg till tjänsthuvudnamn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Lägg till tjänsthuvudnamn i HDInsight-kluster")

    * **Välj datasjölagringskonto:** Välj ett befintligt DataSjölagringsgenm1-konto. Ett befintligt DataSjölagringsgenm1-konto krävs.  Se [Förutsättningar](#prerequisites).
    * **Rotsökväg:** Ange en sökväg där de klusterspecifika filerna ska lagras. På skärmdumpen är det __/clusters/myhdiadlcluster/__, där __mappen /clusters__ måste finnas, och portalen skapar *myhdicluster-mappen.*  Den *myhdicluster* är klustret namn.
    * **Åtkomst till Datasjölagring:** Konfigurera åtkomst mellan DataSjölagringsgenm1-kontot och HDInsight-klustret. Instruktioner finns i [Konfigurera datasjölagring gen1-åtkomst](#configure-data-lake-storage-gen1-access).
    * **Ytterligare lagringskonton**: Lägg till Azure-lagringskonton som ytterligare lagringskonton för klustret. Om du vill lägga till ytterligare Data Lake Storage Gen1-konton görs genom att klusterbehörigheten för data i fler Data Lake Storage Gen1-konton konfigureras samtidigt som ett DataSjölagringsgenm1-konto konfigureras som primär lagringstyp. Se [Konfigurera datasjölagringgend1-åtkomst](#configure-data-lake-storage-gen1-access).

4. Klicka på **Markera**i **datasjölagringsåtkomsten**och fortsätt sedan med att skapa kluster enligt beskrivningen i [Skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Skapa ett kluster med Data Lake Storage Gen1 som ytterligare lagring

Följande instruktioner skapar ett HDInsight-kluster med ett Azure-lagringskonto som standardlagring och ett Data Lake Storage Gen1-konto som ytterligare lagring.

Så här skapar du ett HDInsight-kluster med ett Data Lake Storage Gen1-konto som ett ytterligare lagringskonto:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Följ [Skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) för allmän information om hur du skapar HDInsight-kluster.
3. Välj **Azure Storage**under **Primär lagringstyp**på **bladet Lagring** och ange sedan följande information:

    ![Lägg till tjänsthuvudnamn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Lägg till tjänsthuvudnamn i HDInsight-kluster")

    * **Urvalsmetod** - Om du vill ange ett lagringskonto som ingår i din Azure-prenumeration väljer du **Mina prenumerationer**och väljer sedan lagringskontot. Om du vill ange ett lagringskonto som ligger utanför din Azure-prenumeration väljer du **Access-nyckel**och tillhandahåller sedan informationen för det externa lagringskontot.

    * **Standardbehållare** - Använd antingen standardvärdet eller ange ditt eget namn.
    * **Ytterligare lagringskonton** – Lägg till fler Azure-lagringskonton som ytterligare lagring.
    * **Datasjölagringsåtkomst** – Konfigurera åtkomst mellan DataSjölagringsgenm1-kontot och HDInsight-klustret. Instruktioner finns i [Konfigurera åtkomst för lagring av datasjölagring.](#configure-data-lake-storage-gen1-access)

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurera åtkomst till DataSjölagring Gen1

I det här avsnittet konfigurerar du DataSjölagring Gen1-åtkomst från HDInsight-kluster med hjälp av ett huvudnamn för Tjänsten Azure Active Directory.

### <a name="specify-a-service-principal"></a>Ange ett huvudnamn för tjänsten

Från Azure-portalen kan du antingen använda ett befintligt tjänsthuvudnamn eller skapa ett nytt.

Så här skapar du ett tjänsthuvudnamn från Azure-portalen:

1. Välj **Datasjölagringsåtkomst** från lagringsbladet.
1. Välj **Skapa nytt**på **åtkomstbladet DataSjölagring Gen1** .
1. Välj **Tjänstens huvudnamn**och följ sedan instruktionerna för att skapa ett huvudnamn för tjänsten.
1. Hämta certifikatet om du bestämmer dig för att använda det igen i framtiden. Det är användbart att hämta certifikatet om du vill använda samma huvudnamn när du skapar ytterligare HDInsight-kluster.

    ![Lägg till tjänsthuvudnamn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Lägg till tjänsthuvudnamn i HDInsight-kluster")

1. Välj **Access** för att konfigurera mappåtkomsten.  Se [Konfigurera filbehörigheter](#configure-file-permissions).

Så här använder du ett befintligt tjänsthuvudnamn från Azure-portalen:

1. Välj **åtkomst till Datasjölagring**.
1. Välj **Data Lake Storage Gen1 access** **Använd befintlig**.
1. Välj **Tjänstens huvudnamn**och välj sedan ett huvudnamn för tjänsten.
1. Ladda upp certifikatet (.pfx-filen) som är associerad med ditt valda tjänsthuvudnamn och ange sedan certifikatlösenordet.

    ![Lägg till tjänsthuvudnamn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Lägg till tjänsthuvudnamn i HDInsight-kluster")

1. Välj **Access** för att konfigurera mappåtkomsten.  Se [Konfigurera filbehörigheter](#configure-file-permissions).

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Konfigurera filbehörigheter

Konfigurationen är olika beroende på om kontot används som standardlagring eller ett ytterligare lagringskonto:

* Används som standardlagring

  * behörighet på rotnivån för datasjölagring gen1-kontot
  * behörighet på rotnivån för HDInsight-klusterlagringen. Mappen __/clusters__ som användes tidigare i självstudien.

* Använd som ytterligare lagring

  * Behörighet i de mappar där du behöver filåtkomst.

Så här tilldelar du behörighet på datasjölagringsgenm1-kontorotnivå:

1. Välj **Access**i **accessbladet Data Lake Storage Gen1** . Bladet **Välj filbehörigheter** öppnas. Den listar alla Data Lake Storage Gen1-konton i din prenumeration.
1. Hovra (klicka inte) musen över namnet på datasjölagring gen1-konto för att göra kryssrutan synlig och markera sedan kryssrutan.

    ![Lägg till tjänsthuvudnamn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Lägg till tjänsthuvudnamn i HDInsight-kluster")

   Som standard är __LÄS__, __SKRIV__OCH __KÖR__ alla markerade.

1. Klicka på **Markera** längst ned på sidan.
1. Välj **Kör** om du vill tilldela behörighet.
1. Välj **Done** (Klar).

Så här tilldelar du behörighet på HDInsight-klusterrotnivån:

1. Välj **Access**i **accessbladet Data Lake Storage Gen1** . Bladet **Välj filbehörigheter** öppnas. Den listar alla Data Lake Storage Gen1-konton i din prenumeration.
1. I bladet **Välj filbehörigheter** väljer du kontonamnet Data Lake Storage Gen1 för att visa innehållet.
1. Markera HDInsight-klusterlagringsroten genom att markera kryssrutan till vänster om mappen. Enligt skärmbilden tidigare är __klusterlagringsroten /klustermapp__ som du angav när du valde Data Lake Storage Gen1 som standardlagring.
1. Ange behörigheter för mappen.  Som standard är läsa, skriva och köra alla markerade.
1. Klicka på **Markera** längst ned på sidan.
1. Välj **Kör**.
1. Välj **Done** (Klar).

Om du använder Data Lake Storage Gen1 som ytterligare lagring måste du endast tilldela behörighet för de mappar som du vill komma åt från HDInsight-klustret. I skärmbilden nedan ger du till exempel endast åtkomst till **mynewfolder-mappen** i ett Data Lake Storage Gen1-konto.

![Tilldela behörigheter för tjänstens huvudnamn till HDInsight-klustret](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Tilldela behörigheter för tjänstens huvudnamn till HDInsight-klustret")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Verifiera klusterkonfiguration

När klusterkonfigurationen är klar kontrollerar du resultaten på klusterbladet genom att göra något av följande eller båda av följande steg:

* Om du vill kontrollera att den associerade lagringen för klustret är det DataSjölagringsgenm1-konto som du har angett väljer du **Lagringskonton** i den vänstra rutan.

    ![Lägg till tjänsthuvudnamn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Lägg till tjänsthuvudnamn i HDInsight-kluster")

* Om du vill kontrollera att tjänstens huvudnamn är korrekt associerat med HDInsight-klustret väljer du **DataSjölagring gen1-åtkomst** i den vänstra rutan.

    ![Lägg till tjänsthuvudnamn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Lägg till tjänsthuvudnamn i HDInsight-kluster")

## <a name="examples"></a>Exempel

När du har konfigurerat klustret med Data Lake Storage Gen1 som lagring läser du de här exemplen på hur du använder HDInsight-kluster för att analysera data som lagras i Gen1 för lagring av datasjö.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Köra en Hive-fråga mot data i ett Data Lake Storage Gen1-konto (som primär lagring)

Om du vill köra en Hive-fråga använder du gränssnittet För Hive-vyer i Ambari-portalen. Instruktioner om hur du använder Ambari Hive-vyer finns i [Använda Hive View med Hadoop i HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

När du arbetar med data i ett Data Lake Storage Gen1-konto finns det några strängar att ändra.

Om du till exempel använder klustret som du skapade med Data Lake Storage Gen1 som primär lagring är sökvägen till data: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file*. En Hive-fråga för att skapa en tabell från exempeldata som lagras i datasjölagringsgenm1-kontot ser ut som följande uttryck:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Beskrivningar:

* `adl://hdiadlsg1storage.azuredatalakestore.net/`är roten till Data Lake Storage Gen1-kontot.
* `/clusters/myhdiadlcluster`är roten till de klusterdata som du angav när du skapade klustret.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`är platsen för exempelfilen som du använde i frågan.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Kör en Hive-fråga mot data i ett Data Lake Storage Gen1-konto (som ytterligare lagringsutrymme)

Om klustret som du skapade använder Blob-lagring som standardlagring finns exempeldata inte i kontot DataSjölagring gen1 som används som ytterligare lagring. I så fall överför du först data från Blob-lagring till datasjölagringsgenm1-kontot och kör sedan de frågor som visas i föregående exempel.

Information om hur du kopierar data från Blob-lagring till ett Data Lake Storage Gen1-konto finns i följande artiklar:

* [Använda Distcp för att kopiera data mellan Azure Storage-blobbar och DataSjölagringgeny1](data-lake-store-copy-data-wasb-distcp.md)
* [Använda AdlCopy för att kopiera data från Azure Storage-blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Använda DataSjölagringsgend1 med ett Spark-kluster

Du kan använda ett Spark-kluster för att köra Spark-jobb på data som lagras i ett Data Lake Storage Gen1-konto. Mer information finns i [Använda HDInsight Spark-kluster för att analysera data i Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Använd Data Lake Storage Gen1 i en stormtopologi

Du kan använda datasjölagringsgenm1-kontot för att skriva data från en Storm-topologi. Instruktioner om hur du uppnår det här scenariot finns i [Använda Azure Data Lake Storage Gen1 med Apache Storm med HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Se även

* [Använda DataSjölagringgend1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
