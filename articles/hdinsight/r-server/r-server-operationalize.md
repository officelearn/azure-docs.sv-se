---
title: Operationalize ML-tjänster på HDInsight - Azure
description: Lär dig hur du kan operationalisera din datamodell för att göra förutsägelser med ML Services i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a05bcdef2b7456fbab852e9728c156e57f847f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71123569"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operationalize ML Services-kluster på Azure HDInsight

När du har använt ML Services-klustret i HDInsight för att slutföra datamodelleringen kan du operationalisera modellen för att göra förutsägelser. Den här artikeln innehåller instruktioner om hur du utför den här uppgiften.

## <a name="prerequisites"></a>Krav

* Ett ML Services-kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **ML Services** för **klustertyp**.

* En SSH-klient (Secure Shell): En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operationalize ML Services-kluster med en enda ruta

> [!NOTE]  
> Stegen nedan gäller för R Server 9.0 och ML Server 9.1. För ML Server 9.3 läser du [Använda administrationsverktyget för att hantera operationaliseringskonfigurationen](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. SSH till kantnoden.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Instruktioner om hur du använder SSH med Azure HDInsight finns i [Använda SSH med HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ändra katalogen för den relevanta versionen och sudo pricken netto dll: 

    - För Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R-server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Du presenteras med alternativ att välja mellan. Välj det första alternativet, som visas i följande skärmbild, för att **konfigurera ML Server för operationalisering**.

    ![R-server Administration verktyg välj](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Du får nu möjlighet att välja hur du vill operationalisera ML Server. Välj det första i de presenterade alternativen genom att ange **A**.

    ![R-server Administration verktyg operationalize](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Ange och ange lösenordet för en lokal administratör när du uppmanas att ange och ange lösenordet igen.

1. Du bör se utdata som tyder på att åtgärden lyckades. Du uppmanas också att välja ett annat alternativ på menyn. Välj E för att gå tillbaka till huvudmenyn.

    ![R-server Administration verktyget framgång](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Du kan också utföra diagnostiska kontroller genom att köra ett diagnostiskt test på följande sätt:

    a. Välj **6** i huvudmenyn för att köra diagnostiska tester.

    ![Diagnostik för R-serveradministration](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. Välj **A**på menyn Diagnostiska tester . När du uppmanas till det anger du lösenordet som du angav för den lokala administratörsanvändaren.

    ![R-server administration verktyg test](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Kontrollera att utdata visar att den allmänna hälsan är ett pass.

    ![R-server Administration verktygspass](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. Från de menyalternativ som visas anger du **E** för att återgå till huvudmenyn och anger sedan **8** för att avsluta admin-verktyget.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Långa förseningar när du konsumerar webbtjänst på Apache Spark

Om du stöter på långa fördröjningar när du försöker använda en webbtjänst som skapats med mrsdeploy-funktioner i en Apache Spark-beräkningskontext, kan du behöva lägga till några saknade mappar. Spark-programmet tillhör en användare som kallas '*rserve2*' när den anropas från en webbtjänst med hjälp av mrsdeploy-funktioner. Så här kan du lösa problemet:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


I det här skedet är konfigurationen för driftsättning klar. Nu kan du `mrsdeploy` använda paketet på din RClient för att ansluta till operationalization på kantnoden och börja använda dess funktioner som [fjärrkörning](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) och [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Beroende på om klustret är konfigurerat i ett virtuellt nätverk eller inte kan du behöva konfigurera portvidarebefordran via SSH-inloggning. I följande avsnitt beskrivs hur du konfigurerar den här tunneln.

### <a name="ml-services-cluster-on-virtual-network"></a>ML Services-kluster i virtuellt nätverk

Se till att du tillåter trafik genom port 12800 till kantnoden. På så sätt kan du använda kantnoden för att ansluta till driftsättningsfunktionen.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Om `remoteLogin()` inte kan ansluta till kantnoden, men SSH till kantnod fungerar, kontrollerar du att regeln för att tillåta trafik via port 12800 har ställts in på rätt sätt. Om problemet kvarstår kan du kringgå det genom att ställa in portvidarebefordran via SSH. Instruktioner finns i följande avsnitt:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>ML Services-kluster har inte konfigurerats i virtuellt nätverk

Om inget kluster har konfigurerats på vnet, eller om du har problem med anslutningen via vnet, kan du använda SSH-portvidarebefordran:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

När SSH-sessionen är aktiv vidarebefordras trafiken från den lokala datorns port 12800 till kantnodens port 12800 till SSH-sessionen. Se till att du använder `127.0.0.1:12800` i metoden `remoteLogin()`. Detta loggar in i kantnodens operationalisering genom port vidarebefordran.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skala fungerande beräkningsnoder på HDInsight-arbetsnoder

Om du vill skala beräkningsnoderna inaktiverar du först arbetarnoderna och konfigurerar sedan beräkningsnoder på de inaktiverade arbetsnoderna.

### <a name="step-1-decommission-the-worker-nodes"></a>Steg 1: Inaktivera arbetsnoderna

ML Services-klustret hanteras inte via [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Om arbetsnoderna inte inaktiveras fungerar inte YARN Resource Manager som förväntat eftersom den inte är medveten om de resurser som tas upp av servern. För att undvika detta rekommenderar vi att du inaktiverar arbetsnoderna innan du skalar ut beräkningsnoderna.

Så här inaktiverar du arbetsnoder:

1. Logga in på klustrets Ambari-konsol och klicka på **fliken Värdar.**

1. Välj arbetsnoder (som ska inaktiveras).

1. Klicka på **Åtgärder** > **som valda värdar** > **är värdar** > **för aktivera underhållsläge**. I bilden nedan har vi till exempel valt att inaktivera wn3 och wn4.  

   ![Apache Ambari aktivera underhållsläge](./media/r-server-operationalize/get-started-operationalization.png)  

* Välj **Åtgärder** > **Valda värdar** > **DataNodes** > klicka på **Inaktivera**.
* Välj **Åtgärder** > Som valts ut till**nodHanterar** **Selected Hosts** > > klicka på **Inaktivera**.
* Välj **Åtgärder** > **Markerade värdar** > **DataNodes** > klicka på **Stoppa**.
* Välj **Åtgärder** > **Valda värdar** > **NodeManagers** > klicka på **Stoppa**.
* Välj **Åtgärder** > **valda värdar** > **>** klicka på Stoppa **alla komponenter**.
* Avmarkera arbetsnoderna och markera huvudnoderna.
* Välj **åtgärder** > **som är valda värdar** > "**Värdar** > **starta om alla komponenter**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Steg 2: Konfigurera beräkningsnoder för varje inaktiverad arbetsnod(er)

1. SSH till varje inaktiverad arbetsnod.

1. Kör administratörsverktyget med den relevanta DLL-filen för ML Services-klustret som du har. För ML Server 9.1 kör du följande:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Ange **1** för att välja alternativet **Konfigurera ML-server för operationalisering**.

1. Ange **C** för `C. Compute node`att välja alternativ . Då konfigureras beräkningsnoden på arbetsnoden.

1. Avsluta admin-verktyget.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Steg 3: Lägg till information om beräkningsnoder på webbnod

När alla inaktiverade arbetsnoder har konfigurerats för att köra beräkningsnod, kom tillbaka på kantnoden och lägger till inaktiverade arbetsnoders IP-adresser i ML Server-webbnodens konfiguration:

1. SSH till kantnoden.

1. Kör `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Leta efter avsnittet "Uris" och lägg till arbetarnodens IP- och portinformation.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Nästa steg

* [Hantera ML-tjänstkluster i HDInsight](r-server-hdinsight-manage.md)
* [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
* [Alternativ för Azure Storage för ML-tjänstkluster i HDInsight](r-server-storage.md)
