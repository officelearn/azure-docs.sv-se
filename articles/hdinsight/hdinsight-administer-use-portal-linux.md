---
title: Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure portal
description: Lär dig mer om att skapa och hantera HDInsight-kluster med Azure portal.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: hrasheed
ms.openlocfilehash: 3258f4d36d80c2a501e4ceba3b428128fc3f781b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201015"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Med hjälp av den [Azure-portalen][azure-portal], du kan hantera [Apache Hadoop](https://hadoop.apache.org/) kluster i Azure HDInsight. Använd flikväljaren ovan för information om hur du hanterar Hadoop-kluster i HDInsight med andra verktyg.

**Förutsättningar**
- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Ett befintligt Apache Hadoop-kluster i HDInsight.  Se [skapa Linux-baserade kluster i HDInsight med Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Komma igång
Logga in på [https://portal.azure.com](https://portal.azure.com).


## <a name="showClusters"></a> Lista och visa kluster
Den **HDInsight-kluster** din befintliga kluster visas på sidan.  Från portalen:
1. Välj **alla tjänster** menyn till vänster.
2. Välj **HDInsight-kluster** under **ANALYTICS**.

## <a name="homePage"></a> Startsida för kluster 
Välj klusternamnet från den [ **HDInsight-kluster** sidan](#showClusters).  Då öppnas det **översikt** vyn ser ut som följande bild:

![Azure portal HDInsight-kluster essentials](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Översta menyn:**  
- **Flytta**: Flyttar klustret till en annan resursgrupp eller till en annan prenumeration.  
- **Ta bort**: Tar bort klustret.  
- **Uppdatera**:  Uppdaterar vyn.

**Menyn till vänster:**  
 - **Övre vänstra meny**  
    - **Översikt över**:  Innehåller allmän information för klustret.
    -  **Aktivitetsloggen**: Visa och fråga efter aktivitetsloggar.
    - **Åtkomstkontroll (IAM)**: Använd rolltilldelningar.  Se [använda rolltilldelningar för att hantera åtkomst till din Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md).
    - **Taggar**: Kan du ange nyckel/värde-par för att definiera en anpassad taxonomi för cloud services. Du kan till exempel skapa en nyckel med namnet **projekt**, och sedan använda gemensamt värde för alla tjänster som är associerade med ett specifikt projekt.
    - **Diagnostisera och lösa problem**: Visa felsökningsinformation.
    - **Snabbstart**:  Visar information som hjälper dig att komma igång med HDInsight.
    - **Verktyg**: Hjälpinformation för HDInsight relaterade verktyg.

- **Inställningsmenyn**  
  - **Klusterstorlek**: Kontrollera, öka och minska antalet arbetarnoder i klustret. Se [skala kluster](hdinsight-administer-use-portal-linux.md#scale-clusters).
  - **Kvotgränser**: Visa använda och tillgängliga kärnor för prenumerationen.
  - **SSH + klusterinloggning**: Visar instruktioner för att ansluta till klustret med Secure Shell (SSH)-anslutning. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).
  - **Data Lake Storage Gen1**: Konfigurera åtkomst till Data Lake Storage Gen1.  Gå till [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
  - **Storage-konton**: Visa storage-konton och nycklar. Storage-konton har konfigurerats när klustret skapas.
  - **Program**: Lägg till/ta bort HDInsight-program.  Se [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).
  - **Skriptåtgärder**: Kör Bash-skript på klustret. Se [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).
  - **Externa metastores**: Visa den [Apache Hive](https://hive.apache.org/) och [Apache Oozie](https://oozie.apache.org/) metastores. Metastores kan bara konfigureras när klustret skapas.
  - **HDInsight-partner**: Lägg till/ta bort aktuell HDInsight-Partner.
  - **Egenskaper för**: Visa den [Klusteregenskaper](#properties).
  - **Låser**: Lägg till ett lås för att förhindra att klustret att ändras eller tas bort.
  -  **Automationsskript**: Visa och exportera Azure Resource Manager-mall för klustret. För närvarande kan exportera du bara beroende Azure storage-kontot. Se [skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

- **Övervakning av menyn**
  - **Aviseringar**: Hantera aviseringar och åtgärder.
  - **Mått**: Övervaka klustermått i Azure Monitor-loggar.
  - **Diagnosinställningar**: Inställningarna på var du vill lagra diagnos mätvärden.
  - **Operations Management Suite**:  Övervaka ditt kluster i Azure Operations Management Suite (OMS) och Azure Monitor-loggar.

- **Support och felsökning menyn**
  - **Resurshälsa**: Se [översikt över hälsotillståndet för Azure-resurs](../service-health/resource-health-overview.md).
  - **Ny supportförfrågan**: Kan du skapa ett supportärende hos Microsoft support.
    
## <a name="properties"></a> Egenskaper för klustret
Från den [kluster startsida](#homePage)under **inställningar** Välj **egenskaper**.
* **Hostname**: Klustrets namn.
* **Klustrets URL**: URL till Ambari-webbgränssnittet.
* **Secure shell (SSH)**: Användarnamn och värden namnet ska användas i åtkomst till klustret via SSH.
* **Status**: En av: Avbröts, godkända ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, drift, kör, fel, tar bort, tas bort, nådde sin tidsgräns, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, eller ClusterCustomization.
* **Region**: Azure-plats. En lista över platser som stöds Azure finns i den **Region** nedrullningsbara listrutan på [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Skapandedatum**: Det datum som klustret har distribuerats.
* **Operativsystem**: Antingen **Windows** eller **Linux**.
* **Typ**: Hadoop, HBase, Storm, Spark.
* **Version**. Se [HDInsight versioner](hdinsight-component-versioning.md).
* **Prenumeration**: Prenumerationsnamn.
* **Standarddatakällan**: Standardfilsystemet för klustret.
* **Noder arbetsstorlek**: Den valda storleken på arbetsnoderna.
* **Gå nodstorlek**: Valda VM-storleken för huvudnoderna.
* **Virtuellt nätverk**: Namnet på det virtuella nätverket där klustret distribueras om en valdes vid tidpunkten för distribution.

## <a name="move-clusters"></a>Flytta kluster

Du kan flytta ett HDInsight-kluster till en annan Azure-resursgrupp eller en annan prenumeration. 

Från den [kluster startsida](#homePage):

1. Välj **flytta** på den översta menyn.
2. Välj **flytta till en annan resursgrupp** eller **flytta till en annan prenumeration**.
3. Följ instruktionerna från den nya sidan.

## <a name="delete-clusters"></a>Ta bort kluster
Tar bort ett kluster tas inte bort standardkontot för lagring eller eventuella länkade lagringskonton. Du kan återskapa klustret med hjälp av samma lagringskonton och samma metastores. Vi rekommenderar att du använder en ny standardbehållaren när du skapar klustret igen.

Från den [kluster startsida](#homePage):

1. Välj **ta bort** på den översta menyn.
2. Följ instruktionerna från den nya sidan.

Se även [pausa/Stäng kluster](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Du kan lägga till ytterligare Azure Storage-konton och Azure Data Lake Storage-konton när ett kluster har skapats. Mer information finns i [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="scale-clusters"></a>Skala kluster
Klusterskalning funktionen kan du ändra antalet arbetarnoder som används av ett Azure HDInsight-kluster, utan att behöva återskapa klustret.

> [!NOTE]  
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av ditt kluster kan kontrollera du egenskapssidan.  Visa lista och visa kluster.

Från den [kluster startsida](#homePage):

1. Under **inställningar**väljer **klusterstorlek**.
2. Ange **många av arbetsnoder** i textrutan numeriskt. Gränsen för antalet klusternoder varierar mellan Azure-prenumerationer. Du kan kontakta faktureringssupporten om du vill öka gränsen.  Information om kostnaden ändringarna du gjort i antalet noder.
3. Välj **Spara**.

    ![HDInsight hadoop hbase storm spark skala](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

Effekten av att ändra antalet datanoder varierar för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop

    Du kan smidigt öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller pågående jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras ett smidigt sätt så att klustret finns alltid kvar i funktionsdugligt tillstånd.

    När ett Hadoop-kluster skalas genom att minska antalet datanoder några tjänster i klustret startas om. Detta medför allt körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* Apache HBase

    Du kan smidigt lägga till eller ta bort noder till HBase-kluster medan den körs. Regionservrar balanseras automatiskt inom ett par minuter efter att du skalar igen. Du kan också manuellt balansera regionservrar genom att logga in till huvudnod i klustret och köra följande kommandon från en kommandotolk:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Mer information om hur du använder HBase-gränssnittet finns i [Kom igång med Apache HBase-exempel i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Du kan smidigt lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Efter slutförande av skalning åtgärden kommer du dock behöva balansera om topologin.

    Ombalansering kan utföras på två sätt:

  * Storm-Webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)

    Referera till den [Apache Storm-dokumentationen](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.

    Storm-webbgränssnittet finns på HDInsight-klustret:

    ![HDInsight Storm skala ombalansering](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Här är ett exempel CLI-kommando för att balansera om Storm-topologi:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>Pausa/Stäng kluster

De flesta Hadoop-jobb är batchjobb som endast körs ibland. Det finns stora tidsperioder som klustret inte används för bearbetning för de flesta Hadoop-kluster. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används.
Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Det finns många sätt som du kan programmera processen:

* Användaren Azure Data Factory. Se [skapa på begäran Linux-baserade Apache Hadoop-kluster i HDInsight med Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) länkade tjänster för att skapa HDInsight på begäran.
* Använd Azure PowerShell.  Se [analysera flygförseningsdata](hdinsight-analyze-flight-delay-data-linux.md).
* Använda Azure klassiska CLI. Se [hantera HDInsight-kluster med klassiska Azure-CLI](hdinsight-administer-use-command-line.md).
* Använda HDInsight .NET SDK. Se [skicka Apache Hadoop-jobb](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Prisinformationen, se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/). Om du vill ta bort ett kluster från portalen, se [ta bort kluster](#delete-clusters)



## <a name="upgrade-clusters"></a>Uppgradera kluster

Se [uppgradera HDInsight-kluster till en nyare version](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Öppna Apache Ambari-webbgränssnittet

Ambari innehåller ett intuitivt, enkelt att använda Hadoop management webbgränssnittet backas upp av dess RESTful API: er. Ambari gör det möjligt för systemadministratörer att hantera och övervaka Hadoop-kluster.

Från den [kluster startsida](#homePage):

1. Välj **Klusterinstrumentpaneler**.

    ![Menyn för HDInsight Hadoop-kluster](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Välj **Ambari home** från den nya sidan.
2. Ange kluster användarnamn och lösenord.  Klustret Standardanvändarnamnet är _admin_. Ambari web UI som ser ut som:

Mer information finns i [hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Ändra lösenord
Ett HDInsight-kluster kan ha två användarkonton. I HDInsight-kluster användarkonto (alias) HTTP-användarkonto) och SSH-användarkontot ska skapas under skapandeprocessen. Du kan använda portalen för att ändra lösenordet för användarkontot kluster och skriptåtgärder för att ändra SSH-användarkontot.

### <a name="change-the-cluster-user-password"></a>Ändra användarlösenord kluster

> [!NOTE]  
> Ändra lösenordet för användaren (admin) kluster kan det orsaka skriptåtgärder som körs mot den här klustret slutar fungera. Om du har några beständiga skriptåtgärder arbetsnoder som mål, misslyckas skripten när du lägger till noder i klustret via ändra storlek på åtgärder. Mer information om skriptåtgärder finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

Från den [kluster startsida](#homePage):
1. Välj **SSH- och logga in** under **inställningar**.
2. Välj **Återställ autentiseringsuppgifter**.
3. Ange och Bekräfta nytt lösenord i rutorna.
4. Välj **OK**.

Lösenordet har ändrats på alla noder i klustret.

### <a name="change-the-ssh-user-password"></a>Ändra SSH-användarlösenordet
1. Använd en textredigerare och spara följande text som en fil med namnet **changepassword.sh**.

    > [!IMPORTANT]  
    > Du måste använda ett redigeringsprogram som använder LF som raden slutar. Om redigeraren använder CRLF, fungerar inte skriptet.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Ladda upp filen till en lagringsplats som kan nås från HDInsight med hjälp av en HTTP eller HTTPS-adress. Till exempel lagra en fil för offentlig, t.ex OneDrive eller Azure Blob storage. Spara URI: N (HTTP eller HTTPS-adress) till filen, eftersom denna URI krävs i nästa steg.
3. Från den [kluster startsida](#homePage) Välj **skriptåtgärder** under **inställningar**.
4. Från den **skriptåtgärder** bladet väljer **Skicka ny**. 
5. Från den **skicka skriptåtgärd** bladet anger du följande information:

   | Fält | Värde |
   | --- | --- |
   | Skripttyp | Välj **– anpassade** från den nedrullningsbara listan.|
   | Namn |”Ändra ssh lösenord” |
   | Bash-skript-URI |URI: N till filen changepassword.sh |
   | Nodtyper: (Head, Worker, Nimbus, övervakaren, Zookeeper, osv.) |✓ för alla nodtyper visas |
   | Parametrar |Ange SSH-användarnamn och sedan det nya lösenordet. Det bör finnas ett blanksteg mellan användarnamnet och lösenordet. |
   | Spara den här skriptåtgärden... |Lämna det här fältet ska vara avmarkerat. |
5. Välj **skapa** tillämpas skriptet. När skriptet är färdigt är du kan ansluta till klustret med SSH med det nya lösenordet.

## <a name="grantrevoke-access"></a>Bevilja/återkalla åtkomst
HDInsight-kluster har följande HTTP-webbtjänster (alla dessa tjänster har RESTful-slutpunkter):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Som standard beviljas de här tjänsterna för åtkomst. Du kan återkalla/bevilja åtkomst med hjälp av [klassiska Azure-CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) och [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Hitta prenumerations-ID
Varje kluster är kopplad till en Azure-prenumeration.  Azure-prenumerationen ID: T är synliga från den [kluster startsida](#homePage).

## <a name="find-the-resource-group"></a>Hitta resursgruppen.
Varje HDInsight-klustret har skapats med en Azure Resource Manager-grupp i Azure Resource Manager-läge. Resource Manager-gruppen är synliga från den [kluster startsida](#homePage).

## <a name="find-the-storage-accounts"></a>Hitta storage-konton
HDInsight-kluster använder en Azure Storage-konto eller Azure Data Lake Storage för att lagra data. Varje HDInsight-kluster kan ha en standardkontot för lagring och ett antal länkade storage-konton. Visa en lista över storage-konton från den [kluster startsida](#homePage) under **inställningar**väljer **lagringskonton**.


## <a name="monitor-jobs"></a>Övervaka jobb
Se [hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md#monitoring).


## <a name="monitor-cluster-usage"></a>Övervaka kluster användning
Den **användning** på HDInsight-klusterbladet visar information om hur många kärnor tillgängliga i prenumerationen för användning med HDInsight, samt antalet kärnor som allokerats till det här klustret och hur de är allokerade för noder i det här klustret. Visa lista och visa kluster.

> [!IMPORTANT]  
> För att övervaka de tjänster som tillhandahålls av HDInsight-klustret, måste du använda Ambari Web eller Ambari REST API. Läs mer om hur du använder Ambari [hantera HDInsight-kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Anslut till ett kluster

* [Använda Apache Hive med HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig vissa grundläggande administrativa funktioner. Mer information finns i följande artiklar:

* [Administrera HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrera HDInsight med hjälp av Azure klassiskt CLI](hdinsight-administer-use-command-line.md)
* [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Läs mer om hur du använder Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Information om hur du använder Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Använda Apache Hive i HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig i HDInsight](hadoop/hdinsight-use-pig.md)
* [Använd Apache Sqoop i HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Vilken version av Apache Hadoop är i Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop-kommandoraden"
