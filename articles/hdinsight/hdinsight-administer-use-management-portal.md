---
title: "Hantera Windows-baserade Hadoop-kluster i HDInsight med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig hur du administrerar HDInsight Service. Skapa ett HDInsight-kluster, öppna interaktiva JavaScript-konsolen och öppna konsolen Hadoop-kommando."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0b34f861b306dc62cbdbf97378a1c370494bbb20
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Hantera Windows-baserade Hadoop-kluster i HDInsight med hjälp av Azure portal

Med hjälp av den [Azure-portalen][azure-portal], kan du skapa Windows-baserade Hadoop-kluster i Azure HDInsight, ändra Hadoop användarlösenord och aktivera Remote Desktop Protocol (RDP) så att du kan använda kommandot Hadoop -konsolen på klustret.

Informationen i den här artikeln gäller bara för Windows-baserade HDInsight-kluster. Information om hur du hanterar Linux-baserade kluster finns i [hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure Storage-konto** -ett HDInsight-kluster använder en Azure Blob storage-behållare som filsystemet. Mer information om hur Azure Blob storage ger en sömlös upplevelse med HDInsight-kluster finns [använda Azure Blob Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md). Mer information om hur du skapar ett Azure Storage-konto finns [hur du skapar ett Lagringskonto](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Öppna portalen
1. Logga in på [https://portal.azure.com](https://portal.azure.com).
2. När du öppnar portalen kan du:

   * Klicka på **skapar du en resurs** i den vänstra menyn för att skapa ett nytt kluster:

       ![ny knapp för HDInsight-kluster](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Klicka på **HDInsight-kluster** i den vänstra menyn.

       ![Azure portal HDInsight-kluster-knappen](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Om **HDInsight** inte visas i den vänstra menyn klickar du på **Bläddra**.

     ![Azure portal knappen Bläddra klustret.](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Skapa kluster
Skapa-instruktioner med hjälp av portalen finns i [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight fungerar med en bred Hadoop-komponenter. Lista över de komponenter som har verifierats, och som stöds finns i [vilken version av Hadoop finns i Azure HDInsight](hdinsight-component-versioning.md). Du kan anpassa HDInsight med hjälp av något av följande alternativ:

* Använd skriptåtgärder om du vill köra anpassade skript som kan anpassa ett kluster om du vill ändra klusterkonfigurationen eller installera anpassade komponenter, till exempel Giraph eller Solr. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster.md).
* Använd anpassning Klusterparametrar i Azure PowerShell eller HDInsight .NET SDK när klustret skapas. Ändringarna sparas sedan via livslängden för klustret och påverkas inte av klustret nod reimages som Azure-plattformen utför med jämna mellanrum för underhåll. Mer information om hur du använder anpassning Klusterparametrar finns [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).
* Vissa inbyggda Java-komponenter som Mahout och kaskad, kan köras i klustret som JAR-filer. Dessa JAR-filer kan distribueras till Azure Blob storage och skickas till HDInsight-kluster med Hadoop-jobbet skicka mekanismer. Mer information finns i [skicka Hadoop-jobb via programmering](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Om du har problem med distribution av JAR-filer till HDInsight-kluster eller anropa JAR-filer på HDInsight-kluster, kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/).
  >
  > Sammanhängande stöds inte av HDInsight och är inte tillgänglig för Microsofts Support. Listor med stödda komponenter finns [vad är nytt i klusterversioner som tillhandahålls av HDInsight](hdinsight-component-versioning.md).
  >
  >

Installation av anpassade program i klustret med hjälp av anslutning till fjärrskrivbord stöds inte. Du bör inte lagra filer för enheter på huvudnoden, eftersom de kommer att förloras om du behöver skapa nytt kluster. Vi rekommenderar att du lagrar filer på Azure Blob storage. BLOB storage är permanent.

## <a name="list-and-show-clusters"></a>Listan och visa kluster
1. Logga in på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn.
3. Klicka på klusternamnet. Om klustret är långt, kan du använda filter överst på sidan.
4. Dubbelklicka på ett kluster från listan om du vill visa detaljerad information.

    **Menyn och essentials**:

    ![Azure portal HDInsight-kluster essentials](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Om du vill anpassa menyn högerklickar du någonstans på menyn och klicka sedan på **anpassa**.
   * **Inställningar för** och **alla inställningar**: Visar den **inställningar** bladet för klustret, där du kan komma åt detaljerad konfigurationsinformation för klustret.
   * **Instrumentpanelen**, **Klusterinstrumentpanel** och **URL: dessa är alla sätt att komma åt instrumentpanelen kluster, vilket är Ambari Web för Linux-baserade kluster. -**Secure Shell **: Visar instruktionerna för att ansluta till klustret med SSH (Secure Shell)-anslutning.
   * **Skala klustret**: du kan ändra antalet arbetarnoder för det här klustret.
   * **Ta bort**: tar bort klustret.
   * **Snabbstart**: Visar information som hjälper dig att komma igång med HDInsight.
   * ** Användare: Om du vill ange behörigheter för *portal management* för detta kluster för andra användare på Azure-prenumerationen.

     > [!IMPORTANT]
     > Detta *endast* påverkar åtkomst och behörighet till detta kluster i Azure-portalen och har ingen effekt på vem som kan ansluta till eller skicka jobb till HDInsight-klustret.
     >
     >
   * **Taggar**: taggar kan du ange nyckel/värde-par för att definiera en anpassad taxonomi av dina molntjänster. Du kan till exempel skapa en nyckel som heter **projekt**, och sedan använda ett värde som är gemensamma för alla tjänster som är associerad med ett visst projekt.
   * **Ambari Views**: länkar till Ambari Web.

     > [!IMPORTANT]
     > För att hantera tjänster som tillhandahålls av HDInsight-kluster, måste du använda Ambari Web eller Ambari REST API. Läs mer om hur du använder Ambari [hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Användning**:

     ![Azure portal HDInsight-kluster användning](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Klicka på **inställningar**.

    ![Azure portal HDInsight-kluster användning](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Egenskaper för**: Visa egenskaper för klustret.
   * **Kluster-AAD-identitet**:
   * **Azure Storage-nycklar**: Visa standardkontot för lagring och dess nyckel. Lagringskontot är konfiguration när klustret skapas.
   * **Klustret inloggningen**: ändra klustret HTTP-användarnamn och lösenord.
   * **Externa Metastores**: Visa Hive och Oozie metastores. Metastores kan bara konfigureras när klustret skapas.
   * **Skala klustret**: öka och minska antalet arbetarnoder i klustret.
   * **Fjärrskrivbord**: aktivera och inaktivera åtkomst via fjärrskrivbord (RDP) och konfigurera RDP-användarnamnet.  RDP-användarnamnet måste vara samma som HTTP-användarnamn.
   * **Auktoriserad partner**:

     > [!NOTE]
     > Det här är en generisk lista över tillgängliga inställningar. inte alla finnas för alla typer av klustret.
     >
     >
6. Klicka på **egenskaper**:

    Egenskapsavsnittet innehåller följande:

   * **Hostname**: klustrets namn.
   * **Kluster-URL: en**.
   * **Status för**: inkludera avbröts, godkända ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, fungerar, kör, fel, ta bort, tas bort, för lång tid, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued ResizeQueued, ClusterCustomization
   * **Region**: Azure-plats. En lista över Azure platser som stöds finns i **Region** nedrullningsbar listruta i [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Data som har skapats**.
   * **Operativsystemet**: antingen **Windows** eller **Linux**.
   * **Typen**: Hadoop, HBase, Storm, Väck.
   * **Version**. Se [HDInsight-versioner](hdinsight-component-versioning.md)
   * **Prenumerationen**: prenumerationsnamn.
   * **Prenumerations-ID**.
   * **Primär datakälla**. Azure Blob storage-konto som används som standard Hadoop-filsystem.
   * **Arbetsnoderna prisnivån**.
   * **Huvudnod prisnivån**.

## <a name="delete-clusters"></a>Ta bort kluster
Ett kluster tas inte bort standardkontot för lagring eller eventuella länkade lagringskonton. Du kan återskapa klustret med hjälp av samma lagringskonton och samma metastores.

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** från den vänstra menyn klickar du på **HDInsight-kluster**, klickar du på klusternamnet.
3. Klicka på **ta bort** på den översta menyn och följ sedan instruktionerna.

Se även [paus/stängs av kluster](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Skala kluster
Klustret skalning funktionen kan du ändra antalet arbetarnoder som används av ett kluster som körs i Azure HDInsight utan att behöva återskapa klustret.

> [!NOTE]
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av klustret kan kontrollera du egenskapssidan.  Se [listan och visa](#list-and-show-clusters).
>
>

Konsekvenser av att ändra antalet datanoder som för varje typ av kluster som stöds av HDInsight:

* Hadoop

    Sömlöst kan du öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller körs jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras korrekt så att klustret alltid kvar i ett fungerande tillstånd.

    När ett Hadoop-kluster skalas ned genom att minska antalet datanoder som, en del av tjänsterna i klustret har startats om. Detta medför att alla körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* HBase

    Du kan sömlöst Lägg till eller ta bort noder till HBase-kluster, medan den körs. Regional servrar balanseras automatiskt inom några minuter för att slutföra åtgärden. skalning. Du kan också manuellt balansera regionala servrar genom att logga in på headnode i klustret och köra följande kommandon från en kommandotolk:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Mer information om hur du använder HBase-gränssnittet finns i]
* Storm

    Du kan sömlöst lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Men efter en lyckad skalning åtgärden har slutförts, behöver du ombalansera topologin.

    Ombalansering kan utföras på två sätt:

  * Storm webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)

    Mer information finns i [Apache Storm dokumentationen](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.

    Storm webbgränssnittet är tillgänglig på HDInsight-kluster:

    ![HDInsight storm skala balansera](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Här är ett exempel så använder du kommandot CLI för att balansera Storm-topologi:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Att skala kluster**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** från den vänstra menyn klickar du på **HDInsight-kluster**, klickar du på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och klicka sedan på **kluster**.
4. Ange **numret för arbetarnoder**. Gränsen för antalet klusternod varierar mellan olika Azure-prenumerationer. Du kan kontakta faktureringssupporten om du vill öka gränsvärdet.  Information om kostnaden återspeglas ändringar i antalet noder.

    ![HDInsight Hadoop HBase, Storm Spark skala](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Pausa/stängs av kluster
De flesta Hadoop-jobb är batchjobb som bara ibland kördes. För de flesta Hadoop-kluster finns stora tidsperioder som klustret inte används för bearbetning. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används.
Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Det finns många sätt kan du programmerar processen:

* Användaren Azure Data Factory. Se [länkad Azure HDInsight-tjänst](../data-factory/compute-linked-services.md) och [transformera och analysera med hjälp av Azure Data Factory](../data-factory/transform-data.md) för HDInsight på begäran och automatisk definierad länkade tjänster.
* Använda Azure PowerShell.  Se [analysera svarta fördröjning](hdinsight-analyze-flight-delay-data.md).
* Använda Azure CLI. Se [hantera HDInsight-kluster med hjälp av Azure CLI](hdinsight-administer-use-command-line.md).
* Använda HDInsight .NET SDK. Se [skicka Hadoop-jobb](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Prisinformation, se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/). Om du vill ta bort ett kluster från portalen finns [ta bort kluster](#delete-clusters)

## <a name="change-cluster-username"></a>Ändra kluster användarnamn
Ett HDInsight-kluster kan ha två användarkonton. Användarkonto för HDInsight-kluster skapas under skapandeprocessen. Du kan också skapa ett användarkonto för RDP för åtkomst till klustret via RDP. Se [aktivera Fjärrskrivbord](#connect-to-hdinsight-clusters-by-using-rdp).

**Ändra HDInsight-kluster-användarnamn och lösenord**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** från den vänstra menyn klickar du på **HDInsight-kluster**, klickar du på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och klicka sedan på **Klusterinloggning**.
4. Om **klusterinloggning** har aktiverats, måste du klicka på **inaktivera**, och klicka sedan på **aktivera** innan du kan ändra det användarnamn och lösenord...
5. Ändra den **klustret inloggningsnamnet** och/eller den **klustret inloggningslösenordet**, och klicka sedan på **spara**.

    ![HDInsight ändra klustret användarnamn lösenord http-användare](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Bevilja/återkalla åtkomst
HDInsight-kluster har följande HTTP-webbtjänster (alla dessa tjänster har RESTful slutpunkter):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Som standard beviljas dessa tjänster för åtkomst. Du kan återkalla/bevilja åtkomst från Azure-portalen.

> [!NOTE]
> Genom att bevilja/återkalla åtkomst, återställs klustret användarnamn och lösenord.
>
>

**Att bevilja/återkalla åtkomst HTTP services webbåtkomst**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** från den vänstra menyn klickar du på **HDInsight-kluster**, klickar du på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och klicka sedan på **Klusterinloggning**.
4. Om **klusterinloggning** har aktiverats, måste du klicka på **inaktivera**, och klicka sedan på **aktivera** innan du kan ändra det användarnamn och lösenord...
5. För **klustret inloggning användarnamn** och **klustret inloggningslösenordet**, ange nytt användarnamn och lösenord (respektive) för klustret.
6. Klicka på **SPARA**.

    ![HDInsight grand ta bort HTTP-tjänsten webbåtkomst](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Hitta standardkontot för lagring
Varje HDInsight-kluster har en standardkontot för lagring. Standardkontot för lagring och dess nycklar för ett kluster visas under **inställningar**/**egenskaper**/**Azure Lagringsnycklar**. Se [listan och visa](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Hitta resursgruppen.
I läget Azure Resource Manager skapas varje HDInsight-kluster med en Azure-resursgrupp. Azure-resursgrupp som tillhör ett kluster visas i:

* Listan över kluster har en **resursgruppen** kolumn.
* Klustret **väsentliga** panelen.  

Se [listan och visa](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Öppna konsolen för HDInsight-fråga
Konsolen HDInsight frågan innehåller följande funktioner:

* **Hive Editor**: A GUI Webbgränssnitt för att skicka Hive-jobb.  Se [köra Hive-frågor via konsolen frågan](hadoop/apache-hadoop-use-hive-query-console.md).

    ![HDInsight portal hive-redigeraren](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Jobbhistorik**: övervaka Hadoop-jobb.  

    ![HDInsight portal jobbhistorik](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Klicka på **Frågenamnet** att visa information, inklusive jobbegenskaper, **Jobbfråga**, och ** Jobbutdata. Du kan också hämta både frågan och utdata till din arbetsstation.
* **Filen webbläsare**: Bläddra standardkontot för lagring och länkade storage-konton.

    ![HDInsight portal webbläsare filsökning](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    På skärmbilden, den  **<Account>**  anger objektet är ett Azure storage-konto.  Klicka på namnet på kontot för att bläddra till filerna.
* **Hadoop-Användargränssnittet**.

    ![HDInsight Hadoop UI-portalen](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    Från **Hadoop UI*, kan du bläddra bland filer och loggarna.
* **Yarn UI**.

    ![HDInsight portal YARN-Användargränssnittet](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Köra Hive-frågor
Om du vill köra Hive-jobb från portalen klickar du på **Hive-redigeraren** i konsolen HDInsight frågan. Se [HDInsight fråga konsolen](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Övervaka jobb
Om du vill övervaka jobb från portalen klickar du på **jobbhistorik** i konsolen HDInsight frågan. Se [HDInsight fråga konsolen](#open-hdinsight-query-console).

## <a name="browse-files"></a>Bläddra bland filer
Om du vill bläddra bland filer som lagras i standardkontot för lagring och länkade storage-konton, klickar du på **Filhanteraren** i konsolen HDInsight frågan. Se [HDInsight fråga konsolen](#open-hdinsight-query-console).

Du kan också använda den **Bläddra filsystemet** utility från den **Hadoop UI** i HDInsight-konsolen.  Se [HDInsight fråga konsolen](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Övervaka kluster användning
Den **användning** på HDInsight-klusterbladet visar information om antal kärnor som är tillgängliga för din prenumeration för användning med HDInsight, samt antal kärnor som allokerats till detta kluster och hur de är allokerade för alla noder i klustret. Se [listan och visa](#list-and-show-clusters).

> [!IMPORTANT]
> För att övervaka tjänster som tillhandahålls av HDInsight-kluster, måste du använda Ambari Web eller Ambari REST API. Mer information om hur du använder Ambari finns [hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Öppna Användargränssnittet för Hadoop
Om du vill övervaka klustret Bläddra i filsystemet och loggarna klickar du på **Hadoop UI** i konsolen HDInsight frågan. Se [HDInsight fråga konsolen](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Öppna Yarn UI
Om du vill använda Yarn-användargränssnittet **Yarn-Användargränssnittet** i konsolen HDInsight frågan. Se [HDInsight fråga konsolen](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Anslut till kluster med RDP
Autentiseringsuppgifterna för det kluster som du har angett på skapades ge åtkomst till tjänsterna i klustret, men inte själva klustret via fjärrskrivbord. Du kan aktivera åtkomst via Fjärrskrivbord när du konfigurerar ett kluster eller efter ett kluster har etablerats. Anvisningar om hur du aktiverar fjärrskrivbord på Skapa finns [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

**Aktivera Fjärrskrivbord**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** från den vänstra menyn klickar du på **HDInsight-kluster**, klickar du på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och klicka sedan på **fjärrskrivbord**.
4. Ange **upphör att gälla på**, **användarnamnet för fjärrskrivbord** och **Remote Desktop lösenord**, och klicka sedan på **aktivera**.

    ![HDInsight aktivera inaktivera Konfigurera fjärrskrivbord](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Standardvärden för upphör att gälla på är en vecka.

   > [!NOTE]
   > Du kan också använda HDInsight .NET SDK för att aktivera Fjärrskrivbord på ett kluster. Använd den **EnableRdp** metod på HDInsight klient-objekt på följande sätt: **klienten. EnableRdp (klusternamn, plats, ”rdpuser”, ”rdppassword”, DateTime.Now.AddDays(6))**. På samma sätt för att inaktivera Fjärrskrivbord på klustret, du kan använda **klienten. DisableRdp (klusternamn, platsen)**. Mer information om dessa metoder finns [HDInsight .NET SDK referens](http://go.microsoft.com/fwlink/?LinkId=529017). Detta gäller endast för HDInsight-kluster som körs på Windows.
   >
   >

**Att ansluta till ett kluster med RDP**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **Bläddra bland alla** från den vänstra menyn klickar du på **HDInsight-kluster**, klickar du på klusternamnet.
3. Klicka på **inställningar** från den översta menyn och klicka sedan på **fjärrskrivbord**.
4. Klicka på **Anslut** och följ instruktionerna. Om Connect inaktivera, måste du aktivera den först. Kontrollera med hjälp av fjärrskrivbord användarens användarnamn och lösenord.  Du kan inte använda autentiseringsuppgifter för klustret.

## <a name="open-hadoop-command-line"></a>Öppna kommandoraden för Hadoop
Om du vill ansluta till klustret med hjälp av fjärrskrivbord och använda Hadoop-kommandoraden, måste du först ha aktiverat Fjärrskrivbord åtkomst till klustret enligt beskrivningen i föregående avsnitt.

**Öppna en kommandorad för Hadoop**

1. Anslut till klustret med hjälp av fjärrskrivbord.
2. Från skrivbordet, dubbelklickar du på **Hadoop kommandoraden**.

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Mer information om Hadoop-kommandon finns [Hadoop kommandon referens](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Mappnamnet har versionsnumret Hadoop inbäddade i föregående skärmbild. Versionsnumret kan ändras baserat på vilken version av Hadoop-komponenter som installeras i klustret. Du kan använda Hadoop miljövariabler för att referera till dessa mappar. Exempel:

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
* [Skicka Hadoop-jobb via programmering](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Komma igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Vilken version av Hadoop finns i Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop-kommandorad"
