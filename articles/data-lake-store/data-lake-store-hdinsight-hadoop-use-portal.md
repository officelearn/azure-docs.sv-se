---
title: Skapa Azure HDInsight-kluster med Data Lake Storage Gen1-Portal
description: Använd Azure Portal för att skapa och använda HDInsight-kluster med Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: e3e54b037485a85d836e7e7e67c9af2d9d140986
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856820"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 med hjälp av Azure Portal

> [!div class="op_single_selector"]
> * [Använda Azure-portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använd PowerShell (för standard lagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använd PowerShell (för ytterligare lagrings utrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använd Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure Portal för att skapa ett HDInsight-kluster med ett Azure Data Lake Storage Gen1-konto som standard lagring eller ytterligare lagrings utrymme. Även om ytterligare lagring är valfritt för ett HDInsight-kluster rekommenderar vi att du lagrar dina affärs data i de ytterligare lagrings kontona.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrol lera att du uppfyller följande krav:

* **En Azure-prenumeration**. Gå till [få en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett data Lake Storage gen1 konto**. Följ anvisningarna från [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md). Du måste också skapa en rotmapp på kontot.  I den här artikeln används en rotmapp som heter __/Clusters__ .
* **Ett Azure Active Directory tjänstens huvud namn**. Den här instruktions guiden innehåller instruktioner om hur du skapar ett huvud namn för tjänsten i Azure Active Directory (Azure AD). Om du vill skapa ett huvud namn för tjänsten måste du dock vara en Azure AD-administratör. Om du är administratör kan du hoppa över den här förutsättningen och fortsätta.

>[!NOTE]
>Du kan bara skapa ett huvud namn för tjänsten om du är en Azure AD-administratör. Din Azure AD-administratör måste skapa ett huvud namn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Dessutom måste tjänstens huvud namn skapas med ett certifikat, enligt beskrivningen i [skapa ett huvud namn för tjänsten med certifikatet](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Skapa ett HDInsight-kluster

I det här avsnittet skapar du ett HDInsight-kluster med Data Lake Storage Gen1 konton som standard eller ytterligare lagrings utrymme. Den här artikeln fokuserar bara på en del av att konfigurera Data Lake Storage Gen1-konton. Information och procedurer för allmän kluster skapande finns i [skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Skapa ett kluster med Data Lake Storage Gen1 som standard lagring

Så här skapar du ett HDInsight-kluster med ett Data Lake Storage Gen1 konto som standard lagrings konto:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Följ [skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) för allmän information om hur du skapar HDInsight-kluster.
3. Välj **Azure Data Lake Storage gen1**under **primär lagrings typ**på bladet **lagring** och ange följande information:

    ![Lägg till tjänstens huvud namn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Lägg till tjänstens huvud namn i HDInsight-kluster")

    * **Välj Data Lake Store konto**: Välj ett befintligt data Lake Storage gen1-konto. Ett befintligt Data Lake Storage Gen1-konto krävs.  Se [krav](#prerequisites).
    * **Rot Sök väg**: Ange en sökväg där de klusterbaserade filerna ska lagras. På skärm bilden är det __/Clusters/myhdiadlcluster/__, där mappen __/Clusters__ måste finnas, och portalen skapar *myhdicluster* -mappen.  *Myhdicluster* är kluster namnet.
    * **Data Lake Store åtkomst**: konfigurera åtkomst mellan data Lake Storage gen1 konto och HDInsight-kluster. Instruktioner finns i [konfigurera data Lake Storage gen1 åtkomst](#configure-data-lake-storage-gen1-access).
    * **Ytterligare lagrings konton**: Lägg till Azure Storage-konton som ytterligare lagrings konton för klustret. Om du vill lägga till ytterligare Data Lake Storage Gen1 konton gör du det genom att ge kluster behörigheterna för data i fler Data Lake Storage Gen1-konton när du konfigurerar ett Data Lake Storage Gen1-konto som primär lagrings typ. Se [konfigurera data Lake Storage gen1 åtkomst](#configure-data-lake-storage-gen1-access).

4. Klicka på **Välj**på **data Lake Store åtkomst**och fortsätt sedan med att skapa kluster genom att följa anvisningarna i [skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Skapa ett kluster med Data Lake Storage Gen1 som ytterligare lagrings utrymme

Följande instruktioner skapar ett HDInsight-kluster med ett Azure Storage-konto som standard lagrings utrymme och ett Data Lake Storage Gen1 konto som ytterligare lagrings utrymme.

Så här skapar du ett HDInsight-kluster med ett Data Lake Storage Gen1 konto som ett ytterligare lagrings konto:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Följ [skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) för allmän information om hur du skapar HDInsight-kluster.
3. Välj **Azure Storage**under **primär lagrings typ**på bladet **lagring** och ange följande information:

    ![Lägg till tjänstens huvud namn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Lägg till tjänstens huvud namn i HDInsight-kluster")

    * **Urvals metod** – om du vill ange ett lagrings konto som ingår i din Azure-prenumeration väljer du **Mina prenumerationer**och väljer sedan lagrings kontot. Om du vill ange ett lagrings konto som ligger utanför din Azure-prenumeration väljer du **åtkomst nyckel**och anger sedan informationen för det externa lagrings kontot.

    * **Standard behållare** – Använd antingen standardvärdet eller ange ett eget namn.
    * **Ytterligare lagrings konton** – Lägg till fler Azure Storage-konton som ytterligare lagrings utrymme.
    * **Data Lake Store åtkomst** – konfigurera åtkomst mellan data Lake Storage gen1 konto och HDInsight-kluster. Anvisningar finns i [konfigurera data Lake Storage gen1 åtkomst](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurera Data Lake Storage Gen1 åtkomst

I det här avsnittet konfigurerar du Data Lake Storage Gen1 åtkomst från HDInsight-kluster med hjälp av ett Azure Active Directory tjänstens huvud namn.

### <a name="specify-a-service-principal"></a>Ange ett huvud namn för tjänsten

Från Azure Portal kan du antingen använda ett befintligt huvud namn för tjänsten eller skapa ett nytt.

Så här skapar du ett huvud namn för tjänsten från Azure Portal:

1. Välj **data Lake Store åtkomst** från bladet lagring.
1. På bladet **data Lake Storage gen1 åtkomst** väljer du **Skapa nytt**.
1. Välj **tjänstens huvud namn**och följ sedan anvisningarna för att skapa ett huvud namn för tjänsten.
1. Hämta certifikatet om du bestämmer dig för att använda det igen i framtiden. Att ladda ned certifikatet är användbart om du vill använda samma huvud namn för tjänsten när du skapar ytterligare HDInsight-kluster.

    ![Lägg till tjänstens huvud namn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Lägg till tjänstens huvud namn i HDInsight-kluster")

1. Välj **åtkomst** för att konfigurera åtkomst till mappen.  Se [Konfigurera fil behörigheter](#configure-file-permissions).

Så här använder du ett befintligt huvud namn för tjänsten från Azure Portal:

1. Välj **data Lake Store åtkomst**.
1. På bladet **data Lake Storage gen1 åtkomst** väljer du **Använd befintlig**.
1. Välj **tjänstens huvud namn**och välj sedan ett huvud namn för tjänsten.
1. Ladda upp certifikatet (. pfx-fil) som är associerat med ditt valda huvud namn för tjänsten och ange sedan lösen ordet för certifikatet.

    ![Lägg till tjänstens huvud namn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Lägg till tjänstens huvud namn i HDInsight-kluster")

1. Välj **åtkomst** för att konfigurera åtkomst till mappen.  Se [Konfigurera fil behörigheter](#configure-file-permissions).

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Konfigurera fil behörigheter

Konfigurationen skiljer sig åt beroende på om kontot används som standard lagring eller ytterligare ett lagrings konto:

* Används som standard lagring

  * behörighet på rot nivån för det Data Lake Storage Gen1 kontot
  * behörighet på rot nivån för kluster lagringen i HDInsight. Till exempel mappen __/Clusters__ som användes tidigare i självstudien.

* Använd som ytterligare lagrings utrymme

  * Behörighet för de mappar där du behöver fil åtkomst.

Tilldela behörighet på Data Lake Storage Gen1-kontots rot nivå:

1. Välj **åtkomst**på bladet **data Lake Storage gen1 åtkomst** . Bladet **Välj fil behörigheter** öppnas. Den visar en lista över alla Data Lake Storage Gen1 konton i din prenumeration.
1. Hovra (Klicka inte på) musen över namnet på Data Lake Storage Gen1 kontot så att kryss rutan syns och markera sedan kryss rutan.

    ![Lägg till tjänstens huvud namn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Lägg till tjänstens huvud namn i HDInsight-kluster")

   Som standard har __Läs__-, __Skriv__-och __körnings__ behörighet marker ATS.

1. Klicka på **Välj** längst ned på sidan.
1. Välj **Kör** för att tilldela behörighet.
1. Välj **Klar**.

Så här tilldelar du behörighet på HDInsight-klustrets rot nivå:

1. Välj **åtkomst**på bladet **data Lake Storage gen1 åtkomst** . Bladet **Välj fil behörigheter** öppnas. Den visar en lista över alla Data Lake Storage Gen1 konton i din prenumeration.
1. Från bladet **Välj fil behörigheter** väljer du det data Lake Storage gen1 konto namnet för att visa dess innehåll.
1. Välj HDInsight-klustrets lagrings rot genom att markera kryss rutan till vänster i mappen. Enligt skärm bilden tidigare är kluster lagrings roten __/Clusters__ -mapp som du angav när du valde data Lake Storage gen1 som standard lagring.
1. Ange behörigheter för mappen.  Som standard har Läs-, skriv-och körnings behörighet marker ATS.
1. Klicka på **Välj** längst ned på sidan.
1. Välj **Kör**.
1. Välj **Klar**.

Om du använder Data Lake Storage Gen1 som ytterligare lagrings utrymme måste du tilldela behörighet för de mappar som du vill komma åt från HDInsight-klustret. I skärm bilden nedan ger du till exempel endast åtkomst till mappen **mynewfolder** i ett data Lake Storage gen1-konto.

![Tilldela tjänstens huvud namn behörigheter till HDInsight-klustret](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Tilldela tjänstens huvud namn behörigheter till HDInsight-klustret")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Verifiera kluster konfigurationen

När kluster installationen är klar, går du till kluster bladet och kontrollerar resultatet genom att göra något av eller båda följande steg:

* För att kontrol lera att den associerade lagringen för klustret är det Data Lake Storage Gen1 konto som du har angett väljer du **lagrings konton** i den vänstra rutan.

    ![Lägg till tjänstens huvud namn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Lägg till tjänstens huvud namn i HDInsight-kluster")

* Kontrol lera att tjänstens huvud namn är korrekt associerat med HDInsight-klustret genom att välja **data Lake Storage gen1 åtkomst** i det vänstra fönstret.

    ![Lägg till tjänstens huvud namn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Lägg till tjänstens huvud namn i HDInsight-kluster")

## <a name="examples"></a>Exempel

När du har konfigurerat klustret med Data Lake Storage Gen1 som lagrings plats, se följande exempel på hur du använder HDInsight-kluster för att analysera data som lagras i Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Köra en Hive-fråga mot data i ett Data Lake Storage Gen1 konto (som primär lagring)

Om du vill köra en Hive-fråga använder du gränssnittet Hive views i Ambari-portalen. Instruktioner för hur du använder Ambari Hive-vyer finns i [använda Hive-vyn med Hadoop i HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

När du arbetar med data i ett Data Lake Storage Gen1 konto finns det några strängar att ändra.

Om du exempelvis använder det kluster som du skapade med Data Lake Storage Gen1 som primär lagring, är sökvägen till data: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/File*. En Hive-fråga för att skapa en tabell från exempel data som lagras i Data Lake Storage Gen1 kontot ser ut som följande uttryck:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Förklaringar

* `adl://hdiadlsg1storage.azuredatalakestore.net/`är roten för det Data Lake Storage Gen1 kontot.
* `/clusters/myhdiadlcluster`är roten för de kluster data som du angav när du skapade klustret.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`är platsen för den exempel fil som du använde i frågan.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Köra en Hive-fråga mot data i ett Data Lake Storage Gen1 konto (som ytterligare lagrings utrymme)

Om klustret som du skapade använder Blob Storage som standard lagring, finns inte exempel data i det Data Lake Storage Gen1 konto som används som ytterligare lagrings utrymme. I sådana fall måste du först överföra data från Blob Storage till Data Lake Storage Gen1 kontot och sedan köra frågorna som visas i föregående exempel.

Information om hur du kopierar data från Blob Storage till ett Data Lake Storage Gen1-konto finns i följande artiklar:

* [Använd Distcp för att kopiera data mellan Azure Storage blobbar och Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Använd AdlCopy för att kopiera data från Azure Storage blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Använda Data Lake Storage Gen1 med ett Spark-kluster

Du kan använda ett Spark-kluster för att köra Spark-jobb på data som lagras i ett Data Lake Storage Gen1 konto. Mer information finns i [använda HDInsight Spark-kluster för att analysera data i data Lake Storage gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Använda Data Lake Storage Gen1 i en Storm-topologi

Du kan använda Data Lake Storage Gen1 kontot för att skriva data från en Storm-topologi. Instruktioner för hur du uppnår det här scenariot finns i [använda Azure Data Lake Storage gen1 med Apache Storm med HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Se även

* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
