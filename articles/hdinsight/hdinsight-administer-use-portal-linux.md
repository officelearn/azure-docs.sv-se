---
title: Hantera Apache Hadoop-kluster i HDInsight med Azure-portal
description: Lär dig hur du skapar och hanterar Azure HDInsight-kluster med Azure-portalen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/12/2020
ms.openlocfilehash: b9d923b3272f9d8b3da39d7cdb771a766eee4eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272739"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Med Hjälp av [Azure-portalen](https://portal.azure.com)kan du hantera [Apache Hadoop-kluster](https://hadoop.apache.org/) i Azure HDInsight. Använd tablektorn ovan för information om hur du hanterar Hadoop-kluster i HDInsight med andra verktyg.

## <a name="prerequisites"></a>Krav

Ett befintligt Apache Hadoop-kluster i HDInsight.  Se [Skapa Linux-baserade kluster i HDInsight med Hjälp av Azure-portalen](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Komma igång

Logga in [https://portal.azure.com](https://portal.azure.com)på .

## <a name="list-and-show-clusters"></a><a name="showClusters"></a>Lista och visa kluster

**HdInsight-klustersidan listar** dina befintliga kluster.  Från portalen:
1. Välj **Alla tjänster** på den vänstra menyn.
2. Välj **HDInsight-kluster** under **ANALYTICS**.

## <a name="cluster-home-page"></a><a name="homePage"></a>Startsida för kluster

Välj ditt klusternamn på sidan [**HDInsight-kluster.**](#showClusters)  Då öppnas **översiktsvyn,** som liknar följande bild:

![Grundläggande om Azure Portal HDInsight-kluster](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Övre meny:**  

| Objekt| Beskrivning |
|---|---|
|Flytta|Flyttar klustret till en annan resursgrupp eller till en annan prenumeration.|
|Ta bort|Tar bort klustret. |
|Uppdatera|Uppdaterar vyn.|

**Vänster meny:**  

  - **Menyn överst till vänster**

    | Objekt| Beskrivning |
    |---|---|
    |Översikt|Innehåller allmän information för klustret.|
    |Aktivitetslogg|Visa och fråga aktivitetsloggar.|
    |Åtkomstkontroll (IAM)|Använd rolltilldelningar.  Se [Använda rolltilldelningar för att hantera åtkomst till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md).|
    |Taggar|Gör att du kan ställa in nyckel-/värdepar för att definiera en anpassad taxonomi för dina molntjänster. Du kan till exempel skapa en nyckel med namnet **projekt**och sedan använda ett gemensamt värde för alla tjänster som är associerade med ett visst projekt.|
    |Diagnostisera och lösa problem|Visa felsökningsinformation.|
    |Snabbstart|Visar information som hjälper dig att komma igång med HDInsight.|
    |Verktyg|Hjälpinformation för HDInsight-relaterade verktyg.|

  - **Menyn Inställningar**  

    | Objekt| Beskrivning |
    |---|---|
    |Klusterstorlek|Kontrollera, öka och minska antalet klusterarbetarenoder. Se [Skala kluster](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Kvotgränser|Visa de använda och tillgängliga kärnorna för din prenumeration.|
    |SSH + Klusterinloggning|Visar instruktionerna för att ansluta till klustret med secure shell-anslutning (SSH). Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|Konfigurera åtkomst till datalagringgengen1.  Se [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Lagringskonton|Visa lagringskontona och nycklarna. Lagringskontona konfigureras under processen för att skapa kluster.|
    |Program|Lägg till/ta bort HDInsight-program.  Se [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).|
    |Skriptåtgärder|Kör Bash-skript i klustret. Se [Anpassa Linux-baserade HDInsight-kluster med hjälp av skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).|
    |Externa metabutiker|Visa [Apache Hive](https://hive.apache.org/) och [Apache Oozie](https://oozie.apache.org/) metabutiker. Metabutikerna kan bara konfigureras under processen för att skapa kluster.|
    |HDInsight-partner|Lägg till/ta bort den aktuella HDInsight-partnern.|
    |Egenskaper|Visa [klusteregenskaperna](#properties).|
    |Lås|Lägg till ett lås för att förhindra att klustret ändras eller tas bort.|
    |Exportera mall|Visa och exportera Azure Resource Manager-mallen för klustret. För närvarande kan du bara exportera det beroende Azure-lagringskontot. Se [Skapa Linux-baserade Apache Hadoop-kluster i HDInsight med Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menyn Övervakning**

    | Objekt| Beskrivning |
    |---|---|
    |Aviseringar|Hantera aviseringar och åtgärder.|
    |Mått|Övervaka klustermåtten i Azure Monitor-loggar.|
    |Inställningar för diagnos|Inställningar för var diagnosmåtten ska lagras.|
    |Azure Monitor|Övervaka ditt kluster i Azure Monitor.|

  - **Support + felsökningsmeny**

    | Objekt| Beskrivning |
    |---|---|
    |Resurshälsa|Se [Översikt över Azure-resurshälsa](../service-health/resource-health-overview.md).|
    |Ny supportbegäran|Gör att du kan skapa en supportbiljett med Microsoft-support.|

## <a name="cluster-properties"></a><a name="properties"></a>Klusteregenskaper

Välj **Egenskaper**under **Inställningar** på startsidan för [klustret](#homePage).

|Objekt | Beskrivning |
|---|---|
|Värdnamn|Klusternamn.|
|KLUSTER-URL|Url:en för Ambari-webbgränssnittet.|
|Privat slutpunkt|Den privata slutpunkten för klustret.|
|Säkert skal (SSH)|Användarnamnet och värdnamnet som ska användas för att komma åt klustret via SSH.|
|STATUS|En av: Avbruten, accepterad, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Delete, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, Ändra storlek PåQueued eller ClusterCustomization.|
|REGION|Azure-plats. En lista över Azure-platser som stöds finns i listrutan **Region** för [HDInsight-priser](https://azure.microsoft.com/pricing/details/hdinsight/).|
|SKAPAD DEN|Det datum då klustret distribuerades.|
|Operativsystem|Antingen **Windows** eller **Linux**.|
|TYPE|Hadoop, HBase, Storm, Spark.|
|Version|Se [HDInsight-versioner](hdinsight-component-versioning.md).|
|Minsta TLS-version|TLS-versionen.|
|PRENUMERATION|Prenumerationsnamn.|
|STANDARDDATAKÄLLA|Standardklusterfilsystemet.|
|Storlekar på arbetsnoder|Den valda VM-storleken på arbetarnoderna.|
|Huvudnodens storlek|Den valda VM-storleken på huvudnoderna.|
|Virtuellt nätverk|Namnet på det virtuella nätverket, som klustret distribueras, om ett har valts vid distributionen.|

## <a name="move-clusters"></a>Flytta kluster

Du kan flytta ett HDInsight-kluster till en annan Azure-resursgrupp eller en annan prenumeration.

Från [klustrets startsida:](#homePage)

1. Välj **Flytta** från den övre menyn.
2. Välj **Flytta till en annan resursgrupp** eller Flytta till en annan **prenumeration**.
3. Följ instruktionerna från den nya sidan.

## <a name="delete-clusters"></a>Ta bort kluster

Om du tar bort ett kluster tas inte standardlagringskontot bort eller några länkade lagringskonton. Du kan återskapa klustret med samma lagringskonton och samma metabutiker. Vi rekommenderar att du använder en ny standard blob-behållare när du återskapar klustret.

Från [klustrets startsida:](#homePage)

1. Välj **Ta bort** på den övre menyn.
2. Följ instruktionerna från den nya sidan.

Se även [Paus/stänga av kluster](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Du kan lägga till ytterligare Azure Storage-konton och Azure Data Lake Storage-konton när ett kluster har skapats. Mer information finns i [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="scale-clusters"></a>Skala kluster

Med klusterskalningsfunktionen kan du ändra antalet arbetsnoder som används av ett Azure HDInsight-kluster, utan att behöva återskapa klustret.

Se [Skala HDInsight-kluster](./hdinsight-scaling-best-practices.md) för fullständig information.

## <a name="pauseshut-down-clusters"></a>Pausa/stänga av kluster

De flesta Hadoop-jobb är batchjobb som bara körs ibland. För de flesta Hadoop-kluster finns det stora tidsperioder som klustret inte används för bearbetning. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används.
Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Det finns många sätt du kan programmera processen:

- Azure Data Factory för användare. Se [Skapa Linux-baserade Apache Hadoop-kluster på begäran i HDInsight med Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) för att skapa HDInsight-länkade tjänster på begäran.
- Använd Azure PowerShell.  Se [Analysera flygfördröjningsdata](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Använd Azures CLI. Se [Hantera Azure HDInsight-kluster med Azure CLI](hdinsight-administer-use-command-line.md).
- Använd HDInsight .NET SDK. Se [Skicka Apache Hadoop jobb](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Prisinformation finns i [HDInsight-priser](https://azure.microsoft.com/pricing/details/hdinsight/). Så här tar du bort ett kluster från portalen finns i [Ta bort kluster](#delete-clusters)

## <a name="upgrade-clusters"></a>Uppgradera kluster

Se [Uppgradera HDInsight-kluster till en nyare version](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Öppna webbgränssnittet apache Ambari

Ambari ger en intuitiv, lätt att använda Hadoop management webbgränssnitt backas upp av dess RESTful API: er. Ambari gör det möjligt för systemadministratörer att hantera och övervaka Hadoop-kluster.

Från [klustrets startsida:](#homePage)

1. Välj **klusterinstrumentpaneler**.

    ![Menyn HDInsight Apache Hadoop-kluster](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Välj **Ambari hem** från den nya sidan.
1. Ange klustrets användarnamn och lösenord.  Standardklusteranvändarnamnet är _admin_.

Mer information finns i [Hantera HDInsight-kluster med hjälp av Webbgränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Ändra lösenord

Ett HDInsight-kluster kan ha två användarkonton. Användarkontot för HDInsight-kluster (HTTP-användarkonto) och SSH-användarkontot skapas under skapandeprocessen. Du kan använda portalen för att ändra lösenordet för klustrets användarkonto och skriptåtgärder för att ändra SSH-användarkontot.

### <a name="change-the-cluster-user-password"></a>Ändra lösenordet för klusteranvändare

> [!NOTE]  
> Om du ändrar lösenordet för klusteranvändare (admin) kan skriptåtgärder som körs mot det här klustret misslyckas. Om du har några beständiga skriptåtgärder som riktar sig till arbetsnoder kan dessa skript misslyckas när du lägger till noder i klustret genom att ändra storlek på åtgärder. Mer information om skriptåtgärder finns i [Anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

Från [klustrets startsida:](#homePage)
1. Välj **SSH + Klusterinloggning** under **Inställningar**.
2. Välj **Återställ autentiseringsuppgifter**.
3. Ange och bekräfta nytt lösenord i textrutorna.
4. Välj **OK**.

Lösenordet ändras på alla noder i klustret.

### <a name="change-the-ssh-user-password"></a>Ändra SSH-användarlösenordet

1. Spara följande text med hjälp av en textredigerare som en fil med **namnet changepassword.sh**.

    > [!IMPORTANT]  
    > Du måste använda en redigerare som använder LF som radslut. Om redigeraren använder CRLF fungerar inte skriptet.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Ladda upp filen till en lagringsplats som kan nås från HDInsight med hjälp av en HTTP- eller HTTPS-adress. Till exempel ett offentligt filarkiv som OneDrive eller Azure Blob storage. Spara URI-adressen (HTTP- eller HTTPS-adressen) i filen, eftersom den här URI:n behövs i nästa steg.
3. På [startsidan för klustret](#homePage)väljer du **Skriptåtgärder** under **Inställningar**.
4. På sidan **Skriptåtgärder** väljer du **Skicka ny**.
5. På sidan **Skicka skriptåtgärd** anger du följande information:

   | Field | Värde |
   | --- | --- |
   | Skripttyp | Välj **- Anpassad** i listrutan.|
   | Namn |"Ändra ssh-lösenord" |
   | Bash skript URI |Uri till changepassword.sh-filen |
   | Nodtyper: (Chef, Arbetare, Nimbus, Övervakare eller Zookeeper.) |✓ för alla nodtyper som anges |
   | Parametrar |Ange SSH-användarnamnet och sedan det nya lösenordet. Det bör finnas ett mellanslag mellan användarnamnet och lösenordet. |
   | Kvarstår detta skript åtgärd ... |Lämna det här fältet omarkerat. |

6. Välj **Skapa** om du vill använda skriptet. När skriptet är klart kan du ansluta till klustret med SSH med det nya lösenordet.

## <a name="find-the-subscription-id"></a>Hitta prenumerations-ID:t

Varje kluster är bundet till en Azure-prenumeration.  Azure-prenumerations-ID visas från [klustrets startsida](#homePage).

## <a name="find-the-resource-group"></a>Hitta resursgruppen

I Azure Resource Manager-läget skapas varje HDInsight-kluster med en Azure Resource Manager-grupp. Resurshanteraren visas från [klustrets startsida](#homePage).

## <a name="find-the-storage-accounts"></a>Hitta lagringskontona

HDInsight-kluster använder antingen ett Azure Storage-konto eller Azure Data Lake Storage för att lagra data. Varje HDInsight-kluster kan ha ett standardlagringskonto och ett antal länkade lagringskonton. Om du vill visa lagringskontona väljer du **Lagringskonton**på startsidan för [klustret](#homePage) under **Inställningar**.

## <a name="monitor-jobs"></a>Övervaka jobb

Se [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Klusterstorlek

Panelen **Klusterstorlek** från [klustrets startsida](#homePage) visar antalet kärnor som allokerats till det här klustret och hur de allokeras för noderna i det här klustret.

> [!IMPORTANT]  
> Om du vill övervaka de tjänster som tillhandahålls av HDInsight-klustret måste du använda Ambari Web eller Ambari REST API. Mer information om hur du använder Ambari finns i [Hantera HDInsight-kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Anslut till ett kluster

- [Använda Apache Hive med HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig några grundläggande administrativa funktioner. Mer information finns i följande artiklar:

- [Administrera HDInsight med Azure PowerShell](hdinsight-administer-use-powershell.md)
- [Administrera HDInsight med Azure CLI](hdinsight-administer-use-command-line.md)
- [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
- [Information om hur du använder Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Använd Apache Hive i HDInsight](hadoop/hdinsight-use-hive.md)
- [Använd Apache Sqoop i HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Använd Python User Defined Functions (UDF) med Apache Hive och Apache Pig i HDInsight](hadoop/python-udf-hdinsight.md)
- [Vilken version av Apache Hadoop finns i Azure HDInsight?](hdinsight-component-versioning.md)
