---
title: "Använda Azure portal för att skapa Azure HDInsight-kluster med Data Lake Store | Microsoft Docs"
description: "Använda Azure portal för att skapa och använda HDInsight-kluster med Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/14/2017
ms.author: nitinme
ms.openlocfilehash: 88fd608a02364000699224f0f2df5b4df8a29e11
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Skapa HDInsight-kluster med Data Lake Store med hjälp av Azure portal
> [!div class="op_single_selector"]
> * [Använda Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använd PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använd PowerShell (för ytterligare lagringsutrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använd Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure-portalen för att skapa ett HDInsight-kluster med ett Azure Data Lake Store-konto som standardlagring eller en extra lagringsutrymme. Även om ytterligare lagringsutrymme är valfri för ett HDInsight-kluster, rekommenderas att lagra företagsdata i ytterligare storage-konton.

## <a name="prerequisites"></a>Krav
Se till att du har uppfyllt följande krav innan du börjar den här kursen:

* **En Azure-prenumeration**. Gå till [hämta kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Store-konto**. Följ instruktionerna från [Kom igång med Azure Data Lake Store med hjälp av Azure portal](data-lake-store-get-started-portal.md). Du måste också skapa en rotmapp på kontot.  I den här självstudiekursen en rotmapp kallas __/kluster__ används.
* **Ett Azure Active Directory-tjänstens huvudnamn**. Den här självstudiekursen innehåller instruktioner om hur du skapar en tjänst UPN i Azure Active Directory (AD Azure). Du måste dock vara en Azure AD-administratör om du vill skapa ett huvudnamn för tjänsten. Om du är administratör kan du gå vidare med självstudiekursen hoppa över det här kravet.

    >[!NOTE]
    >Du kan skapa en tjänst huvudnamn endast om du är administratör för Azure AD. Azure AD-administratör skapa en tjänst huvudnamn innan du kan skapa ett HDInsight-kluster med Data Lake Store. Dessutom tjänstens huvudnamn måste skapas med ett certifikat, enligt beskrivningen i [skapa ett huvudnamn för tjänsten med certifikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Skapa ett HDInsight-kluster

I det här avsnittet skapar du ett HDInsight-kluster med Data Lake Store-konton som standard eller ytterligare lagringsutrymme. Den här artikeln fokuserar endast del av Data Lake Store-konton.  Allmän information om skapandet och procedurer finns [skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Skapa ett kluster med Data Lake Store som standardlagring

**Skapa ett HDInsight-kluster med ett Data Lake Store som standardkontot för lagring**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Följ [Skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) allmän information om hur du skapar HDInsight-kluster.
3. På den **lagring** bladet under **primära lagringstyp**väljer **Datasjölager**, och ange följande information:

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

    - **Välj Data Lake Store-konto**: Välj ett befintligt Data Lake Store-konto. Ett befintligt Data Lake Store-konto krävs.  Se [krav](#prereuisites).
    - **Rotsökvägen**: Ange en sökväg där klusterspecifika filerna ska lagras. På skärmbilden, är det __/kluster-myhdiadlcluster__, där den __/kluster__ mappen måste finnas och skapar portalen *myhdicluster* mapp.  Den *myhdicluster* är klusternamnet.
    - **Data Lake Store-åtkomst**: Konfigurera åtkomst mellan Data Lake Store-konto och HDInsight-kluster. Instruktioner finns i [konfigurera Data Lake Store-åtkomst](#configure-data-lake-store-access).
    - **Ytterligare lagringskonton**: Lägg till Azure Storage-konton som ytterligare lagringsutrymme konton för klustret. Om du vill lägga till ytterligare görs Data Lake lagrar genom att ge behörigheterna som klustret på data i flera datasjölagerkonton när du konfigurerar ett Data Lake Store-konto som den primära lagringstypen. Läs mer i [Konfigurera åtkomst till Data Lake Store](#configure-data-lake-store-access).

4. På den **Data Lake Store-åtkomst**, klickar du på **Välj**, och sedan fortsätta med skapa ett kluster, enligt beskrivningen i [skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Skapa ett kluster med Data Lake Store som ytterligare lagringsutrymme

Följande instruktioner skapar ett HDInsight-kluster med ett Azure Storage-konto som standardlagring och ett Data Lake Store-konto som ett ytterligare lagringsutrymme.
**Skapa ett HDInsight-kluster med ett Data Lake Store som standardkontot för lagring**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Följ [Skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) allmän information om hur du skapar HDInsight-kluster.
3. På den **lagring** bladet under **primära lagringstyp**väljer **Azure Storage**, och ange följande information:

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

    - **Urvalsmetod**: Använd ett av följande alternativ:

        * Om du vill ange ett lagringskonto som är en del av din Azure-prenumeration, Välj **Mina prenumerationer**, och välj sedan lagringskontot.
        * Om du vill ange ett lagringskonto som ligger utanför din Azure-prenumeration, Välj **åtkomstnyckeln**, och sedan ange information för det externa lagringskontot.

    - **Standardbehållaren**: Använd antingen standardvärdet eller ange ett eget namn.

    - Ytterligare lagringskonton: lägga till fler Azure Storage-konton som det extra lagringsutrymmet.
    - Data Lake Store-åtkomst: Konfigurera åtkomst mellan Data Lake Store-konto och HDInsight-kluster. Instruktioner finns i [konfigurera Data Lake Store-åtkomst](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Konfigurera Data Lake Store-åtkomst 

I det här avsnittet konfigurerar du Data Lake Store-åtkomst från HDInsight-kluster med en Azure Active Directory-tjänstens huvudnamn. 

### <a name="specify-a-service-principal"></a>Ange ett huvudnamn för tjänsten

Från Azure-portalen kan du använda ett befintligt huvudnamn för tjänsten eller skapa en ny.

**Skapa ett huvudnamn för tjänsten från Azure-portalen**

1. Klicka på **Data Lake Store-åtkomst** från Store-bladet.
2. På den **Data Lake Store-åtkomst** bladet, klickar du på **Skapa nytt**.
3. Klicka på **tjänstens huvudnamn**, och följ sedan instruktionerna för att skapa ett huvudnamn för tjänsten.
4. Hämta certifikatet om du vill använda den igen. Hämta certifikatet är användbart om du vill använda samma tjänstens huvudnamn när du skapar ytterligare HDInsight-kluster.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

4. Klicka på **åtkomst** att konfigurera mappåtkomst.  Se [konfigurera filbehörigheter](#configure-file-permissions).


**Att använda ett befintligt huvudnamn för tjänsten från Azure-portalen**

1. Klicka på **Data Lake Store-åtkomst**.
1. På den **Data Lake Store-åtkomst** bladet, klickar du på **använda befintliga**.
2. Klicka på **tjänstens huvudnamn**, och välj sedan ett huvudnamn för tjänsten. 
3. Ladda upp certifikatet (.pfx-fil) som är kopplad till ditt valda tjänstens huvudnamn och ange lösenord för certifikatet.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

4. Klicka på **åtkomst** att konfigurera mappåtkomst.  Se [konfigurera filbehörigheter](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Konfigurera behörigheter för filer

Den konfigurerar varierar beroende på om kontot används som standardlagring eller ett ytterligare storage-konto:

- Används som standardlagring

    - behörigheten på rotnivå på Data Lake Store-konto
    - behörighet på rotnivå lagring för HDInsight-kluster. Till exempel den __/kluster__ mappen som används för tidigare i kursen.
- Använda som ett ytterligare lagringsutrymme

    - Behörighet till mapparna där du behöver filåtkomst.

**Att tilldela behörighet på rotnivå för Data Lake Store-konto**

1. På den **Data Lake Store-åtkomst** bladet, klickar du på **åtkomst**. Den **Välj filbehörigheter** blad öppnas. Den visar alla Data Lake Store-konton i din prenumeration.
2. Hovra (inte på) muspekaren över namnet på Data Lake Store-konto så att den här kryssrutan visas, välj sedan kryssrutan.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

  Som standard __läsa__, __skriva__, AND __EXECUTE__ är alla markerade.

3. Klicka på **Välj** längst ned på sidan.
4. Klicka på **kör** att tilldela behörighet.
5. Klicka på **Klar**.

**Att tilldela behörighet på rotnivå för HDInsight-kluster**

1. På den **Data Lake Store-åtkomst** bladet, klickar du på **åtkomst**. Den **Välj filbehörigheter** blad öppnas. Den visar alla Data Lake Store-konton i din prenumeration.
1. Från den **Välj filbehörigheter** bladet, klickar du på Data Lake Store-namnet för att visa dess innehåll.
2. Välj lagringsroten för HDInsight-kluster genom att markera kryssrutan till vänster i mappen. Enligt skärmbilden tidigare lagringsroten klustret är __/kluster__ mappen som du angav när du väljer Data Lake Store som standardlagring.
3. Ange behörigheter för mappen.  Som standard, läsa, skriva och köra är alla markerade.
4. Klicka på **Välj** längst ned på sidan.
5. Klicka på **Run** (Kör).
6. Klicka på **Klar**.

Om du använder Data Lake Store som ytterligare lagringsutrymme måste du tilldela behörigheten endast för de mappar som du vill komma åt från HDInsight-klustret. Till exempel i skärmbilden nedan du ge åtkomst endast **hdiaddonstorage** mapp i ett Data Lake Store-konto.

![Tilldela service principal behörigheter till HDInsight-klustret](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "tilldela service principal behörigheter till HDInsight-kluster")


## <a name="verify-cluster-set-up"></a>Kontrollera konfigurera klustret

När klustret installationen är klar, i bladet klustret kontrollera resultaten genom att göra något eller båda av följande steg:

* Kontrollera att den associerade lagringen för klustret är Data Lake Store-konto som du angav, klicka på **lagringskonton** i den vänstra rutan.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")

* Kontrollera att tjänstens huvudnamn är korrekt associerad med HDInsight-klustret, klicka på **Data Lake Store-åtkomst** i den vänstra rutan.

    ![Lägg till tjänstens huvudnamn till HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Lägg till tjänstens huvudnamn till HDInsight-kluster")


## <a name="examples"></a>Exempel

När du har skapat klustret med Data Lake Store som din lagring finns i följande exempel på hur du använder HDInsight-kluster för att analysera data som lagras i Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Köra en Hive-fråga mot data i ett Data Lake Store (som primär lagring)

Använda Hive vyer gränssnittet i Ambari-portal för att köra en Hive-fråga. Instruktioner om hur du använder Ambari Hive-vyer finns [använda Hive-vy med Hadoop i HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

När du arbetar med data i ett Data Lake Store finns några strängar att ändra.

Om du använder, till exempel det kluster som du skapade med Data Lake Store som primär lagring sökvägen till data är: *adl: / / < data_lake_store_account_name > /azuredatalakestore.net/path/to/file*. En Hive-fråga för att skapa en tabell från exempeldata som lagras i Data Lake Store-konto ser ut som följande sats:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Beskrivningar:
* `adl://hdiadlstorage.azuredatalakestore.net/`är roten till Data Lake Store-konto.
* `/clusters/myhdiadlcluster`är roten till kluster-data som du angav när du skapar klustret.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`är platsen för exempelfilen som du använde i frågan.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Köra en Hive-fråga mot data i ett Data Lake Store (som ytterligare lagringsutrymme)

Om klustret som du skapade använder Blob storage som standardlagring, ingår inte exempeldata i Azure Data Lake Store-konto som används som ytterligare lagringsutrymme. I så fall först överföra data från Blob storage till Data Lake Store och sedan köra frågor som visas i föregående exempel.

Information om hur du kopierar data från Blob storage till ett Data Lake Store finns i följande artiklar:

* [Använd Distcp för att kopiera data mellan Azure Storage-blobbar och Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Använd AdlCopy för att kopiera data från Azure Storage BLOB till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Använd Data Lake Store med ett Spark-kluster
Du kan använda ett Spark-kluster för att köra Spark på data som lagras i ett Data Lake Store. Mer information finns i [använda HDInsight Spark-klustret för att analysera data i Data Lake Store](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Använd Data Lake Store i en Storm-topologi
Du kan använda Data Lake Store för att skriva data från en Storm-topologi. Instruktioner om hur du utför det här scenariot finns [Använd Azure Data Lake Store med Apache Storm med HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Se även
* [PowerShell: Skapa ett HDInsight-kluster om du vill använda Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
