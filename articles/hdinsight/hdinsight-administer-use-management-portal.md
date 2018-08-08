---
title: Hantera Windows-baserade Hadoop-kluster i HDInsight med Azure portal
description: Lär dig hur du administrerar HDInsight Service. Skapa ett HDInsight-kluster, öppna den interaktiva JavaScript-konsolen och öppna kommandokonsolen Hadoop.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: a4cbc0c14d0f6c505b391becf33c56dd95bfc251
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592228"
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Hantera Windows-baserade Hadoop-kluster i HDInsight med hjälp av Azure-portalen

Med hjälp av den [Azure-portalen][azure-portal], du kan skapa Windows-baserade Hadoop-kluster i Azure HDInsight, ändra lösenord för Hadoop-användare och aktivera Remote Desktop Protocol (RDP) så att du kan använda kommandot Hadoop -konsolen på klustret.

Informationen i den här artikeln gäller endast för Windows-baserad HDInsight-kluster. Information om hur du hanterar Linux-baserade kluster finns i [hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure Storage-konto** – ett HDInsight-klustret använder en Azure Blob storage-behållare som standardfilsystemet. Mer information om hur Azure Blob storage ger en sömlös upplevelse med HDInsight-kluster finns i [använda Azure Blob Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md). Mer information om hur du skapar ett Azure Storage-konto finns i [hur du skapar ett Lagringskonto](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Öppna portalen
1. Logga in på [ https://portal.azure.com ](https://portal.azure.com).
2. När du öppnar portalen, kan du:

   * Klicka på **skapa en resurs** från den vänstra menyn för att skapa ett nytt kluster:

       ![knapp för ny HDInsight-kluster](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Klicka på **HDInsight-kluster** menyn till vänster.

       ![Azure HDInsight-kluster portalknappen](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Om **HDInsight** inte visas i den vänstra menyn, klickar du på **Bläddra**.

     ![Azure portal kluster bläddringsknappen](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Skapa kluster
Skapa-instruktioner med hjälp av portalen finns i [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight fungerar med en bred Hadoop-komponenter. Lista över de komponenter som har verifierats och som stöds finns i [vilken version av Hadoop är i Azure HDInsight](hdinsight-component-versioning.md). Du kan anpassa HDInsight med hjälp av något av följande alternativ:

* Använd skriptåtgärder för att köra anpassade skript som kan anpassa ett kluster för att ändra klusterkonfiguration eller installera anpassade komponenter, till exempel Giraph och Solr. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster.md).
* Använd anpassning Klusterparametrar i HDInsight .NET SDK eller Azure PowerShell när klustret skapas. De här konfigurationsändringarna bevaras sedan igenom livslängden för klustret och påverkas inte av klustret noden avbildningen som Azure-plattformen utför med jämna mellanrum för underhåll. Läs mer om hur du använder anpassning Klusterparametrar [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).
* Vissa interna Java-komponenter som Mahout och kaskad, kan köras på klustret som JAR-filerna. De här JAR-filerna kan distribueras till Azure Blob storage och skickas till HDInsight-kluster via Hadoop-jobb skickas mekanismer. Mer information finns i [skicka Hadoop-jobb programmässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Om du har problem när du distribuerar JAR-filerna till HDInsight-kluster eller anropa JAR-filerna på HDInsight-kluster, kontakta [Microsoft Support](https://azure.microsoft.com/support/options/).
  >
  > Sammanhängande stöds inte av HDInsight och är inte berättigade till Microsoft Support. För listor över stödda komponenter som finns i [vad är nytt i klusterversionerna från HDInsight](hdinsight-component-versioning.md).
  >
  >

Installationen av anpassad programvara i klustret med hjälp av anslutning till fjärrskrivbord stöds inte. Du bör inte lagra filer på enheter för huvudnoden, eftersom de kommer att gå förlorade om du behöver återskapa klustren. Vi rekommenderar att du lagrar filer på Azure Blob storage. BLOB storage är permanent.

## <a name="list-and-show-clusters"></a>Lista och visa kluster
1. Logga in på [ https://portal.azure.com ](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** menyn till vänster.
3. Klicka på klusternamnet. Om listan över klustret är lång kan använda du filter överst på sidan.
4. Dubbelklicka på ett kluster från listan om du vill visa information.

    **Menyn och essentials**:

    ![Azure portal HDInsight-kluster essentials](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Om du vill anpassa menyn, högerklickar du någonstans på menyn och klicka sedan på **anpassa**.
   * **Inställningar för** och **alla inställningar**: Visar den **inställningar** bladet för kluster, vilket ger dig tillgång till mer information om konfigurationen för klustret.
   * **Instrumentpanelen**, **Klusterinstrumentpanel** och **URL**: det här är alla sätt att komma åt instrumentpanelen för klustret, vilket är Ambari Web för Linux-baserade kluster.
   * **Secure Shell**: Visar instruktioner för att ansluta till klustret med Secure Shell (SSH)-anslutning.
   * **Skala kluster**: gör att du kan ändra antalet arbetsnoder för det här klustret.
   * **Ta bort**: tar bort klustret.
   * **Snabbstart**: Visar information som hjälper dig att komma igång med HDInsight.
   * **Användare**: gör att du kan ange behörigheter för *portal management* för detta kluster för andra användare på din Azure-prenumeration.

     > [!IMPORTANT]
     > Detta *endast* påverkar åtkomst och behörighet till det här klustret i Azure-portalen och har ingen effekt på vem som kan ansluta till eller skicka jobb till HDInsight-klustret.
     >
     >
   * **Taggar**: taggar kan du ange nyckel/värde-par för att definiera en anpassad taxonomi för cloud services. Du kan till exempel skapa en nyckel med namnet **projekt**, och sedan använda gemensamt värde för alla tjänster som är associerade med ett specifikt projekt.
   * **Ambari-vyer**: länkar till Ambari Web.

     > [!IMPORTANT]
     > För att hantera de tjänster som tillhandahålls av HDInsight-klustret, måste du använda Ambari Web eller Ambari REST API. Läs mer om hur du använder Ambari [hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Användning**:

     ![Azure portal HDInsight-kluster-användning](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Klicka på **inställningar**.

    ![Azure portal HDInsight-kluster-användning](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Egenskaper för**: Visa egenskaper för klustret.
   * **Kluster AAD-identitet**:
   * **Azure Storage-nycklar**: Visa standardkontot för lagring och dess nyckel. Lagringskontot är konfiguration när klustret skapas.
   * **Kluster-inloggningen**: ändra kluster HTTP-användarnamn och lösenord.
   * **Externa Metastores**: Visa metastores för Hive och Oozie. Metastores kan bara konfigureras när klustret skapas.
   * **Skala kluster**: öka och minska antalet arbetarnoder i klustret.
   * **Fjärrskrivbord**: aktivera och inaktivera åtkomst för fjärrskrivbord (RDP) och konfigurera RDP-användarnamnet.  RDP-användarnamnet måste skilja sig från användarnamnet HTTP.
   * **Partnern för posten**:

     > [!NOTE]
     > Det här är en allmän lista över tillgängliga inställningar. inte alla ska vara tillgänglig för alla typer av klustret.
     >
     >
6. Klicka på **egenskaper**:

    Egenskapsavsnittet visas följande:

   * **Hostname**: klustrets namn.
   * **Klustrets URL**.
   * **Status för**: inkludera avbröts, godkända ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, drift, kör, fel, tar bort, tas bort, nådde sin tidsgräns, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Region**: Azure-plats. En lista över platser som stöds Azure finns i den **Region** nedrullningsbara listrutan på [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data som har skapats**.
   * **Operativsystemet**: antingen **Windows** eller **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Version**. Se [HDInsight-versioner](hdinsight-component-versioning.md)
   * **Prenumeration**: prenumerationsnamn.
   * **Prenumerations-ID**.
   * **Primära datakälla**. Azure Blob storage-konto som används som standard Hadoop-filsystem.
   * **Arbetsnoderna prisnivån**.
   * **Huvudnod prisnivån**.

## <a name="delete-clusters"></a>Ta bort kluster
Ta bort ett kluster kommer inte att ta bort standardkontot för lagring eller eventuella länkade lagringskonton. Du kan återskapa klustret med hjälp av samma lagringskonton och samma metastores.

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** i den vänstra menyn klickar du på **HDInsight-kluster**, klicka på klusternamnet.
3. Klicka på **ta bort** på den översta menyn och följ sedan instruktionerna.

Se även [pausa/Stäng kluster](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Skala kluster
Klusterskalning funktionen kan du ändra antalet arbetarnoder som används av ett kluster som körs i Azure HDInsight utan att behöva återskapa klustret.

> [!NOTE]
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av ditt kluster kan kontrollera du egenskapssidan.  Se [lista och visa kluster](#list-and-show-clusters).
>
>

Effekten av att ändra antalet datanoder som för varje typ av kluster som stöds av HDInsight:

* Hadoop

    Du kan smidigt öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller pågående jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras ett smidigt sätt så att klustret finns alltid kvar i funktionsdugligt tillstånd.

    När ett Hadoop-kluster skalas genom att minska antalet datanoder några tjänster i klustret startas om. Detta gör allt körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* HBase

    Du kan smidigt lägga till eller ta bort noder till HBase-kluster medan den körs. Regionservrar balanseras automatiskt inom ett par minuter efter att du skalar igen. Du kan också manuellt balansera regionservrar genom att logga till huvudnoden i klustret och köra följande kommandon från en kommandotolk:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Mer information om hur du använder HBase-gränssnittet finns i]
* Storm

    Du kan smidigt lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Men när en installationen har slutförts åtgärdens skalning, behöver du balansera om topologin.

    Ombalansering kan utföras på två sätt:

  * Storm-Webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)

    Finns det [Apache Storm-dokumentationen](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.

    Storm-webbgränssnittet finns på HDInsight-klustret:

    ![HDInsight storm skala ombalansering](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Här är ett exempel hur du använder CLI-kommando för att balansera om Storm-topologi:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Skala kluster**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** i den vänstra menyn klickar du på **HDInsight-kluster**, klicka på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och sedan på **skala kluster**.
4. Ange **många av arbetsnoder**. Gränsen för antalet noder varierar mellan olika Azure-prenumerationer. Du kan kontakta faktureringssupporten om du vill öka gränsen.  Information om kostnaden avspeglar ändringar i antalet noder.

    ![HDInsight Hadoop HBase, Storm Spark-skala](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Pausa/Stäng kluster
De flesta Hadoop-jobb är batchjobb som bara körde ibland. Det finns stora tidsperioder som klustret inte används för bearbetning för de flesta Hadoop-kluster. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används.
Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Det finns många sätt som du kan programmera processen:

* Användaren Azure Data Factory. Se [länkad Azure HDInsight-tjänst](../data-factory/compute-linked-services.md) och [transformera och analysera med Azure Data Factory](../data-factory/transform-data.md) för HDInsight på begäran och själva definierade länkade tjänster.
* Använd Azure PowerShell.  Se [analysera flygförseningsdata](hdinsight-analyze-flight-delay-data.md).
* Använda Azure CLI. Se [hantera HDInsight-kluster med Azure CLI](hdinsight-administer-use-command-line.md).
* Använda HDInsight .NET SDK. Se [skicka Hadoop-jobb](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Prisinformationen, se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/). Om du vill ta bort ett kluster från portalen, se [ta bort kluster](#delete-clusters)

## <a name="change-cluster-username"></a>Ändra klusternamn
Ett HDInsight-kluster kan ha två användarkonton. Användarkonto för HDInsight-klustret har skapats under skapandeprocessen. Du kan också skapa ett användarkonto för RDP för åtkomst till klustret via RDP. Se [aktivera Fjärrskrivbord](#connect-to-hdinsight-clusters-by-using-rdp).

**Ändra HDInsight-kluster-användarnamn och lösenord**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** i den vänstra menyn klickar du på **HDInsight-kluster**, klicka på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och sedan på **Klusterinloggning**.
4. Om **klusterinloggning** har varit aktiverad, du måste klicka på **inaktivera**, och klicka sedan på **aktivera** innan du kan ändra det användarnamn och lösenord...
5. Ändra den **klustrets inloggningsnamn** och/eller den **inloggningslösenordet för klustret**, och klicka sedan på **spara**.

    ![HDInsight ändra kluster användarnamn lösenord http-användare](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Bevilja/återkalla åtkomst
HDInsight-kluster har följande HTTP-webbtjänster (alla dessa tjänster har RESTful-slutpunkter):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Som standard beviljas de här tjänsterna för åtkomst. Du kan återkalla/bevilja åtkomst från Azure-portalen.

> [!NOTE]
> Genom att bevilja/återkalla åtkomst, ska du återställa klustret användarnamn och lösenord.
>
>

**Att bevilja/återkalla åtkomst HTTP web services-åtkomst**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** i den vänstra menyn klickar du på **HDInsight-kluster**, klicka på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och sedan på **Klusterinloggning**.
4. Om **klusterinloggning** har varit aktiverad, du måste klicka på **inaktivera**, och klicka sedan på **aktivera** innan du kan ändra det användarnamn och lösenord...
5. För **användarnamnet för Klusterinloggning** och **inloggningslösenordet för klustret**, ange nytt användarnamn och lösenord (respektive) för klustret.
6. Klicka på **SPARA**.

    ![HDInsight grand ta bort http Webbtjänståtkomst](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Hitta standardkontot för lagring
Varje HDInsight-kluster är ett standard storage-konto. Standardkontot för lagring och dess nycklar för ett kluster visas under **inställningar**/**egenskaper**/**Azure Storage-nycklar**. Se [lista och visa kluster](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Hitta resursgruppen.
Varje HDInsight-klustret har skapats med en Azure-resursgrupp i Azure Resource Manager-läge. Azure-resursgrupp som tillhör ett kluster visas i:

* Listan över kluster har en **resursgrupp** kolumn.
* Klustret **viktiga** panelen.  

Se [lista och visa kluster](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Öppna konsolen för HDInsight-fråga
HDInsight frågekonsolen innehåller följande funktioner:

* **Hive-redigeraren**: A GUI Webbgränssnitt för att skicka Hive-jobb.  Se [kör Hive-frågor med Frågekonsolen](hadoop/apache-hadoop-use-hive-query-console.md).

    ![HDInsight portal hive-redigeraren](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Jobbhistorik**: övervaka Hadoop-jobb.  

    ![HDInsight portal jobbhistorik](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Klicka på **Frågenamnet** att visa information inklusive jobbegenskaper, **Jobbfråga**, och ** Jobbutdata. Du kan också hämta både frågan och utdata till din arbetsstation.
* **Filen webbläsare**: Bläddra standardkontot för lagring och länkade storage-konton.

    ![HDInsight portal webbläsare filsökning](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    På skärmbilden är den **<Account>** anger objektet är ett Azure storage-konto.  Klicka på namnet på kontot för att läsa filerna.
* **Hadoop-Användargränssnittet**.

    ![Portal för HDInsight Hadoop UI](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    Från **Hadoop UI*, du kan bläddra efter filer och kontrollera loggarna.
* **Yarn UI**.

    ![HDInsight portal YARN-Användargränssnittet](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Köra Hive-frågor
Om du vill köra Hive-jobb från portalen, klickar du på **Hive-redigeraren** i HDInsight frågekonsolen. Se [öppna HDInsight frågekonsol](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Övervaka jobb
För att övervaka jobb från portalen, klickar du på **jobbhistorik** i HDInsight frågekonsolen. Se [öppna HDInsight frågekonsol](#open-hdinsight-query-console).

## <a name="browse-files"></a>Bläddra efter filer
Om du vill bläddra efter filer som lagras i standardkontot för lagring och länkade storage-konton, klickar du på **filläsare** i HDInsight frågekonsolen. Se [öppna HDInsight frågekonsol](#open-hdinsight-query-console).

Du kan också använda den **Bläddra i filsystemet** utility från den **Hadoop UI** i HDInsight-konsolen.  Se [öppna HDInsight frågekonsol](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Övervaka kluster användning
Den **användning** på HDInsight-klusterbladet visar information om hur många kärnor tillgängliga i prenumerationen för användning med HDInsight, samt antalet kärnor som allokerats till det här klustret och hur de är allokerade för noder i det här klustret. Se [lista och visa kluster](#list-and-show-clusters).

> [!IMPORTANT]
> För att övervaka de tjänster som tillhandahålls av HDInsight-klustret, måste du använda Ambari Web eller Ambari REST API. Läs mer om hur du använder Ambari [hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Öppna Användargränssnittet för Hadoop
För att övervaka klustret, bläddra i filsystemet och kontrollera loggarna, klickar du på **Hadoop UI** i HDInsight frågekonsolen. Se [öppna HDInsight frågekonsol](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Öppna Yarn UI
Om du vill använda Yarn-användargränssnittet, klickar du på **Yarn-Användargränssnittet** i HDInsight frågekonsolen. Se [öppna HDInsight frågekonsol](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Ansluta till ett kluster med RDP
Autentiseringsuppgifterna för det kluster som du angav när den har skapandet ge åtkomst till tjänster på klustret, men inte själva klustret via fjärrskrivbord. Du kan aktivera Remote Desktop åtkomst när du etablerar ett kluster eller när ett kluster har etablerats. Anvisningar om hur du aktiverar Remote Desktop när du skapar finns i [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

**Aktivera Fjärrskrivbord**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** i den vänstra menyn klickar du på **HDInsight-kluster**, klicka på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och sedan på **Remote Desktop**.
4. Ange **upphör att gälla på**, **Remote Desktop användarnamn** och **Remote Desktop lösenord**, och klicka sedan på **aktivera**.

    ![HDInsight-aktivera, inaktivera Konfigurera fjärrskrivbord](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Standardvärden för upphör att gälla på är en vecka.

   > [!NOTE]
   > Du kan också använda HDInsight .NET SDK för att aktivera Fjärrskrivbord i ett kluster. Använd den **EnableRdp** metoden på HDInsight-klientobjekt på följande sätt: **klienten. EnableRdp (klusternamn, plats, ”rdpuser”, ”rdppassword”, DateTime.Now.AddDays(6))**. På samma sätt för att inaktivera Fjärrskrivbord på klustret, du kan använda **klienten. DisableRdp (klusternamn, plats)**. Läs mer om dessa metoder, [HDInsight .NET SDK-referens](http://go.microsoft.com/fwlink/?LinkId=529017). Detta gäller endast för HDInsight-kluster som körs på Windows.
   >
   >

**Att ansluta till ett kluster med RDP**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** i den vänstra menyn klickar du på **HDInsight-kluster**, klicka på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och sedan på **Remote Desktop**.
4. Klicka på **Connect** och följ instruktionerna. Om Connect är inaktivera, måste du aktivera det först. Kontrollera att med hjälp av fjärrskrivbord användarens användarnamn och lösenord.  Du kan inte använda autentiseringsuppgifter för klustret.

## <a name="open-hadoop-command-line"></a>Öppna Hadoop-kommandoraden
Om du vill ansluta till klustret med hjälp av fjärrskrivbord och använder Hadoop-kommandoraden, måste du först ha aktiverat Fjärrskrivbord åtkomst till klustret enligt beskrivningen i föregående avsnitt.

**Öppna en kommandorad för Hadoop**

1. Ansluta till klustret med hjälp av fjärrskrivbord.
2. Från skrivbordet, dubbelklickar du på **Hadoop-kommandoraden**.

    ![HDI. HadoopCommandLine][image-hadoopcommandline]

    Mer information om Hadoop-kommandon finns i [Hadoop-kommandon referens](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Mappnamnet har Hadoop-versionsnumret som är inbäddad i föregående skärmbild. Versionsnumret kan ändras baserat på vilken version av Hadoop-komponenterna installeras i klustret. Du kan använda Hadoop miljövariabler för att referera till dessa mappar. Exempel:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du skapar ett HDInsight-kluster med hjälp av portalen och hur du öppnar kommandoradsverktyget Hadoop. Mer information finns i följande artiklar:

* [Administrera HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrera HDInsight med hjälp av Azure CLI](hdinsight-administer-use-command-line.md)
* [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Skicka Hadoop-jobb programmässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Vilken version av Hadoop finns i Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop-kommandoraden"
