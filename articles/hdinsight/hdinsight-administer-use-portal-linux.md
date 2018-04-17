---
title: Hantera Hadoop-kluster i HDInsight med hjälp av Azure-portalen | Microsoft Docs
description: Lär dig mer om att skapa och hantera HDInsight-kluster med Azure-portalen.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: ea5b19abed0b9d2a2b8131f2be3be14e94e17405
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Hantera Hadoop-kluster i HDInsight med hjälp av Azure portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Med hjälp av den [Azure-portalen][azure-portal], kan du hantera Hadoop-kluster i Azure HDInsight. Använd flikväljaren ovan för information om hur du hanterar Hadoop-kluster i HDInsight med andra verktyg.

**Krav**

Om du vill följa stegen i den här artikeln behöver du en **Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Öppna Azure-portalen
1. Logga in på [ https://portal.azure.com ](https://portal.azure.com).
2. När du öppnar portalen kan du:

   * Klicka på **skapar du en resurs** i den vänstra menyn för att skapa ett nytt kluster:

       ![ny knapp för HDInsight-kluster](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Ange **HDInsight** i **söka Marketplace**, klickar du på **HDInsight**, och klicka sedan på **skapa**.

   * Klicka på **HDInsight-kluster** i den vänstra menyn för att visa befintliga kluster:

       ![Azure portal HDInsight-kluster-knappen](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Om du inte ser den **HDInsight-kluster** knappen och klicka sedan på **HDInsight-kluster** under den **Intelligence + analys** avsnitt.


## <a name="create-clusters"></a>Skapa kluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight fungerar med en bred Hadoop-komponenter. Lista över de komponenter som verifieras och stöds, se [vilken version av Hadoop finns i Azure HDInsight?](hdinsight-component-versioning.md) Allmän klustret skapas information finns i [skapa Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Åtkomstkontrollkrav

När du skapar ett HDInsight-kluster måste du ange en Azure-prenumeration. Klustret kan skapas i en ny Azure resursgrupp eller i en befintlig resursgrupp. Du kan använda följande steg för att verifiera din behörighet för att skapa HDInsight-kluster:

- Om du vill skapa en ny resursgrupp:

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Klicka på **prenumeration** i den vänstra menyn. Den har en gul nyckelikonen. Du bör se en lista över prenumerationer.
    3. Klicka på den prenumeration som du använder för att skapa kluster. 
    4. Klicka på **min behörighet**.  Det visar dina [rollen](../role-based-access-control/overview.md#built-in-roles) för prenumerationen. Du behöver minst deltagare behörighet att skapa HDInsight-kluster.

- Använda en befintlig resursgrupp:

    1. Logga in på [Azure Portal](https://portal.azure.com).
    2. Klicka på **resursgrupper** i den vänstra menyn att lista resursgrupper.
    3. Klicka på den resursgrupp som du vill använda för att skapa ditt HDInsight-kluster.
    4. Klicka på **åtkomstkontroll (IAM)**, och kontrollera att du (eller en grupp som du tillhör) har minst deltagare åtkomst till resursgruppen.

Om du får felet NoRegisteredProviderFound eller MissingSubscriptionRegistration felet, se [felsöka vanliga Azure-distribution med Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Listan och visa kluster
1. Logga in på [ https://portal.azure.com ](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn för att visa befintliga kluster. Om du inte ser **HDInsight-kluster**, klickar du på **alla tjänster** första.
3. Klicka på klusternamnet. Om klustret är långt, kan du använda filter överst på sidan.
4. Klicka på ett kluster från listan som ska visas på översiktssidan:

    ![Azure portal HDInsight-kluster essentials](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **översikt-menyn:**
    * **Instrumentpanelen**: öppnar det Ambari-webbgränssnittet för klustret.
    * **Secure Shell**: Visar instruktionerna för att ansluta till klustret med SSH (Secure Shell)-anslutning.
    * **Skala klustret**: du kan ändra antalet arbetarnoder för det här klustret.
    * **Flytta**: flyttar klustret till en annan resursgrupp eller till en annan prenumeration.
    * **Ta bort**: tar bort klustret.

    **Vänstra menyn:**
    * **Aktivitetsloggar**: visa och fråga aktivitetsloggar.
    * **Åtkomstkontroll (IAM)**: Använd rolltilldelningar.  Se [använda rolltilldelningar för att hantera åtkomst till resurserna i Azure-prenumeration](../role-based-access-control/role-assignments-portal.md).
    * **Taggar**: Om du vill ange nyckel/värde-par för att definiera en anpassad taxonomi av dina molntjänster. Du kan till exempel skapa en nyckel som heter **projekt**, och sedan använda ett värde som är gemensamma för alla tjänster som är associerad med ett visst projekt.
    * **Diagnostisera och lösa problem**: visa felsökningsinformation.
    * **Låser**: lägga till ett lås för att förhindra att klustret som ändras eller tas bort.
    * **Automatiseringsskriptet**: visa och exportera Azure Resource Manager-mall för klustret. Du kan för närvarande bara exportera beroende Azure storage-konto. Se [skapa Linux-baserade Hadoop-kluster i HDInsight med hjälp av Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Snabbstart**: Visar information som hjälper dig att komma igång med HDInsight.
    * **Verktyg för HDInsight**: hjälpinformation för HDInsight relaterade verktyg.
    * **Prenumerationen Core användning**: Visa Använd och tillgänglig kärnor för prenumerationen.
    * **Skala klustret**: öka och minska antalet arbetarnoder i klustret. Se[skala kluster](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **SSH + klusterinloggning**: Visar instruktionerna för att ansluta till klustret med SSH (Secure Shell)-anslutning. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).
    * **HDInsight-partnern**: Lägg till/ta bort aktuell HDInsight-Partner.
    * **Externa Metastores**: Visa Hive och Oozie metastores. Metastores kan bara konfigureras när klustret skapas. Se [använda Hive/Oozie metastore](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Script åtgärder**: köra Bash-skript på klustret. Se [anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).
    * **Program**: Lägg till/ta bort HDInsight-program.  Se [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).
    * **Övervaka**: övervaka kluster i Azure logganalys.
    * **Egenskaper för**: Visa egenskaper för klustret.
    * **Storage-konton**: Visa storage-konton och nycklarna. Storage-konton har konfigurerats när klustret skapas.
    * **Data Lake Store-åtkomst**: Konfigurera åtkomst som lagrar Data Lake.  Se [skapa HDInsight-kluster med Data Lake Store med hjälp av Azure portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
    * **Resurshälsa**: se [Azure resource health översikt](../service-health/resource-health-overview.md).
    * **Ny supportbegäran**: du kan skapa ett supportärende Microsoft support.
    
6. Klicka på **egenskaper**:

    Egenskaperna är:

   * **Hostname**: klustrets namn.
   * **Kluster-URL: en**: en för Ambari-webbgränssnittet.
   * **SSH (Secure shell)**: namnet på användarnamnet och värden ska användas för åtkomst till klustret via SSH.
   * **Status för**: en av: avbröts, godkända ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, fungerar, kör, fel, ta bort, tas bort, för lång tid, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued eller ClusterCustomization.
   * **Region**: Azure-plats. En lista över Azure platser som stöds finns i **Region** nedrullningsbar listruta i [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Skapandedatum**: det datum som klustret har distribuerats.
   * **Operativsystemet**: antingen **Windows** eller **Linux**.
   * **Typen**: Hadoop, HBase, Storm, Väck.
   * **Version**. Se [HDInsight-versioner](hdinsight-component-versioning.md).
   * **Prenumerationen**: prenumerationsnamn.
   * **Standarddatakälla**: standardfilsystem för klustret.
   * **Worker noder storlek**: valda VM-storlek för arbetsnoderna.
   * **Gå nodstorlek**: valda VM-storlek för huvudnoderna.
   * **Virtuellt nätverk**: namnet på det virtuella nätverk och undernät som klustret distribueras, om en har valts vid tidpunkten för distribution.

## <a name="delete-clusters"></a>Ta bort kluster
Tar bort ett kluster tar inte bort standardkontot för lagring eller eventuella länkade lagringskonton. Du kan återskapa klustret med hjälp av samma lagringskonton och samma metastores. Vi rekommenderar att du använder en ny standardbehållaren när du skapar klustret igen.

1. Logga in på den [Portal][azure-portal].
2. Klicka på **HDInsight-kluster** i den vänstra menyn. Om du inte ser **HDInsight-kluster**, klickar du på **alla tjänster** första.
3. Klicka på det kluster som du vill ta bort.
4. Klicka på **ta bort** på den översta menyn och följ sedan instruktionerna.

Se även [paus/stängs av kluster](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Du kan lägga till ytterligare Azure Storage-konton och Azure Data Lake Store-konton när ett kluster skapas. Mer information finns i [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="scale-clusters"></a>Skala kluster
Klustret skalning funktionen kan du ändra antalet arbetarnoder som används av ett Azure HDInsight-kluster utan att behöva återskapa klustret.

> [!NOTE]
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av klustret kan kontrollera du egenskapssidan.  Se [listan och visa](#list-and-show-clusters).
>
>

Effekten av att ändra antalet datanoder som varierar för varje typ av kluster som stöds av HDInsight:

* Hadoop

    Sömlöst kan du öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller körs jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras korrekt så att klustret alltid kvar i ett fungerande tillstånd.

    När ett Hadoop-kluster skalas ned genom att minska antalet datanoder som, en del av tjänsterna i klustret har startats om. Detta medför alla körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.
* HBase

    Du kan sömlöst Lägg till eller ta bort noder till HBase-kluster, medan den körs. Regional servrar balanseras automatiskt inom några minuter för att slutföra åtgärden. skalning. Du kan också manuellt balansera regionala servrar genom att logga in på headnode i klustret och köra följande kommandon från en kommandotolk:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Mer information om hur du använder HBase-gränssnittet finns [komma igång med ett exempel Apache HBase i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    Du kan sömlöst lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Men efter en lyckad skalning åtgärden har slutförts behöver du ombalansera topologin.

    Ombalansering kan utföras på två sätt:

  * Storm webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)

    Referera till den [Apache Storm dokumentationen](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.

    Storm webbgränssnittet är tillgänglig på HDInsight-kluster:

    ![HDInsight Storm skala balansera](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Här är ett exempel CLI-kommando för att balansera Storm-topologi:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Att skala kluster**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **HDInsight-kluster** i den vänstra menyn.
3. Klicka på det kluster som du vill skala.
3. Klicka på **skala klustret**.
4. Ange **numret för arbetarnoder**. Gränsen för antalet klusternoder varierar mellan Azure-prenumerationer. Du kan kontakta faktureringssupporten om du vill öka gränsvärdet.  Information om kostnaden avspeglar ändringar i antalet noder.

    ![HDInsight hadoop hbase storm spark skala](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Pausa/stängs av kluster

De flesta Hadoop-jobb är batchjobb som endast körs ibland. För de flesta Hadoop-kluster finns stora tidsperioder som klustret inte används för bearbetning. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används.
Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Det finns många sätt kan du programmerar processen:

* Användaren Azure Data Factory. Se [skapa på begäran Linux-baserade Hadoop-kluster i HDInsight med hjälp av Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) länkade tjänster för att skapa HDInsight på begäran.
* Använda Azure PowerShell.  Se [analysera svarta fördröjning](hdinsight-analyze-flight-delay-data.md).
* Använda Azure CLI. Se [hantera HDInsight-kluster med hjälp av Azure CLI](hdinsight-administer-use-command-line.md).
* Använda HDInsight .NET SDK. Se [skicka Hadoop-jobb](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Prisinformation, se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/). Om du vill ta bort ett kluster från portalen finns [ta bort kluster](#delete-clusters)

## <a name="move-cluster"></a>Flytta kluster

Du kan flytta ett HDInsight-kluster till ett annat Azure resursgrupp eller en annan prenumeration.  Se [listan och visa](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Uppgradera kluster

Se [uppgradera HDInsight-kluster till en nyare version](./hdinsight-upgrade-cluster.md).

## <a name="open-the-ambari-web-ui"></a>Öppna Ambari-webbgränssnittet

Ambari innehåller en intuitiv är enkelt att använda Hadoop management webbgränssnittet backas upp av dess RESTful-API: er. Ambari gör det möjligt för systemadministratörer att hantera och övervaka Hadoop-kluster.

1. Öppna ett HDInsight-kluster från Azure-portalen.  Se [listan och visa](#list-and-show-clusters).
2. Klicka på **kluster instrumentpanelen**.

    ![HDInsight Hadoop-kluster-menyn](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Ange klustret användarnamn och lösenord.  Klustret Standardanvändarnamnet är _admin_. Ambari web UI ser ut:

    ![HDInsight Hadoop Ambari-Webbgränssnittet](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Mer information finns i [hantera HDInsight-kluster med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Ändra lösenord
Ett HDInsight-kluster kan ha två användarkonton. HDInsight-kluster användarkonto (kallas även HTTP-användarkontot) och SSH-användarkontot skapas under skapandeprocessen. Du kan använda Ambari-webbgränssnittet för att ändra klustret användarens användarnamn och lösenord och skriptåtgärder ändra SSH-användarkontot

### <a name="change-the-cluster-user-password"></a>Ändra användarlösenord kluster
Du kan använda Ambari-Webbgränssnittet för att ändra användarlösenord klustret. Du måste använda det befintliga klustret användarnamn och lösenord för att logga in till Ambari.

> [!NOTE]
> Ändra kluster-användarlösenord (admin) kan det orsaka skriptåtgärder körs mot det här klustret slutar fungera. Om du har några beständiga skriptåtgärder arbetsnoder som mål, misslyckas dessa skript när du lägger till noder i klustret via ändra storlek på åtgärder. Mer information om skriptåtgärder finns [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Logga in på Ambari-Webbgränssnittet användarens autentiseringsuppgifter för HDInsight-kluster. Standardanvändarnamnet är **admin**. URL-Adressen är **https://&lt;HDInsight-klustrets namn > azurehdinsight.net**.
2. Klicka på **Admin** på den översta menyn och klicka sedan på ”Hantera Ambari”.
3. I den vänstra menyn klickar du på **användare**.
4. Klicka på **Admin**.
5. Klicka på **ändra lösenord**.

Ambari och ändrar lösenordet på alla noder i klustret.

### <a name="change-the-ssh-user-password"></a>Ändra lösenord för SSH-användare
1. Med hjälp av en textredigerare, spara följande text som en fil med namnet **changepassword.sh**.

    > [!IMPORTANT]
    > Du måste använda ett redigeringsprogram som använder LF som rad avslutas. Om CRLF används, fungerar inte skriptet.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Överföra filen till en lagringsplats som kan nås från HDInsight med hjälp av en HTTP- eller HTTPS-adress. Till exempel lagra en gemensam fil, t.ex OneDrive eller Azure Blob storage. Spara URI: N (HTTP eller HTTPS-adress) i filen eftersom den här URI krävs i nästa steg.
3. Azure-portalen klickar du på **HDInsight-kluster**.
4. Klicka på ditt HDInsight-kluster.
4. Klicka på **skript åtgärder**.
4. Från den **skriptåtgärder** bladet väljer **skicka nya**. När den **skicka skriptåtgärden** bladet visas anger du följande information:

   | Fält | Värde |
   | --- | --- |
   | Namn |Ändra ssh lösenord |
   | Bash-skript-URI |URI: N till filen changepassword.sh |
   | Noder (Head, Worker, Nimbus, chef, Zookeeper osv.) |✓ för alla nodtyper som anges |
   | Parametrar |Ange SSH-användarnamn och det nya lösenordet. Det bör finnas ett blanksteg mellan användarnamnet och lösenordet. |
   | Spara den här skriptåtgärden... |Lämna det här fältet är avmarkerat. |
5. Välj **skapa** tillämpa skriptet. När skriptet har slutförts, är du kunna ansluta till klustret med SSH med det nya lösenordet.

## <a name="grantrevoke-access"></a>Bevilja/återkalla åtkomst
HDInsight-kluster har följande HTTP-webbtjänster (alla dessa tjänster har RESTful slutpunkter):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Som standard beviljas dessa tjänster för åtkomst. Du kan återkalla/bevilja åtkomst med hjälp av [Azure CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) och [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Hitta prenumerations-ID

**Hitta din Azure-prenumeration ID: N**

1. Logga in på den [Portal][azure-portal].
2. Klicka på **prenumerationer**. Varje prenumeration har ett namn och ett ID.

Varje kluster är kopplad till en Azure-prenumeration. Prenumerationen ID visas i klustret **väsentliga** panelen. Se [listan och visa](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Hitta resursgruppen.
I läget Azure Resource Manager skapas varje HDInsight-kluster med en Azure Resource Manager-grupp. Resource Manager-gruppen som tillhör ett kluster visas i:

* Listan över kluster har en **resursgruppen** kolumn.
* Klustret **väsentliga** panelen.  

Se [listan och visa](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Hitta storage-konton

HDInsight-kluster kan du använda ett Azure Storage-konto eller ett Azure Data Lake Store för att lagra data. Varje HDInsight-kluster kan ha en standardkontot för lagring och ett antal länkade lagringskonton. Om du vill visa en lista med lagringskonton, först öppna klustret från portalen och klicka sedan på **lagringskonton**:

![Storage-konton för HDInsight-kluster](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

I föregående skärmbild kan det finns en __standard__ kolumnen som anger om kontot är standardkontot för lagring.

Om du vill visa en lista med Data Lake Store-konton, klickar du på **Data Lake Store-åtkomst** i föregående skärmbild.

## <a name="run-hive-queries"></a>Köra Hive-frågor
Du kan inte köra Hive-jobb direkt från Azure-portalen, men du kan använda Hive-vy på Ambari-Webbgränssnittet.

**Köra Hive-frågor med Ambari Hive-vy**

1. Logga in på Ambari-Webbgränssnittet användarens autentiseringsuppgifter för HDInsight-kluster. Standardanvändarnamnet är **admin**. URL-Adressen är **https://&lt;HDInsight-klustrets namn > azurehdinsight.net**.
2. Öppna Hive-vy som visas i följande skärmbild:  

    ![HDInsight hive-vyn](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Klicka på **frågan** på den översta menyn.
4. Ange en Hive-fråga i **frågeredigeraren**, och klicka sedan på **kör**.

## <a name="monitor-jobs"></a>Övervaka jobb
Se [hantera HDInsight-kluster med Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Bläddra bland filer
Du kan bläddra innehållet i standardbehållaren med Azure-portalen.

1. Logga in på [ https://portal.azure.com ](https://portal.azure.com).
2. Klicka på **HDInsight-kluster** i den vänstra menyn för att visa befintliga kluster.
3. Klicka på klusternamnet. Om klustret är långt, kan du använda filter överst på sidan.
4. Klicka på **Lagringskonton** i den vänstra menyn i klustret.
5. Klicka på ett lagringskonto.
7. Klicka på den **Blobbar** panelen.
8. Klicka på standardnamnet för behållaren.

## <a name="monitor-cluster-usage"></a>Övervaka kluster användning
Den **användning** på HDInsight-klusterbladet visar information om antal kärnor som är tillgängliga för din prenumeration för användning med HDInsight, samt antal kärnor som allokerats till detta kluster och hur de är allokerade för alla noder i klustret. Se [listan och visa](#list-and-show-clusters).

> [!IMPORTANT]
> För att övervaka tjänster som tillhandahålls av HDInsight-kluster, måste du använda Ambari Web eller Ambari REST API. Mer information om hur du använder Ambari finns [hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Ansluta till ett kluster

* [Använda Hive med HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig vissa grundläggande administrativa funktioner. Mer information finns i följande artiklar:

* [Administrera HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrera HDInsight med hjälp av Azure CLI](hdinsight-administer-use-command-line.md)
* [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Läs mer om hur du använder Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Information om hur du använder Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Använda Hive i HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Pig i HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda Sqoop i HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Komma igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Vilken version av Hadoop finns i Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop-kommandorad"
