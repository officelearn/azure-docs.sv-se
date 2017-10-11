---
title: "Installera RStudio med R Server på HDInsight - Azure | Microsoft Docs"
description: "Så här installerar du RStudio med R Server på HDInsight."
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 416420d855505508735ebd8526e93efdb230ad53
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>Installera RStudio med R Server på HDInsight

Den här artikeln beskriver hur du installerar community (kostnadsfritt) version av [RStudio Server](https://www.rstudio.com/products/rstudio-server/) på edge-nod i ett kluster med ett anpassat skript. RStudio Server innehåller en webbläsarbaserad IDE för användning av fjärranslutna klienter och som används ofta i Linux. Det finns flera integrerad utvecklingsmiljöer (IDEs) för R idag, inklusive:

- Microsofts [R Tools för Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) 
- [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 
- Walware har Eclipse-baserade [StatET](http://www.walware.de/goto/statet).

Fördelen med att installera RStudio på kantnoden för ett HDInsight-kluster är att det ger en fullständig IDE-miljö för utveckling och körning av R-skript med R Server i klustret. Den här konfigurationen kan vara betydligt mer effektiva än standardinställningen för användning av R-konsolen.

> [!NOTE]
> Det förfarande som beskrivs i den här artikeln gäller endast om du inte valde för att installera RStudio Server community edition vid etablering av klustret. Om du lade till den under etableringen och att komma åt det. Klicka på den **R Server instrumentpaneler** panelen i Azure portal posten för klustret, sedan på den **R Studio Server** panelen. 

Om du föredrar att använda kommersiellt licensierad Pro-version av RStudio Server, måste du följa installationsanvisningarna från [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [!NOTE]
> Om du använder ett HDInsight-kluster som R har installerats med hjälp av den [installera R skriptåtgärd](hdinsight-hadoop-r-scripts-linux.md), stegen i det här dokumentet fungerar inte korrekt eftersom de kräver en R Server på HDInsight-kluster.
>
> 

## <a name="prerequisites"></a>Krav

* Ett Azure HDInsight-kluster med R Server installerat. Instruktioner finns i [komma igång med R Server på HDInsight-kluster](hdinsight-hadoop-r-server-get-started.md).
* En SSH-klient. För Linux och Unix-distributioner eller Mac OS X i `ssh` kommandot ingår i operativsystemet. För Windows, rekommenderar vi [Cygwin](http://www.redhat.com/services/custom/cygwin/) med den [OpenSSH alternativet](https://www.youtube.com/watch?v=CwYSvvGaiWU), eller [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Installera RStudio på klustret med ett anpassat skript

Här är stegen:

1. Identifiera edge-nod i klustret. Följande är ett HDInsight-kluster med R Server namngivningskonvention huvudnod och kantnod.
   * Huvudnod`CLUSTERNAME-ssh.azurehdinsight.net`
   * Kantnod`CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. SSH i edge-nod i klustret med namngivningsmönstret i steg 1. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

3. När du är ansluten bli rotanvändare i klustret. I SSH-session använder du följande kommando:

        sudo su -

4. Hämta anpassade skript för att installera RStudio. Ange följande kommando:

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Ändra behörigheterna för anpassat skript-fil och kör skriptet. Använd följande kommandon:

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Om du har använt en SSH-lösenordet när du skapar ett HDInsight-kluster med R Server kan du hoppa över detta steg och fortsätta till nästa. Om du har använt en SSH-nyckel i stället för att skapa klustret, måste du ange ett lösenord för SSH-användare. Du behöver det här lösenordet när du ansluter till RStudio. Kör följande kommandon:

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. När du tillfrågas om **aktuella Kerberos lösenord**, tryck på **RETUR**.  Observera att du måste ersätta `USERNAME` med en SSH-användare för din HDInsight-kluster. Om ditt lösenord har angetts ska du se följande meddelande:

        passwd: password updated successfully

    Avsluta SSH-session.

8. Skapa en SSH-tunnel till klustret genom att mappa `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` på HDInsight-kluster till klientdatorn. Du måste skapa en SSH-tunnel innan du öppnar en ny webbläsarsession.

   * På en Linux-klient eller en Windows-klient med [Cygwin](http://www.redhat.com/services/custom/cygwin/), öppna en terminalsession och använder du följande kommando:

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       Ersätt **användarnamn** med en SSH-användare för din HDInsight-kluster och Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.
       Du kan också använda en SSH-nyckel i stället för ett lösenord genom att lägga till `-i id_rsa_key`.        
   * Om du använder en Windows-klienter med PuTTY sedan

     1. Öppna PuTTY och ange anslutningsinformationen.
     2. I den **kategori** till vänster om dialogrutan, expanderar **anslutning**, expandera **SSH**, och välj sedan **tunnlar**.
     3. Ange följande information på den **alternativ som styr SSH-port vidarebefordran** formulär:

        * **Källport** – Porten på den klient som du vill vidarebefordra. Till exempel **8787**.
        * **Mål** -målet måste mappas till den lokala datorn. Till exempel **localhost:8787**.

            ![Skapa en SSH-tunnel](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "skapa en SSH-tunnel")

     4. Klicka på **Lägg till** lägga till inställningarna och klicka sedan på **öppna** att öppna en SSH-anslutning.
     5. När du uppmanas logga in på servern för att upprätta en SSH-session och aktiverar tunneln.

9. Öppna en webbläsare och ange följande URL baserat på vilken port som du angav för tunneln:

        http://localhost:8787/ 

10. Du uppmanas att ange SSH-användarnamn och lösenord för att ansluta till klustret. Om du har använt en SSH-nyckel när du skapar klustret måste du ange lösenordet du skapade i steg 5.

    ![Ansluta till R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "skapa en SSH-tunnel")

11. Du kan köra ett testskript som körs R-baserade MapReduce och Spark jobb i klustret för att testa om RStudio installationen lyckades. Hämta testskriptet ska köras i RStudio gå tillbaka till konsolen SSH och ange följande kommandon:

    *    Om du har skapat ett Hadoop-kluster med R, Använd följande kommando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    Om du har skapat ett Spark-kluster med R, Använd följande kommando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. I RStudio ser du testskriptet som du hämtat. Dubbelklicka på filen för att öppna den, markerar du innehållet i filen och klicka sedan på **kör**. Du bör se utdata i den **konsolen** fönstret:

   ![Testa installationen](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "testa installationen")

Ett annat alternativ är att ange `source(testhdi.r)` eller `source(testhdi_spark.r)` att köra skriptet.

## <a name="see-also"></a>Se även

* [Compute-kontexten alternativ för R Server på HDInsight-kluster](hdinsight-hadoop-r-server-compute-contexts.md)
* [Alternativ för Azure Storage för R Server på HDInsight](hdinsight-hadoop-r-server-storage.md)

