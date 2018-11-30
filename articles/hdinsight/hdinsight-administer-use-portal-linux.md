---
title: Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure portal
description: Lär dig mer om att skapa och hantera HDInsight-kluster med Azure portal.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: hrasheed
ms.openlocfilehash: 77f1d0f9d1f9ad07b977750e7701b660781d63e5
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499383"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Med hjälp av den [Azure-portalen][azure-portal], du kan hantera [Apache Hadoop](https://hadoop.apache.org/) kluster i Azure HDInsight. Använd flikväljaren ovan för information om hur du hanterar Hadoop-kluster i HDInsight med andra verktyg.

**Krav**

Om du vill följa stegen i den här artikeln behöver du en **Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Öppna Azure portal
1. Logga in på [https://portal.azure.com](https://portal.azure.com).
2. När du öppnar portalen, kan du:

   * Klicka på **skapa en resurs** från den vänstra menyn för att skapa ett nytt kluster:

       ![knapp för ny HDInsight-kluster](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Ange **HDInsight** i **Sök på Marketplace**, klickar du på **HDInsight**, och klicka sedan på **skapa**.

   * Klicka på **HDInsight-kluster** i den vänstra menyn Visa en lista över befintliga kluster:

       ![Azure HDInsight-kluster portalknappen](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Om du inte ser den **HDInsight-kluster** knappen och klicka sedan på **HDInsight-kluster** under den **information + analys** avsnittet.


## <a name="create-clusters"></a>Skapa kluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight fungerar med en bred Hadoop-komponenter. Lista över de komponenter som verifieras och som stöds finns i [vilken version av Apache Hadoop är i Azure HDInsight?](hdinsight-component-versioning.md) Allmän kluster skapas information finns i [skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Åtkomstkontrollkrav

Du måste ange en Azure-prenumeration när du skapar ett HDInsight-kluster. Klustret kan skapas i en ny Azure-resursgrupp eller i en befintlig resursgrupp. Du kan använda följande steg för att kontrollera dina behörigheter för att skapa HDInsight-kluster:

- Om du vill skapa en ny resursgrupp:

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Klicka på **prenumeration** menyn till vänster. Den har en gul nyckelikonen. Du bör se en lista över prenumerationer.
    3. Klicka på den prenumeration som används för att skapa kluster. 
    4. Klicka på **Mina behörigheter**.  Den visar dina [rollen](../role-based-access-control/built-in-roles.md) för prenumerationen. Du behöver minst deltagaråtkomst till att skapa HDInsight-kluster.

- Använda en befintlig resursgrupp:

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Klicka på **resursgrupper** i den vänstra menyn Visa en lista över resursgrupper.
    3. Klicka på resursgruppen som du vill använda för att skapa ditt HDInsight-kluster.
    4. Klicka på **åtkomstkontroll (IAM)**, och kontrollera att du (eller en grupp som du tillhör) ha minst deltagaråtkomst till resursgruppen.

Om du får felet NoRegisteredProviderFound eller MissingSubscriptionRegistration felet, se [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Lista och visa kluster
1. Logga in på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn Visa en lista över befintliga kluster. Om du inte ser **HDInsight-kluster**, klickar du på **alla tjänster** första.
3. Klicka på klusternamnet. Om listan över klustret är lång kan använda du filter överst på sidan.
4. Klicka på ett kluster från listan om du vill se översiktssidan:

    ![Azure portal HDInsight-kluster essentials](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **menyn översikt:**
    * **Instrumentpanelen**: öppnas Ambari-webbgränssnittet för klustret.
    * **Secure Shell**: Visar instruktioner för att ansluta till klustret med Secure Shell (SSH)-anslutning.
    * **Skala kluster**: gör att du kan ändra antalet arbetsnoder för det här klustret.
    * **Flytta**: flyttar klustret till en annan resursgrupp eller till en annan prenumeration.
    * **Ta bort**: tar bort klustret.

**Menyn till vänster:**
    * **Aktivitetsloggar**: visa och fråga aktivitetsloggar.
    * **Åtkomstkontroll (IAM)**: använda rolltilldelningar.  Se [använda rolltilldelningar för att hantera åtkomst till din Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md).
    * **Taggar**: kan du ange nyckel/värde-par för att definiera en anpassad taxonomi för cloud services. Du kan till exempel skapa en nyckel med namnet **projekt**, och sedan använda gemensamt värde för alla tjänster som är associerade med ett specifikt projekt.
* **Diagnostisera och lösa problem**: visa felsökningsinformation.
    * **Snabbstart**: Visar information som hjälper dig att komma igång med HDInsight.
    * **Verktyg för HDInsight**: hjälpinformation för HDInsight relaterade verktyg.
**Inställningar**
* **Klusterstorlek**: Kontrollera, öka och minska antalet arbetarnoder i klustret. Se [skala kluster](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **Kvotgränser**: visa använda och tillgängliga kärnor för prenumerationen.
    * **SSH + klusterinloggning**: Visar instruktioner för att ansluta till klustret med Secure Shell (SSH)-anslutning. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).
* **Data Lake Store Gen1**: Konfigurera åtkomst till Data Lake Store Gen1.  Se [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
    * **Storage-konton**: Visa storage-konton och nycklar. Storage-konton har konfigurerats när klustret skapas.
    * **Program**: Lägg till/ta bort HDInsight-program.  Se [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).
    * **Skriptåtgärder**: kör Bash-skript på klustret. Se [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).
    * **HDInsight-Partner**: Lägg till/ta bort aktuell HDInsight-Partner.
    * **Egenskaper för**: Visa egenskaper för klustret.
* **Låser**: Lägg till ett lås för att förhindra att klustret som ändrats eller tagits bort.
    * **Automationsskript**: visa och exportera Azure Resource Manager-mall för klustret. För närvarande kan exportera du bara beroende Azure storage-kontot. Se [skapa Linux-baserade Apache Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
**Övervakning**
* **Alters**: Hantera aviseringar och åtgärder.
    * **Mått**: övervaka klustermått i Azure Log Analytics.
* **Diagnosinställningar**: inställningarna på var du vill lagra mått som du kan undersöka **Support och felsökning**
    * **Resurshälsa**: se [översikt över hälsotillståndet för Azure-resurs](../service-health/resource-health-overview.md).
    * **Ny supportförfrågan**: gör att du kan skapa ett supportärende hos Microsoft support.

6. Klicka på **egenskaper**:

    Egenskaperna är:

   * **Hostname**: klustrets namn.
   * **Klustrets URL**: en för Ambari-webbgränssnittet.
   * **Secure shell (SSH)**: namnet på användarnamn och värden ska användas i åtkomst till klustret via SSH.
   * **Status för**: en av: avbröts, godkända ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, drift, kör, fel, tar bort, tas bort, nådde sin tidsgräns, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued eller ClusterCustomization.
   * **Region**: Azure-plats. En lista över platser som stöds Azure finns i den **Region** nedrullningsbara listrutan på [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Skapandedatum**: det datum som klustret har distribuerats.
   * **Operativsystemet**: antingen **Windows** eller **Linux**.
   * **Typ**: Hadoop, HBase, Storm, Spark.
   * **Version**. Se [HDInsight versioner](hdinsight-component-versioning.md).
   * **Prenumeration**: prenumerationsnamn.
   * **Standarddatakällan**: standardfilsystem för klustret.
   * **Noder arbetsstorlek**: valda VM-storleken på arbetsnoderna.
   * **Gå nodstorlek**: valda VM-storleken för huvudnoderna.
   * **Virtuellt nätverk**: namnet på det virtuella nätverket där klustret distribueras om en valdes vid tidpunkten för distribution.

## <a name="delete-clusters"></a>Ta bort kluster
Tar bort ett kluster tas inte bort standardkontot för lagring eller eventuella länkade lagringskonton. Du kan återskapa klustret med hjälp av samma lagringskonton och samma metastores. Vi rekommenderar att du använder en ny standardbehållaren när du skapar klustret igen.

1. Logga in på den [Portal][azure-portal].
2. Klicka på **HDInsight-kluster** menyn till vänster. Om du inte ser **HDInsight-kluster**, klickar du på **alla tjänster** första.
3. Klicka på det kluster som du vill ta bort.
4. Klicka på **ta bort** på den översta menyn och följ sedan instruktionerna.

Se även [pausa/Stäng kluster](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Du kan lägga till ytterligare Azure Storage-konton och Azure Data Lake Store-konton när ett kluster har skapats. Mer information finns i [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="scale-clusters"></a>Skala kluster
Klusterskalning funktionen kan du ändra antalet arbetarnoder som används av ett Azure HDInsight-kluster, utan att behöva återskapa klustret.

> [!NOTE]
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av ditt kluster kan kontrollera du egenskapssidan.  Se [lista och visa kluster](#list-and-show-clusters).
>
>
**Skala kluster**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **HDInsight-kluster** menyn till vänster.
3. Klicka på klustret som du vill skala.
3. Klicka på **skala kluster**.
4. Ange **många av arbetsnoder**. Gränsen för antalet klusternoder varierar mellan Azure-prenumerationer. Du kan kontakta faktureringssupporten om du vill öka gränsen.  Information om kostnaden ändringarna du gjort i antalet noder.

    ![HDInsight hadoop hbase storm spark skala](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

Effekten av att ändra antalet datanoder varierar för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop

    Du kan smidigt öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller pågående jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras ett smidigt sätt så att klustret finns alltid kvar i funktionsdugligt tillstånd.

    När ett Hadoop-kluster skalas genom att minska antalet datanoder några tjänster i klustret startas om. Detta medför allt körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* Apache HBase

    Du kan smidigt lägga till eller ta bort noder till HBase-kluster medan den körs. Regionservrar balanseras automatiskt inom ett par minuter efter att du skalar igen. Du kan också manuellt balansera regionservrar genom att logga in till huvudnod i klustret och köra följande kommandon från en kommandotolk:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Mer information om hur du använder HBase-gränssnittet finns i [Kom igång med Apache HBase-exempel i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Du kan smidigt lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Efter slutförande av skalning åtgärden kommer du dock behöva balansera om topologin.

    Ombalansering kan utföras på två sätt:

  * Storm-Webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)

    Referera till den [Apache Storm-dokumentationen](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.

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
* Använd Azure PowerShell.  Se [analysera flygförseningsdata](hdinsight-analyze-flight-delay-data.md).
* Använda Azure klassiska CLI. Se [hantera HDInsight-kluster med klassiska Azure-CLI](hdinsight-administer-use-command-line.md).
* Använda HDInsight .NET SDK. Se [skicka Apache Hadoop-jobb](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Prisinformationen, se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/). Om du vill ta bort ett kluster från portalen, se [ta bort kluster](#delete-clusters)

## <a name="move-cluster"></a>Flytta klustret

Du kan flytta ett HDInsight-kluster till en annan Azure-resursgrupp eller en annan prenumeration.  Se [lista och visa kluster](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Uppgradera kluster

Se [uppgradera HDInsight-kluster till en nyare version](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Öppna Apache Ambari-webbgränssnittet

Ambari innehåller ett intuitivt, enkelt att använda Hadoop management webbgränssnittet backas upp av dess RESTful API: er. Ambari gör det möjligt för systemadministratörer att hantera och övervaka Hadoop-kluster.

1. Öppna ett HDInsight-kluster från Azure-portalen.  Se [lista och visa kluster](#list-and-show-clusters).
2. Klicka på **kluster instrumentpanelen**.

    ![Menyn för HDInsight Hadoop-kluster](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Ange kluster användarnamn och lösenord.  Klustret Standardanvändarnamnet är _admin_. Ambari web UI som ser ut som:

    ![HDInsight Hadoop Ambari-Webbgränssnittet](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Mer information finns i [hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Ändra lösenord
Ett HDInsight-kluster kan ha två användarkonton. I HDInsight-kluster användarkonto (alias) HTTP-användarkonto) och SSH-användarkontot ska skapas under skapandeprocessen. Du kan använda Ambari-webbgränssnittet för att ändra klusternamn användaren-konto och lösenord och skriptåtgärder för att ändra SSH-användarkonto

### <a name="change-the-cluster-user-password"></a>Ändra användarlösenord kluster
Du kan använda Ambari-Webbgränssnittet för att ändra användarlösenord för klustret. Om du vill logga in på Ambari, måste du använda det befintliga klustret användarnamn och lösenord.

> [!NOTE]
> Ändra lösenordet för användaren (admin) kluster kan det orsaka skriptåtgärder som körs mot den här klustret slutar fungera. Om du har några beständiga skriptåtgärder arbetsnoder som mål, misslyckas skripten när du lägger till noder i klustret via ändra storlek på åtgärder. Mer information om skriptåtgärder finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Logga in på Ambari-Webbgränssnittet på HDInsight-kluster användarens autentiseringsuppgifter. Standardanvändarnamnet är **admin**. URL: en är **https://&lt;HDInsight-klustrets namn > azurehdinsight.net**.
2. Klicka på **Admin** på den översta menyn och klicka sedan på ”Hantera Ambari”.
3. I den vänstra menyn klickar du på **användare**.
4. Klicka på **Admin**.
5. Klicka på **ändra lösenord**.

Ambari ändrar sedan lösenordet på alla noder i klustret.

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
3. Azure-portalen klickar du på **HDInsight-kluster**.
4. Klicka på ditt HDInsight-kluster.
4. Klicka på **skriptåtgärder**.
4. Från den **skriptåtgärder** bladet väljer **Skicka ny**. När den **skicka skriptåtgärd** bladet visas anger du följande information:

   | Fält | Värde |
   | --- | --- |
   | Namn |Ändra ssh lösenord |
   | Bash-skript-URI |URI: N till filen changepassword.sh |
   | Noder (Head, Worker, Nimbus, övervakaren, Zookeeper, osv.) |✓ för alla nodtyper visas |
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

**Hitta din Azure-prenumerations-ID**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **prenumerationer**. Varje prenumeration har ett namn och ett ID.

Varje kluster är kopplad till en Azure-prenumeration. Prenumerationen ID visas i klustret **viktiga** panelen. Se [lista och visa kluster](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Hitta resursgruppen.
Varje HDInsight-klustret har skapats med en Azure Resource Manager-grupp i Azure Resource Manager-läge. Resource Manager-grupp som tillhör ett kluster visas i:

* Listan över kluster har en **resursgrupp** kolumn.
* Klustret **viktiga** panelen.  

Se [lista och visa kluster](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Hitta storage-konton

HDInsight-kluster använder en Azure Storage-konto eller ett Azure Data Lake Store för att lagra data. Varje HDInsight-kluster kan ha en standardkontot för lagring och ett antal länkade storage-konton. Om du vill visa storage-konton du först öppna klustret från portalen och klicka sedan på **lagringskonton**:

![Lagringskonton för HDInsight-kluster](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

I föregående skärmbild, det finns en __standard__ kolumn och anger om kontot är standardkontot för lagring.

Om du vill visa Data Lake Store-konton, klickar du på **Data Lake Store-åtkomst** i föregående skärmbild.

## <a name="run-apache-hive-queries"></a>Kör Apache Hive-frågor
Du kan inte köra Hive-jobb direkt från Azure-portalen, men du kan använda Hive-vyn på Ambari-Webbgränssnittet.

**Att köra Hive-frågor med Ambari Hive-vy**

1. Logga in på Ambari-Webbgränssnittet på HDInsight-kluster användarens autentiseringsuppgifter. Standardanvändarnamnet är **admin**. URL: en är **https://&lt;HDInsight-klustrets namn > azurehdinsight.net**.
2. Öppna Hive-vy som du ser i följande skärmbild:  

    ![HDInsight hive-vyn](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Klicka på **fråga** på den översta menyn.
4. Ange en Hive-fråga i **frågeredigeraren**, och klicka sedan på **kör**.

## <a name="monitor-jobs"></a>Övervaka jobb
Se [hantera HDInsight-kluster med hjälp av Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Bläddra efter filer
Med Azure-portalen kan bläddra du innehållet i behållare som standard.

1. Logga in på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn Visa en lista över befintliga kluster.
3. Klicka på klusternamnet. Om listan över klustret är lång kan använda du filter överst på sidan.
4. Klicka på **Lagringskonton** i den vänstra menyn i klustret.
5. Klicka på ett lagringskonto.
7. Klicka på den **Blobar** panelen.
8. Klicka på standard-behållarnamn.

## <a name="monitor-cluster-usage"></a>Övervaka kluster användning
Den **användning** på HDInsight-klusterbladet visar information om hur många kärnor tillgängliga i prenumerationen för användning med HDInsight, samt antalet kärnor som allokerats till det här klustret och hur de är allokerade för noder i det här klustret. Se [lista och visa kluster](#list-and-show-clusters).

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
