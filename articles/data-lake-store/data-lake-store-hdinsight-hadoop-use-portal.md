---
title: Använda Azure portal för att skapa Azure HDInsight-kluster med Azure Data Lake Storage Gen1 | Microsoft Docs
description: Använda Azure portal för att skapa och använda HDInsight-kluster med Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 25e4c308b55b66038be6825a239f185e5e813af5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085770"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 med hjälp av Azure portal
> [!div class="op_single_selector"]
> * [Använda Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använda PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använda PowerShell (för ytterligare lagring)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använd Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure-portalen för att skapa ett HDInsight-kluster med ett konto för Azure Data Lake Storage Gen1 som standardlagring eller ett ytterligare lagringsutrymme. Även om ytterligare lagringsutrymme är valfritt för ett HDInsight-kluster, rekommenderar vi att du lagrar dina affärsdata i ytterligare storage-konton.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här självstudien måste du se till att du har uppfyllt följande krav:

* **En Azure-prenumeration**. Gå till [skaffa Azure kostnadsfria utvärderingsversionen](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto för Data Lake Storage Gen1**. Följ instruktionerna från [Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure portal](data-lake-store-get-started-portal.md). Du måste också skapa en rotmapp för kontot.  I den här självstudien en rotmapp kallas __/kluster__ används.
* **Ett Azure Active Directory-tjänstobjekt**. Den här självstudien innehåller instruktioner om hur du kan skapa ett tjänstobjekt i Azure Active Directory (AD Azure). Om du vill skapa ett huvudnamn för tjänsten, måste du vara en Azure AD-administratör. Om du är administratör kan du hoppa över den nödvändiga komponenten och fortsätter med självstudien.

    >[!NOTE]
    >Du kan skapa en tjänst huvudnamn endast om du är en Azure AD-administratör. Azure AD-administratören måste skapa ett tjänstens huvudnamn av innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Dessutom tjänstens huvudnamn måste skapas med ett certifikat, enligt beskrivningen i [skapa tjänstens huvudnamn med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Skapa ett HDInsight-kluster

I det här avsnittet skapar du ett HDInsight-kluster med Data Lake Storage Gen1 konton som standard eller det extra lagringsutrymmet. Den här artikeln fokuserar endast del av Konfigurera Gen1 för Data Lake Storage-konton.  Allmän kluster skapas information och procedurer finns i [skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Skapa ett kluster med Data Lake Storage Gen1 som standardlagring

**Att skapa ett HDInsight-kluster med ett Data Lake Storage Gen1-konto som standardkonto för lagring**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Följ [Skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) allmän information om hur du skapar HDInsight-kluster.
3. På den **Storage** bladet under **primär lagringstyp**väljer **Azure Data Lake Storage Gen1**, och ange sedan följande information:

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

    - **Välj Data Lake Store-konto**: Välj ett befintligt Data Lake Storage Gen1-konto. Ett befintligt Data Lake Storage Gen1-konto krävs.  Se [Förutsättningar](#prerequisites).
    - **Rotsökvägen**: Ange en sökväg där klusterspecifika filer ska lagras. På skärmbilden är den är __/kluster/myhdiadlcluster/__, där den __/kluster__ mappen måste finnas och portalen skapar *myhdicluster* mapp.  Den *myhdicluster* är klustrets namn.
    - **Data Lake Store-åtkomst**: Konfigurera åtkomst mellan Data Lake Storage Gen1-kontot och HDInsight-kluster. Instruktioner finns i Konfigurera Data Lake Storage Gen1 åtkomst.
    - **Ytterligare lagringskonton**: Lägg till Azure storage-konton som ytterligare lagringskonton för klustret. Om du vill lägga till ytterligare Data Lake Storage Gen1 konton görs genom att ge behörigheterna som klustret på data i flera Data Lake Storage Gen1 konton när du konfigurerar ett Data Lake Storage Gen1-konto som primär lagring. Se Konfigurera Data Lake Storage Gen1 åtkomst.

4. På den **Data Lake Store-åtkomst**, klickar du på **Välj**, och sedan fortsätta med Skapa kluster, enligt beskrivningen i [skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Skapa ett kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme

Följande anvisningar kan du skapa ett HDInsight-kluster med ett Azure storage-konto som standardlagring och ett Data Lake Storage Gen1-konto som ett ytterligare lagringsutrymme.

**Att skapa ett HDInsight-kluster med ett Data Lake Storage Gen1-konto som ett ytterligare lagringskonto**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Följ [Skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) allmän information om hur du skapar HDInsight-kluster.
3. På den **Storage** bladet under **primär lagringstyp**väljer **Azure Storage**, och ange sedan följande information:

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

    - **Urvalsmetod**: Använd någon av följande alternativ:

        * Om du vill ange ett lagringskonto som är en del av din Azure-prenumeration, Välj **Mina prenumerationer**, och välj sedan lagringskontot.
        * Om du vill ange ett lagringskonto som ligger utanför din Azure-prenumeration, Välj **åtkomstnyckeln**, och sedan ange information för det externa lagringskontot.

    - **Standardbehållaren**: Använd antingen standardvärdet eller ange egna namn.

    - Ytterligare lagringskonton: lägga till fler Azure storage-konton som ytterligare lagringsutrymme.
    - Data Lake Store-åtkomst: Konfigurera åtkomst mellan Data Lake Storage Gen1-kontot och HDInsight-kluster. Instruktioner finns i [konfigurerar Data Lake Storage Gen1 åtkomst](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurera åtkomst till Data Lake Storage Gen1 

I det här avsnittet konfigurerar du Data Lake Storage Gen1 åtkomst från HDInsight-kluster med ett Azure Active Directory-tjänstobjekt. 

### <a name="specify-a-service-principal"></a>Ange ett huvudnamn för tjänsten

Från Azure-portalen kan du använda ett befintligt huvudnamn för tjänsten eller skapa en ny.

**Skapa ett huvudnamn för tjänsten från Azure portal**

1. Klicka på **Data Lake Store-åtkomst** från bladet Storage.
2. På den **åtkomst till Data Lake Storage Gen1** bladet klickar du på **Skapa ny**.
3. Klicka på **tjänstens huvudnamn**, och följ sedan anvisningarna för att skapa ett huvudnamn för tjänsten.
4. Hämta certifikatet om du vill använda den igen. Nedladdningen av certifikatet är användbart om du vill använda samma tjänstens huvudnamn när du skapar ytterligare HDInsight-kluster.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

4. Klicka på **åtkomst** att konfigurera mappåtkomst.  Se [konfigurera filbehörigheter](#configure-file-permissions).


**Att använda ett befintligt huvudnamn för tjänsten från Azure portal**

1. Klicka på **Data Lake Store-åtkomst**.
1. På den **åtkomst till Data Lake Storage Gen1** bladet klickar du på **Använd befintlig**.
2. Klicka på **tjänstens huvudnamn**, och välj sedan ett huvudnamn för tjänsten. 
3. Ladda upp certifikatet (.pfx-fil) som är associerat med din valda tjänstens huvudnamn och ange sedan lösenordet för certifikatet.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

4. Klicka på **åtkomst** att konfigurera mappåtkomst.  Se [konfigurera filbehörigheter](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Konfigurera filbehörigheter

Den konfigurerar varierar beroende på om konton som används som standardlagring eller ett annat lagringskonto:

- Används som standardlagring

    - behörighet på rotnivå för Data Lake Storage Gen1-konto
    - -behörighet på rotnivå lagringsutrymme för HDInsight-kluster. Till exempel den __/kluster__ mappen använde tidigare i självstudien.
- Använda som ett ytterligare lagringsutrymme

    - Behörighet när de mappar där du behöver filåtkomst.

**Att tilldela behörighet på rotnivå Gen1 för Data Lake Storage-konto**

1. På den **åtkomst till Data Lake Storage Gen1** bladet klickar du på **åtkomst**. Den **Välj filbehörigheter** bladet öppnas. Den visar alla Data Lake Storage Gen1 konton i din prenumeration.
2. Hovra (Klicka inte) musen över namnet på Data Lake Storage Gen1 kontot så att kryssrutan visas, välj sedan kryssrutan.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

   Som standard __läsa__, __skriva__, AND __kör__ är alla markerade.

3. Klicka på **Välj** längst ned på sidan.
4. Klicka på **kör** att tilldela behörighet.
5. Klicka på **Klar**.

**Att tilldela behörighet på rotnivå för HDInsight-kluster**

1. På den **åtkomst till Data Lake Storage Gen1** bladet klickar du på **åtkomst**. Den **Välj filbehörigheter** bladet öppnas. Den visar alla Data Lake Storage Gen1 konton i din prenumeration.
1. Från den **Välj filbehörigheter** bladet klickar du på namnet på Data Lake Storage Gen1 att visa dess innehåll.
2. Välj lagringsroten för HDInsight-kluster genom att markera kryssrutan till vänster i mappen. Enligt skärmbilden tidigare lagringsroten klustret är __/kluster__ mappen som du angav när du väljer Data Lake Storage Gen1 som standardlagringsutrymme.
3. Ange behörigheter för mappen.  Som standard, läsa, skriva och köra är alla markerade.
4. Klicka på **Välj** längst ned på sidan.
5. Klicka på **Run** (Kör).
6. Klicka på **Klar**.

Om du använder Data Lake Storage Gen1 som ytterligare lagringsutrymme måste du tilldela behörigheten endast för de mappar som du vill komma åt från HDInsight-kluster. Till exempel i skärmbilden nedan du ge åtkomst endast till den **mynewfolder** mapp i ett Data Lake Storage Gen1-konto.

![Tilldela behörigheter för tjänstens huvudnamn i HDInsight-klustret](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "tilldela behörigheter för tjänstens huvudnamn till HDInsight-kluster")


## <a name="verify-cluster-set-up"></a>Verifiera kluster konfigurera

När klustret installationen är klar, kontrollerar du dina resultat genom att göra ett eller båda av följande steg på bladet för klustret:

* Kontrollera att den associerade lagringen för klustret är Gen1 för Data Lake Storage-konto som du angav, klicka på **lagringskonton** i den vänstra rutan.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

* Kontrollera att tjänstens huvudnamn är korrekt associerad med HDInsight-kluster, klicka på **åtkomst till Data Lake Storage Gen1** i den vänstra rutan.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")


## <a name="examples"></a>Exempel

När du har skapat klustret med Data Lake Storage Gen1 som din lagring, referera till de här exemplen på hur du använder HDInsight-kluster för att analysera data som lagras i Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Köra en Hive-fråga mot data i ett Data Lake Storage Gen1-konto (som primär lagring)

Använda gränssnittet för Hive-vyer i Ambari-portalen för att köra en Hive-fråga. Anvisningar om hur du använder Ambari Hive-vyer finns i [använda vyn Hive med Hadoop i HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

När du arbetar med data i ett Data Lake Storage Gen1-konto, finns det några strängar som ska ändras.

Om du använder, till exempel det kluster som du skapade med Data Lake Storage Gen1 som primär lagring är sökvägen till data: *adl: / / < data_lake_storage_gen1_account_name > /azuredatalakestore.net/path/to/file*. En Hive-fråga för att skapa en tabell från exempeldata som lagras i kontot Data Lake Storage Gen1 ser ut som följande uttryck:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Beskrivningar:
* `adl://hdiadlsg1storage.azuredatalakestore.net/` är roten för Data Lake Storage Gen1-kontot.
* `/clusters/myhdiadlcluster` är roten av klusterdata som du angav när du skapade klustret.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` är platsen för exempelfil som du använde i frågan.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Köra en Hive-fråga mot data i ett Data Lake Storage Gen1-konto (som ytterligare lagringsutrymme)

Om det kluster som du skapade använder Blob storage som standardlagring, finns exempeldata inte i Data Lake Storage Gen1-konto som ska användas som ytterligare lagringsutrymme. I detta fall är först överföra data från Blob storage till kontot Data Lake Storage Gen1 och sedan köra frågor som visas i föregående exempel.

Information om hur du kopierar data från Blob storage till ett Data Lake Storage Gen1-konto finns i följande artiklar:

* [Använd Distcp för att kopiera data mellan Azure Storage-blobbar och Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Använd AdlCopy för att kopiera data från Azure Storage-blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Använd Data Lake Storage Gen1 med ett Spark-kluster
Du kan använda ett Spark-kluster för att köra Spark-jobb på data som lagras i ett Data Lake Storage Gen1-konto. Mer information finns i [använda HDInsight Spark-kluster för att analysera data i Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Använda Data Lake Storage Gen1 i en Storm-topologi
Du kan använda Data Lake Storage Gen1-konto för att skriva data från en Storm-topologi. Anvisningar för hur du utför det här scenariot finns i [Använd Azure Data Lake Storage Gen1 med Apache Storm med HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Se också
* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Skapa ett HDInsight-kluster om du vill använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
