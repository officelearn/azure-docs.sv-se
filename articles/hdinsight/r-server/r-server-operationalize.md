---
title: Operationalisera ML-tjänster på HDInsight - Azure
description: Lär dig hur du operationaliserar ML-tjänster i Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 0a275405530c60c5e2f08a2af120382f81c2b6f8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041379"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operationalisera ML-tjänster på Azure HDInsight-kluster

När du har använt ML-Services-kluster i HDInsight för att slutföra dina datamodeller, kan du driftsätta modellen för att göra förutsägelser. Den här artikeln innehåller instruktioner om hur du utför den här uppgiften.

## <a name="prerequisites"></a>Förutsättningar

* **En ML-Services-kluster i HDInsight**: anvisningar finns i [Kom igång med ML-tjänster på HDInsight](r-server-get-started.md).

* **En SSH-klient (Secure Shell)**: En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operationalisera ML-Services-kluster med enda-konfiguration

> [!NOTE]
> Stegen nedan gäller för R Server 9.0 och ML Server 9.1. ML Server 9.3 finns [använder administrationsverktyget för att hantera konfigurationen för driftsättning](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. SSH till kantnoden.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Anvisningar för hur du använder SSH med Azure HDInsight finns i [använda SSH med HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Byter du katalog till rätt version och sudo punkt net DLL-filen: 

    - Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R-server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Visas alternativ att välja mellan. Välj det första alternativet, som visas i följande skärmbild till **konfigurera ML Server för driftsättning**.

    ![en enda driftsättning](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Visas nu möjlighet att välja hur du vill utföra åtgärder för ML Server. Välj den första mallen genom att ange de presenterade alternativen **A**.

    ![en enda driftsättning](./media/r-server-operationalize/admin-util-one-box-2.png)

1. När du uppmanas, ange och ange lösenordet för en lokal administratörsanvändare.

1. Du bör se utdata som tyder på att åtgärden lyckades. Du uppmanas också att välja ett annat alternativ på menyn. Välj E att gå tillbaka till huvudmenyn.

    ![en enda driftsättning](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Du kan också utföra diagnostiska kontroller genom att köra diagnostiska test på följande sätt:

    a. På huvudmenyn väljer **6** att köra diagnostiktest.

    ![en enda driftsättning](./media/r-server-operationalize/diagnostic-1.png)

    b. På menyn diagnostiktest väljer **A**. När du uppmanas, anger du det lösenord som du angav för lokal administratörsanvändare.

    ![en enda driftsättning](./media/r-server-operationalize/diagnostic-2.png)

    c. Kontrollera att utdata visar att övergripande hälsa är ett steg.

    ![en enda driftsättning](./media/r-server-operationalize/diagnostic-3.png)

    d. I menyalternativen visas, ange **E** återgå till huvudmenyn och sedan ange **8** att avsluta admin-verktyget.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Långa fördröjningar när webbtjänster på Spark

Om du får långa fördröjningar när du försöker använda en webbtjänst som skapats med mrsdeploy-funktioner i en Spark-beräkningskontext kan du behöva lägga till vissa mappar som saknas. Spark-programmet tillhör en användare som kallas '*rserve2*' när den anropas från en webbtjänst med hjälp av mrsdeploy-funktioner. Så här kan du lösa problemet:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


I det här skedet är konfigurationen för driftsättning klar. Nu kan du använda den `mrsdeploy` paketet på din RClient för att ansluta till driftsättningen på gränsnoden och börja använda funktioner som [fjärrkörning](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) och [webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Beroende på om klustret är konfigurerat i ett virtuellt nätverk eller inte kan du behöva konfigurera portvidarebefordran via SSH-inloggning. I följande avsnitt beskrivs hur du konfigurerar den här tunneln.

### <a name="ml-services-cluster-on-virtual-network"></a>ML-Services-kluster i virtuellt nätverk

Se till att du tillåter trafik genom port 12800 till kantnoden. På så sätt kan du använda kantnoden för att ansluta till driftsättningsfunktionen.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Om `remoteLogin()` inte kan ansluta till kantnoden, men SSH till kantnod fungerar, kontrollerar du att regeln för att tillåta trafik via port 12800 har ställts in på rätt sätt. Om problemet kvarstår kan du kringgå det genom att ställa in portvidarebefordran via SSH. Anvisningar finns i följande avsnitt:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>ML-tjänster klustret inte konfigurerad på virtuellt nätverk

Om inget kluster har konfigurerats på vnet, eller om du har problem med anslutningen via vnet, kan du använda SSH-portvidarebefordran:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

När din SSH-session är aktiv vidarebefordras trafiken från den lokala datorns port 12800 till gränsnodens port 12800 via SSH-session. Se till att du använder `127.0.0.1:12800` i metoden `remoteLogin()`. Detta loggar in på gränsnodens driftsättning via portvidarebefordran.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skala operationaliserat beräkningsnoder på HDInsight-arbetsnoder

Om du vill skala beräkningsnoderna du först inaktivera arbetsnoder och sedan konfigurera beräkningsnoder på de inaktiverade arbetarnoderna.

### <a name="step-1-decommission-the-worker-nodes"></a>Steg 1: Inaktivera arbetsnoder

ML-tjänster kluster hanteras inte via YARN. Om arbetarnoderna inte är inaktiverade fungerar inte YARN-resurshanteraren som förväntat eftersom den inte känner till resurserna som förbrukas av servern. För att undvika detta rekommenderar vi att du inaktiverar arbetsnoderna innan du skalar ut beräkningsnoderna.

Så här inaktiverar du arbetsnoder:

1. Logga in på klustrets Ambari-konsol och klicka på **värdar** fliken.

1. Markera arbetsnoder (som tas ur drift).

1. Klicka på **åtgärder** > **valda värdar** > **värdar** > **aktivera underhållsläget**. I bilden nedan har vi till exempel valt att inaktivera wn3 och wn4.  

   ![inaktivera arbetsnoder](./media/r-server-operationalize/get-started-operationalization.png)  

* Välj **åtgärder** > **valda värdar** > **DataNodes** > klickar du på **inaktivera**.
* Välj **åtgärder** > **valda värdar** > **NodeManagers** > klickar du på **inaktivera**.
* Välj **åtgärder** > **valda värdar** > **DataNodes** > klickar du på **stoppa**.
* Välj **åtgärder** > **valda värdar** > **NodeManagers** > Klicka på **stoppa**.
* Välj **åtgärder** > **valda värdar** > **värdar** > klickar du på **stoppa alla komponenter**.
* Avmarkera arbetsnoderna och markera huvudnoderna.
* Välj **åtgärder** > **valda värdar** > ”**värdar** > **starta om alla komponenter**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Steg 2: Konfigurera beräkningsnoder på varje inaktiverad arbetsnod

1. SSH till varje inaktiverad arbetsnod.

1. Kör admin-verktyget med hjälp av relevanta DLL-filen för klustret ML-tjänster som du har. ML Server 9.1, kör du följande:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Ange **1** att välja alternativet **konfigurera ML Server för driftsättning**.

1. Ange **C** att välja alternativet `C. Compute node`. Då konfigureras beräkningsnoden på arbetsnoden.

1. Avsluta admin-verktyget.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Steg 3: Lägg till compute-noder information på webbnod

När alla inaktiverade arbetsnoder har konfigurerats för att köra beräkningsnoder återgår på gränsnoden och lägger till inaktiverade arbetsnoders IP-adresser i ML Server-webbnodens konfiguration:

1. SSH till kantnoden.

1. Kör `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Sök efter ”URI”-avsnittet och Lägg till arbetsnodens IP och portinformation.

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
