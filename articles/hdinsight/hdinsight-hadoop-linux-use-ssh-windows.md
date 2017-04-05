---
title: "Använda SSH med HDInsight-kluster från PuTTY för Windows | Microsoft Docs"
description: "Lär dig hur du skapar och använder SSH-nycklar för att autentisera till Linux-baserade HDInsight-kluster från Windows-baserade klienter med hjälp av PuTTY SSH-klienten."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 639328ca-d800-4fa9-97ed-5664477b88cd
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: larryfr
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b1806950581e0adbeec52839f12c70599d28100d
ms.lasthandoff: 03/21/2017

---
# <a name="use-ssh-with-hdinsight-hadoop-from-putty-on-windows"></a>Använda SSH med HDInsight (Hadoop) från PuTTY för Windows

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) gör att du kan utföra åtgärder på Linux-baserade HDInsight-kluster med ett kommandoradsgränssnitt via fjärranslutning. Det här dokumentet innehåller information om hur du ansluter till HDInsight från Windows-baserade klienter med PuTTY SSH-klienten.

> [!NOTE]
> Stegen i den här artikeln förutsätter att du använder en Windows-baserad dator med PuTTY SSH-klienten. Om du använder en Linux-, Unix-, OS X- eller Windows-dator som tillhandahåller `ssh`-kommandot läser du [Använda SSH med Linux-baserat Hadoop i HDInsight från Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="prerequisites"></a>Krav

* **PuTTY** och **PuTTYGen** för Windows-baserade klienter. Dessa hjälpmedel är tillgängliga från [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
* En modern webbläsare som stöder HTML5.

## <a name="what-is-ssh"></a>Vad är SSH?

SSH är ett verktyg för att logga in till och från en annan dator fjärrstyra kommandon på en fjärrserver. Med Linux-baserat HDInsight etablerar SSH en krypterad anslutning till klustrets huvudnod och etablerar en kommandorad som du använder för att skriva in kommandon. Kommandona körs sedan direkt på servern.

Tidigare tillhandahölls ingen SSH-klient för Windows. PuTTY är en grafisk SSH-klient som kan installeras i Windows.

### <a name="ssh-user-name"></a>SSH-användarnamn

Ett SSH-användarnamn är det namn som du använder för att autentisera för HDInsight-klustret. Om du anger ett SSH-användarnamn när klustret skapas kommer den här användaren att skapas på alla noder i klustret. Du kan använda det här användarnamnet för att ansluta till HDInsight-klustrets huvudnoder när klustret har skapats. Från huvudnoderna kan du därefter ansluta till de enskilda arbetsnoderna.

### <a name="ssh-password-or-public-key"></a>SSH-lösenord eller offentlig nyckel

En SSH-användare kan använda ett lösenord eller en offentlig nyckel för autentisering. Ett lösenord är bara en textsträng som du hittar på medan en offentlig nyckel är en del av ett krypterat nyckelpar som genereras för unik identifiering av dig.

En nyckel är säkrare än ett lösenord, men det krävs ytterligare steg för att generera nyckeln och du måste lagra filerna som innehåller nyckeln på en säker plats. Om någon får tillgång till nyckelfilerna får de tillgång till ditt konto. Och om du förlorar nyckelfilerna kan du inte kan logga in på ditt konto.

Ett nyckelpar består av en offentlig nyckel (som skickas till HDInsight-servern) och en privat nyckel (som sparas på klientdatorn.) När du ansluter till HDInsight-servern med SSH använder SSH-klienten den privata nyckeln på datorn för att autentisera med servern.

## <a name="create-an-ssh-key"></a>Skapa en SSH-nyckel

Använd följande information om du tänker använda SSH-nycklar med ditt kluster. Om du tänker använda ett lösenord kan du hoppa över det här avsnittet.

1. Öppna PuTTYGen.

2. För **Typ av nyckel att generera** väljer du **SSH-2 RSA** och klickar sedan på **Generera**.
   
    ![PuTTYGen-gränssnitt](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Flytta runt musen i området nedanför förloppsindikatorn tills fältet fylls. Flyttningen av musen genererar slumpmässiga data som används för att generera nyckeln.
   
    ![flytta musen](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)
   
    När nyckeln har genererats visas den offentliga nyckeln.

4. För extra säkerhet kan du ange en lösenfras i fältet för **nyckellösenfras** fältet och sedan skriva in samma värde i fältet **Bekräfta lösenfras**.
   
    ![lösenfras](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)
   
   > [!NOTE]
   > Vi rekommenderar starkt att du använder en säker lösenfras för nyckeln. Men om du glömmer bort lösenfrasen finns inget sätt att återställa den.

5. Klicka på **Spara privat nyckel** för att spara nyckeln i en **.ppk**-fil. Den här nyckeln används för autentisering för ditt Linux-baserade HDInsight-kluster.
   
   > [!NOTE]
   > Du bör lagra den här nyckeln på en säker plats då den kan användas för att få åtkomst till ditt Linux-baserade HDInsight-kluster.

6. Klicka på **Spara offentlig nyckel** för att spara nyckeln som en **.txt**-fil. På så sätt kan du återanvända den offentliga nyckeln i framtiden när du skapar ytterligare Linux-baserade HDInsight-kluster.
   
   > [!NOTE]
   > Den offentliga nyckeln visas också överst i PuTTYGen. Du kan högerklicka på det här fältet, kopiera värdet och sedan klistra in det i ett formulär när du skapar ett kluster med Azure-portalen.

## <a name="create-a-linux-based-hdinsight-cluster"></a>Skapa ett Linux-baserat HDInsight-kluster

När du skapar ett Linux-baserat HDInsight-kluster måste du ange den offentliga nyckel som har skapats tidigare. Det finns två sätt att skapa ett Linux-baserat HDInsight-kluster från Windows-baserade klienter:

* **Azure Portal** – Använder en webbaserad portal för att skapa klustret.

* **Azure CLI för Mac, Linux och Windows** – Använder kommandon på kommandoraden för att skapa klustret.

Var och en av dessa metoder kräver den offentliga nyckeln. Fullständig information om hur du skapar ett Linux-baserat HDInsight-kluster finns i [Etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="azure-portal"></a>Azure Portal

När du använder [Azure Portal][preview-portal] för att skapa ett Linux-baserat HDInsight-kluster måste du ange ett **SSH-användarnamn** och välja att ange ett **LÖSENORD** eller en **OFFENTLIG SSH-NYCKEL**.

Om du väljer **SSH OFFENTLIG NYCKEL** kan du antingen klistra in den offentliga nyckeln (visas i den **offentliga nyckeln för att klistra in i det OpenSSH-auktoriserade\_nyckelfilsfältet** i PuttyGen) i **SSH PublicKey**-fältet eller välja **Välj en fil** för att bläddra och välja den fil som innehåller den offentliga nyckeln.

![Bild av formulär som ber om en offentlig nyckel](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Detta skapar en inloggning för den angivna användaren och möjliggör antingen lösenordsautentisering eller SSH-nyckelautentisering.

### <a name="azure-command-line-interface-for-mac-linux-and-windows"></a>Azure-kommandoradsgränssnitt för Mac, Linux och Windows

Du kan använda  [Azure CLI för Mac, Linux och Windows](../cli-install-nodejs.md) för att skapa ett nytt kluster med hjälp av `azure hdinsight cluster create`-kommandot.

Mer information om hur du använder det här kommandot finns i [Etablera Hadoop Linux-kluster i HDInsight med anpassade alternativ](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="connect-to-a-linux-based-hdinsight-cluster"></a>Ansluta till ett Linux-baserat HDInsight-kluster

1. Öppna PuTTY.
   
    ![putty-gränssnitt](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)
2. Om du har angett en SSH-nyckel när du skapade ditt användarkonto måste du utföra följande steg för att välja den privata nyckel som ska användas vid autentisering för klustret:
   
    I **Kategori** expanderar du **Anslutning**. Expandera sedan **SSH** och välj **Auth**. Klicka slutligen på **Bläddra** och välj den .ppk-fil som innehåller din privata nyckel.
   
    ![putty-gränssnitt, välj privat nyckel](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. I **Kategori** väljer du **Session**. Gå till skärmen **Grundalternativ för din PuTTY-session** och ange SSH-adressen för din HDInsight-server in fältet **Värdnamn (eller IP-adress)**. Det finns två möjliga SSH-adresser som du kan använda när du ansluter till ett kluster:

    ![putty-gränssnitt med ssh-adress angiven](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

    * **Huvudnodadress**: För att ansluta till klustrets huvudnod använder du klusternamnet och sedan **-ssh.azurehdinsight.net**. Till exempel **mycluster-ssh.azurehdinsight.net**.
   
    * **Kantnodadress**: Om du ansluter till en R-server i ett HDInsight-kluster, kan du ansluta till R-serverns kantnod med adressen **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, där KLUSTERNAMN är namnet på klustret. Till exempel **RServer.mycluster.ssh.azurehdinsight.net**.
     

4. Om du vill spara anslutningsinformationen för framtida bruk anger du ett namn för anslutningen under **Sparade sessioner** och klickar sedan på **Spara**. Anslutningen läggs till i listan över sparade sessioner.
5. Klicka på **Öppna** att ansluta till klustret.
   
   > [!NOTE]
   > Du får en säkerhetsvarning om det är första gången som du ansluter till klustret. Detta är normalt. Välj **Ja** till att cachelagra serverns RSA2-nyckel för att fortsätta.

6. Ange den användare som du angav när du skapade klustret när du uppmanas till detta. Om du har tillhandahållit ett lösenord för användaren uppmanas du att ange det också.

> [!NOTE]
> Stegen ovan förutsätter att du använder port 22, som kommer att ansluta till primär huvudnod i HDInsight-klustret. Om du använder port 23 ansluter du till den sekundära. Mer information om huvudnoderna finns i [Tillgänglighet och tillförlitlighet för Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md).

### <a name="connect-to-worker-nodes"></a>Ansluta till arbetsnoder

Arbetsnoderna är inte direkt åtkomliga från utanför Azure-datacentret men de kan nås från klustrets huvudnod via SSH.

Om du har angett en SSH-nyckel när du skapade ditt användarkonto, måste du utföra följande steg för att använda den privata nyckeln vid autentisering för klustret om du vill ansluta till arbetsnoderna:

1. Installera Pageant från [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). Det här verktyget används för att cachelagra SSH-nycklar för PuTTY.

2. Kör Pageant. Det minimeras till en ikon i statusfältet. Högerklicka på ikonen och välj **Lägg till nyckel**.
   
    ![lägga till nyckel](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. När bläddringsdialogrutan visas väljer du den .ppk-fil som innehåller nyckeln och klickar sedan på **Öppna**. Då läggs nyckeln till i Pageant som tillhandahåller den till PuTTY vid anslutning till klustret.
   
   > [!IMPORTANT]
   > Om du använder en SSH-nyckel för att skydda ditt konto, måste du slutföra de föregående stegen innan du kan ansluta till arbetsnoder.

4. Öppna PuTTY.

5. Om du använder en SSH-nyckel för autentisering i avsnittet **Kategori**,  expanderar du först **Anslutning** och sedan **SSH** och väljer **Auth**.
   
    I avsnittet **Autentiseringsparametrar** aktiverar du **Tillåt vidarebefordran från agent**. Detta medför att PuTTY automatiskt kan skicka certifikatautentiseringen via anslutningen till klustrets huvudnod vid anslutning till arbetsnoder.
   
    ![tillåt agenten att vidarebefordra](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. Anslut till klustret enligt tidigare beskrivning. Om du använder en SSH-nyckel för autentisering behöver du inte välja nyckeln – den SSH-nyckel som lades till i Pageant används för att autentisera för klustret.

7. När anslutningen har etablerats kan du använda följande för att hämta en lista över noderna i klustret. Ersätt *ADMINPASSWORD* med lösenordet för ditt klusteradministratörskonto. Ersätt *CLUSTERNAME* med namnet på klustret.
   
        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts
   
    Detta returnerar information i JSON-format för noderna i klustret, inklusive `host_name`, som innehåller det fullständigt kvalificerade domännamnet (FQDN) för varje nod. Följande är ett exempel på en `host_name`-post som returneras av **curl**-kommandot:
   
        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. När du har en lista över de arbetsnoder som du vill ansluta till, använder du följande kommando från PuTTY-sessionen för att öppna en anslutning till en arbetsnod:
   
        ssh USERNAME@FQDN
   
    Ersätt *USERNAME* med ditt SSH-användarnamn och *FQDN* med FQDN för arbetsnoden. Till exempel `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.
    
    > [!NOTE]
    > Om du använder ett lösenord för att autentisera SSH-sessionen uppmanas du att ange lösenordet igen. Om du använder en SSH-nyckel ska anslutningen slutföras utan några frågor.

9. När sessionen har upprättats ändras tolken för din PuTTY-session från `username@hn#-clustername` till `username@wn#-clustername` för att indikera att du är ansluten till arbetsnoden. Alla kommandon som du kör nu kommer att köras på arbetsnoden.

10. När du är klar med att utföra åtgärder på arbetsnoden använder du `exit`-kommandot för att stänga sessionen till arbetsnoden. Detta återför dig till `username@hn#-clustername`-tolken.

## <a name="add-more-accounts"></a>Lägga till fler konton

Om du behöver lägga till fler konton i klustret utför du följande steg:

1. Generera en ny offentlig nyckel och privat nyckel för det nya användarkontot så som det beskrivs ovan.

2. Lägg till den nya användaren från en SSH-session till klustret med följande kommando:
   
        sudo adduser --disabled-password <username>
   
    Detta skapar ett nytt konto men inaktiverar lösenordsautentisering.

3. Skapa katalogen och filerna för nyckeln med hjälp av följande kommandon:
   
        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. När nanoredigeraren öppnas kopierar du och klistrar in innehållet i den offentliga nyckeln för det nya användarkontot. Använd slutligen **Ctrl + X** för att spara filen och avsluta redigeraren.
   
    ![bild av nanoredigerare med exempelnyckel](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. Använd följande kommando för att ändra ägarskap för .ssh-mappen och innehåll till det nya användarkontot:
   
        sudo chown -hR <username>:<username> /home/<username>/.ssh

6. Du bör nu kunna autentisera till servern med det nya användarkontot och den privata nyckeln.

## <a id="tunnel"></a>SSH-tunnel

SSH kan användas för lokala tunnelbegäranden, till exempel webbegäranden, till HDInsight-klustret. Begäran kommer sedan att dirigeras till den begärda resursen som om den hade sitt ursprung i HDInsight-klustrets huvudnod.

> [!IMPORTANT]
> En SSH-tunnel är ett krav för att komma åt webbgränssnittet för vissa Hadoop-tjänster. Till exempel kan både jobbhistorikgränssnittet eller resurshanterargränssnittet bara användas med en SSH-tunnel.

Mer information om hur du skapar och använder en SSH-tunnel finns i [Använda SSH-tunnlar för att komma åt Ambari-webbgränssnittet, resurshanteraren, jobbhistorik, NameNode, Oozie och andra webbgränssnitt](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du autentiserar genom att använda en SSH-nyckel kan du lära dig hur du använder MapReduce med Hadoop på HDInsight.

* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

