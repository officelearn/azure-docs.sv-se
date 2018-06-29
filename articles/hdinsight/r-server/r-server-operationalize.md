---
title: Operationalisera ML-tjänster på HDInsight - Azure | Microsoft Docs
description: Lär dig mer om att operationalisera ML tjänster i Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: caefe30ff567a5e24e1f4c3a11309bd35e06190c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046147"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operationalisera ML Services kluster på Azure HDInsight

När du har använt ML Services kluster i HDInsight för att slutföra din datamodeller, kan du operationalisera modellen för att göra förutsägelser. Den här artikeln innehåller instruktioner om hur du utför den här uppgiften.

## <a name="prerequisites"></a>Förutsättningar

* **Ml – tjänster-kluster i HDInsight**: instruktioner finns i [komma igång med ML Services på HDInsight](r-server-get-started.md).

* **En SSH-klient (Secure Shell)**: En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operationalisera ML Services kluster med en ruta konfiguration

> [!NOTE]
> Stegen nedan gäller för R Server 9.0 och ML Server 9.1. ML Server 9.3 finns [Använd administrationsverktyget för att hantera konfigurationen operationalization](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. SSH till kantnoden.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Instruktioner om hur du använder SSH med Azure HDInsight finns [använda SSH med HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ändra katalogen för de relevanta och sudo punkt net DLL-filen: 

    - För Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R-server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Visas med alternativ att välja mellan. Välj det första alternativet, som visas i följande skärmbild till **ML konfigurerar Server för Operationalization**.

    ![en enda driftsättning](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Nu visas med möjlighet att välja hur du vill att operationalisera ML-Server. Alternativ för presenterades väljer du den första genom att ange **A**.

    ![en enda driftsättning](./media/r-server-operationalize/admin-util-one-box-2.png)

5. När du uppmanas, ange och ange lösenordet för en lokal administratör.

6. Du bör se utdata som tyder på att åtgärden lyckades. Du uppmanas också att välja ett annat alternativ på menyn. Välj E gå tillbaka till huvudmenyn.

    ![en enda driftsättning](./media/r-server-operationalize/admin-util-one-box-3.png)

7. Alternativt kan utföra du diagnostiska kontroller genom att köra en diagnostisk test på följande sätt:

    a. Välj på huvudmenyn **6** köra diagnostiska tester.

    ![en enda driftsättning](./media/r-server-operationalize/diagnostic-1.png)

    b. Välj menyn diagnostiktest **A**. När du uppmanas, anger du det lösenord som du angav för lokal administratör.

    ![en enda driftsättning](./media/r-server-operationalize/diagnostic-2.png)

    c. Kontrollera att utdata visar att övergripande hälsa är ett steg.

    ![en enda driftsättning](./media/r-server-operationalize/diagnostic-3.png)

    d. Från menyn alternativen, ange **E** återgå till huvudmenyn och sedan ange **8** avsluta verktyget för administration.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Lång fördröjning när förbrukar webbtjänst på Spark

Om du får långa fördröjningar när du försöker använda en webbtjänst som skapats med mrsdeploy-funktioner i en Spark-beräkningskontext kan du behöva lägga till vissa mappar som saknas. Spark-programmet tillhör en användare som kallas '*rserve2*' när den anropas från en webbtjänst med hjälp av mrsdeploy-funktioner. Så här kan du lösa problemet:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


I det här skedet är konfigurationen för driftsättning klar. Nu kan du använda den `mrsdeploy` paketet på din RClient att ansluta till operationalization på kantnod och börja använda dess funktioner som [fjärrkörning](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) och [-webbtjänster](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Beroende på om klustret är konfigurerat i ett virtuellt nätverk eller inte kan du behöva konfigurera portvidarebefordran via SSH-inloggning. I följande avsnitt beskrivs hur du konfigurerar den här tunneln.

### <a name="ml-services-cluster-on-virtual-network"></a>Ml – tjänster kluster på virtuellt nätverk

Se till att du tillåter trafik genom port 12800 till kantnoden. På så sätt kan du använda kantnoden för att ansluta till driftsättningsfunktionen.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Om `remoteLogin()` inte kan ansluta till kantnoden, men SSH till kantnod fungerar, kontrollerar du att regeln för att tillåta trafik via port 12800 har ställts in på rätt sätt. Om problemet kvarstår kan du kringgå det genom att ställa in portvidarebefordran via SSH. Anvisningar finns i följande avsnitt:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Ml – tjänster klustret inte ställts in på det virtuella nätverket

Om inget kluster har konfigurerats på vnet, eller om du har problem med anslutningen via vnet, kan du använda SSH-portvidarebefordran:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

När din SSH-session är aktiv vidarebefordras trafiken från port 12800 i datorn till port 12800 på kantnoden via SSH-sessionen. Se till att du använder `127.0.0.1:12800` i metoden `remoteLogin()`. Detta loggar in på den kantnod operationalization via vidarebefordrade portar.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skala operationalized compute-noder på HDInsight arbetsnoder

Om du vill skala datornoderna först inaktivera arbetsnoderna och konfigurera sedan compute-noder på inaktiverade arbetsnoderna.

### <a name="step-1-decommission-the-worker-nodes"></a>Steg 1: Inaktivera arbetsnoderna

Ml – tjänster klustret hanteras inte via YARN. Om arbetsnoderna inte är inaktiverade fungerar YARN Resource Manager inte som förväntat, eftersom den inte är medveten om de resurser som tas upp av servern. För att undvika detta rekommenderar vi att du inaktiverar arbetsnoderna innan du skalar ut beräkningsnoderna.

Följ dessa steg om du vill inaktivera arbetsnoderna:

1. Logga in på klustrets Ambari-konsolen och klicka på **värdar** fliken.

2. Välj arbetarnoder (för att vara inaktiverade).

3. Klicka på **åtgärder** > **valt värdar** > **värdar** > **aktivera underhållsläge**. I bilden nedan har vi till exempel valt att inaktivera wn3 och wn4.  

   ![inaktivera arbetsnoder](./media/r-server-operationalize/get-started-operationalization.png)  

* Välj **åtgärder** > **valt värdar** > **DataNodes** > klickar du på **Decommission**.
* Välj **åtgärder** > **valt värdar** > **NodeManagers** > klickar du på **Decommission**.
* Välj **åtgärder** > **valt värdar** > **DataNodes** > klickar du på **stoppa**.
* Välj **åtgärder** > **valt värdar** > **NodeManagers** > klickar du på **stoppa**.
* Välj **åtgärder** > **valt värdar** > **värdar** > klickar du på **stoppa alla komponenter**.
* Avmarkera arbetsnoderna och markera huvudnoderna.
* Välj **åtgärder** > **valt värdar** > ”**värdar** > **starta om alla komponenter**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Steg 2: Konfigurera compute-noder på varje inaktiverade worker-noder

1. SSH till varje inaktiverad arbetsnod.

2. Kör verktyget för administration med relevanta DLL-filen för klustret ML-tjänster som du har. För ML Server 9.1, kör du följande:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Ange **1** att välja alternativet **ML konfigurerar Server för Operationalization**.

4. Ange **C** att välja alternativet `C. Compute node`. Då konfigureras beräkningsnoden på arbetsnoden.

5. Avsluta admin-verktyget.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Steg 3: Lägg till compute-noder information på webben nod

När alla inaktiverade arbetarnoder har konfigurerats för att köra compute-nod, gå tillbaka på kantnoden och Lägg till inaktiverade worker noder IP-adresser i ML Server web nodens konfiguration:

1. SSH till kantnoden.

2. Kör `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

3. Sök efter avsnittet ”URI” och Lägg till worker noden IP och port information.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Nästa steg

* [Hantera tjänster ML-kluster i HDInsight](r-server-hdinsight-manage.md)
* [Compute-kontexten alternativ för ML Services kluster i HDInsight](r-server-compute-contexts.md)
* [Azure lagringsalternativ för ML Services-kluster i HDInsight](r-server-storage.md)
