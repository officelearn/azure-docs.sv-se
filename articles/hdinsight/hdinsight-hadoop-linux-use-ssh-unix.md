<properties
   pageTitle="Använda SSH-nycklar med Linux-baserade Hadoop från Linux, Unix eller OS X | Microsoft Azure"
   description=" Du kan komma åt Linux-baserade HDInsight med hjälp av SSH (Secure Shell). Det här dokumentet innehåller information om att använda SSH med HDInsight från Linux-, Unix- eller OS X-klienter."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/30/2016"
   ms.author="larryfr"/>

#Använda SSH med Linux-baserade Hadoop på HDInsight från Linux, Unix eller OS X

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) gör att du kan utföra åtgärder på Linux-baserade HDInsight-kluster med ett kommandoradsgränssnitt via fjärranslutning. Det här dokumentet innehåller information om att använda SSH med HDInsight från Linux-, Unix- eller OS X-klienter.

> [AZURE.NOTE] Stegen i den här artikeln förutsätter att du använder en Linux-, Unix- eller OS X-klient. De här stegen kan utföras på en Windows-baserad klient om du har installerat ett paket som innehåller `ssh` och `ssh-keygen`, såsom [Bash på Ubuntu på Windows](https://msdn.microsoft.com/commandline/wsl/about).
>
> Om du inte har installerat SSH på din Windows-baserade klient följer du stegen i [Använda SSH med Linux-baserat HDInsight (Hadoop) från Windows](hdinsight-hadoop-linux-use-ssh-windows.md) för att få information om hur du installerar och använder PuTTY.

##Krav

* **ssh-keygen** och **ssh** för Linux-, Unix- och OS X-klienter. Dessa verktyg följer vanligen med operativsystemet eller är tillgängliga via systemet för pakethantering.

* En modern webbläsare som stöder HTML5.

ELLER

* [Azure CLI](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##Vad är SSH?

SSH är ett verktyg för att logga in till och från en annan dator fjärrstyra kommandon på en fjärrserver. Med Linux-baserat HDInsight etablerar SSH en krypterad anslutning till klustrets huvudnod och etablerar en kommandorad som du använder för att skriva in kommandon. Kommandona körs sedan direkt på servern.

###SSH-användarnamn

Ett SSH-användarnamn är det namn som du använder för att autentisera för HDInsight-klustret. Om du anger ett SSH-användarnamn när klustret skapas kommer den här användaren att skapas på alla noder i klustret. Du kan använda det här användarnamnet för att ansluta till HDInsight-klustrets huvudnoder när klustret har skapats. Från huvudnoderna kan du därefter ansluta till de enskilda arbetsnoderna.

###SSH-lösenord eller offentlig nyckel

En SSH-användare kan använda ett lösenord eller en offentlig nyckel för autentisering. Ett lösenord är bara en textsträng som du hittar på medan en offentlig nyckel är en del av ett krypterat nyckelpar som genereras för unik identifiering av dig.

En nyckel är säkrare än ett lösenord, men det krävs ytterligare steg för att generera nyckeln och du måste lagra filerna som innehåller nyckeln på en säker plats. Om någon får tillgång till nyckelfilerna får de tillgång till ditt konto. Och om du förlorar nyckelfilerna kan du inte kan logga in på ditt konto.

Ett nyckelpar består av en offentlig nyckel (som skickas till HDInsight-servern) och en privat nyckel (som sparas på klientdatorn.) När du ansluter till HDInsight-servern med SSH använder SSH-klienten den privata nyckeln på datorn för att autentisera med servern.

##Skapa en SSH-nyckel

Använd följande information om du tänker använda SSH-nycklar med ditt kluster. Om du tänker använda ett lösenord kan du hoppa över det här avsnittet.

1. Öppna en terminalsession och använd följande kommando för att se om du har några befintliga SSH-nycklar:

        ls -al ~/.ssh

    Leta efter följande filer i kataloglistan. Detta är vanliga namn för offentliga SSH-nycklar.

    * id\_dsa.pub
    * id\_ecdsa.pub
    * id\_ed25519.pub
    * id\_rsa.pub

2. Om du inte vill använda en befintlig fil eller om du inte har några befintliga SSH-nycklar utför du följande åtgärder för att skapa en ny fil:

        ssh-keygen -t rsa

    Du uppmanas att ange följande information:

    * Filplatsen – standard för platsen är ~/.ssh/id\_rsa.
    * En lösenfras – du uppmanas att ange den igen.

        > [AZURE.NOTE] Vi rekommenderar starkt att du använder en säker lösenfras för nyckeln. Men om du glömmer bort lösenfrasen går det inte att återställa den.

    När kommandot har slutförts får du två nya filer, den privata nyckeln (till exempel **id\_rsa**) och den offentliga nyckeln (till exempel **id\_rsa.pub**).

##Skapa ett Linux-baserat HDInsight-kluster

När du skapar ett Linux-baserat HDInsight-kluster måste du ange den offentliga nyckeln som skapats tidigare. Det finns två sätt att skapa ett HDInsight-kluster från Linux-, Unix- eller OS X-klienter:

* **Azure-portalen** – använder en webbaserad portal för att skapa klustret.

* **Azure CLI för Mac, Linux och Windows** – använder kommandon på kommandoraden för att skapa klustret.

Var och en av dessa metoder kräver ett lösenord eller en offentlig nyckel. Fullständig information om hur du skapar ett Linux-baserat HDInsight-kluster finns i [Etablera Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

###Azure Portal

När du använder [Azure-portalen][Preview Portal] för att skapa ett Linux-baserat HDInsight-kluster, måste du ange ett **SSH-ANVÄNDARNAMN** och välja att ange ett **lösenord** eller en **offentlig SSH- NYCKEL**.

Om du väljer **SSH PUBLIC KEY** kan du antingen klistra in den offentliga nyckeln (finns i filen med **pub**-tillägget) i fältet __SSH PublicKey__ eller välja __Välj en fil__ för att bläddra och välja filen för den offentliga nyckeln.

![Bild av formuläret som ber om offentlig nyckel](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] Nyckelfilen är helt enkelt en textfil. Innehållet bör se ut ungefär så här:
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Detta skapar en inloggning för den angivna användaren med hjälp av det lösenord eller den offentliga nyckel som du anger.

###Azure-kommandoradsgränssnittet för Mac, Linux och Windows

Du kan använda  [Azure CLI för Mac, Linux och Windows](../xplat-cli-install.md) för att skapa ett nytt kluster med hjälp av `azure hdinsight cluster create`-kommandot.

Mer information om hur du använder det här kommandot finns i [Etablera Hadoop Linux-kluster i HDInsight med anpassade alternativ](hdinsight-hadoop-provision-linux-clusters.md).

##Ansluta till ett Linux-baserat HDInsight-kluster

Använd SSH-kommandot från en terminalsession för att ansluta till klustrets huvudnod med adressen och användarnamnet:

* **SSH-adress** – det finns två adresser som kan användas för att ansluta till ett kluster med SSH:

    * **Ansluta till huvudnoden**: klustrets namn följt av **-ssh.azurehdinsight.net**. Till exempel **det här klustret-ssh.azurehdinsight.net**.
    
    * **Ansluta till kantnoden**: om klustret är R-servern på HDInsight innehåller klustret även en kantnod som du kan nå genom att ange **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, där __CLUSTERNAME__ är namnet på klustret.

* **Användarnamn** – SSH-användarnamnet du angav när du skapade klustret.

Med följande exempel ansluter du till huvudnoden 0 av **det här klustret** som användaren **jag**:

    ssh me@mycluster-ssh.azurehdinsight.net

Om du har använt ett lösenord för användarkontot uppmanas du att ange lösenordet.

Om du har använt en SSH-nyckel som är skyddad med en lösenfras uppmanas du att ange lösenfrasen. I annat fall försöker SSH automatiskt autentisera med någon av de lokala privata nycklarna på klienten.

> [AZURE.NOTE] Om SSH inte autentiserar automatiskt med rätt privat nyckel använder du **-i**-parametern och anger sökvägen till den privata nyckeln. I följande exempel läses den privata nyckeln in från `~/.ssh/id_rsa`:
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

Om ingen port anges när du ansluter med adressen för huvudnoden blir SSH som standard port 22. I dessa fall ansluter du till huvudnod 0 på HDInsight-klustret. Om du använder port 23 ansluter du till huvudnod 1. Mer information om huvudnoderna finns i [Tillgänglighet och tillförlitlighet för Hadoop-kluster i HDInsight](hdinsight-high-availability-linux.md).

###Ansluta till arbetsnoder

Arbetsnoderna är inte direktåtkomliga från utanför Azure-datacentret men de kan nås från klustrets huvudnod via SSH.

Om du använder en SSH-nyckel för att verifiera ditt användarkonto måste du utföra följande steg på klienten:

1. Använd en textredigerare och öppna `~/.ssh/config`. Om den här filen inte finns kan du skapa den genom att ange `touch ~/.ssh/config` i terminalen.

2. Lägg till följande i filen. Ersätt *KLUSTERNAMN* med namnet på ditt HDInsight-kluster.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Detta konfigurerar SSH-agentvidarebefordran för ditt HDInsight-kluster.

3. Testa SSH-agentvidarebefordran med hjälp av följande kommando från terminalen:

        echo "$SSH_AUTH_SOCK"

    Detta bör returnera information som liknar följande:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Om inget returneras indikerar det att **ssh-agent** inte körs. I dokumentationen till operativsystemet finns specifika anvisningar för installation och konfiguration av **ssh-agent**. Du kan även gå till [Använda ssh-agenten med ssh](http://mah.everybody.org/docs/ssh).

4. När du har verifierat att **ssh-agent** körs använder du följande för att lägga till din privata SSH-nyckel till agenten:

        ssh-add ~/.ssh/id_rsa

    Om din privata nyckel finns i en annan fil ersätter du `~/.ssh/id_rsa` med sökvägen till filen.

Använd följande steg för att ansluta till arbetsnoderna för klustret.

> [AZURE.IMPORTANT] Om du använder en SSH-nyckel för att verifiera ditt konto måste du slutföra de föregående stegen för att verifiera att agentens vidarebefordran fungerar.

1. Anslut till HDInsight-klustret med hjälp av SSH enligt tidigare beskrivning.

2. När du är ansluten utför du följande åtgärder för att hämta en lista över noderna i klustret. Ersätt *ADMINLÖSENORD* med lösenordet för ditt klusteradministratörskonto. Ersätt *CLUSTERNAME* med namnet på klustret.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Detta returnerar information i JSON-format för noderna i klustret, inklusive `host_name`, som innehåller det fullständigt kvalificerade domännamnet (FQDN) för varje nod. Följande är ett exempel på en `host_name` post som returneras av **curl**-kommandot:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. När du har en lista över arbetsnoder som du vill ansluta till använder du följande kommando från SSH-sessionen till servern för att öppna en anslutning till en arbetsnod:

        ssh USERNAME@FQDN

    Ersätt *ANVÄNDARNAMN* med SSH-användarnamnet och *FQDN* med FQDN för arbetsnoden. Till exempel `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Om du använder ett lösenord för att autentisera SSH-sessionen uppmanas du att ange lösenordet igen. Om du använder en SSH-nyckel bör anslutningen slutföras utan några uppmaningar.

4. När sessionen har upprättats ändras uppmaningen från `username@hn0-clustername` till `username@wk0-clustername` för att indikera att du är ansluten till arbetsnoden. Alla kommandon som du kör nu kommer att köras på arbetsnoden.

4. När du är klar med att utföra åtgärder på arbetsnoden använder du `exit`-kommandot för att stänga sessionen till arbetsnoden. Detta återför dig till `username@hn0-clustername`-tolken.

##Lägga till fler konton

1. Generera en ny offentlig nyckel och privat nyckel för det nya användarkontot enligt beskrivningen i avsnittet [Skapa en SSH-nyckel](#create-an-ssh-key-optional).

    > [AZURE.NOTE] Den privata nyckeln ska antingen genereras på en klient som användaren använder för att ansluta till klustret eller på ett säkert sätt överföras till en sådan klient när den har skapats.

1. Lägg till den nya användaren med följande kommando från en SSH-session i klustret:

        sudo adduser --disabled-password <username>

    Detta skapar ett nytt konto men inaktiverar lösenordsautentisering.

2. Skapa katalogen och filerna för nyckeln med hjälp av följande kommandon:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

3. När nanoredigeraren öppnas kopierar du och klistrar in innehållet i den offentliga nyckeln för det nya användarkontot. Använd slutligen **Ctrl + X** för att spara filen och avsluta redigeraren.

    ![bild av nanoredigerare med exempelnyckel](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Använd följande kommando för att ändra ägarskap för .ssh-mappen och innehåll till det nya användarkontot:

        sudo chown -hR <username>:<username> /home/<username>/.ssh

5. Du bör nu kunna autentisera till servern med det nya användarkontot och den privata nyckeln.

##<a id="tunnel"></a>SSH-tunnel

SSH kan användas för lokala tunnelbegäranden, till exempel webbegäranden, till HDInsight-klustret. Begäran kommer sedan att dirigeras till den begärda resursen som om den hade sitt ursprung i HDInsight-klustrets huvudnod.

> [AZURE.IMPORTANT] En SSH-tunnel är ett krav för att komma åt webbgränssnittet för vissa Hadoop-tjänster. Till exempel kan både jobbhistorikgränssnittet eller resurshanterargränssnittet bara användas med en SSH-tunnel.

Mer information om hur du skapar och använder en SSH-tunnel finns i [Använda SSH-tunnlar för att komma åt Ambari-webbgränssnittet, resurshanteraren, jobbhistorik, NameNode, Oozie och andra webbgränssnitt](hdinsight-linux-ambari-ssh-tunnel.md).

##Nästa steg

Nu när du vet hur du autentiserar genom att använda en SSH-nyckel kan du lära dig hur du använder MapReduce med Hadoop på HDInsight.

* [Använda Hive med HDInsight](hdinsight-use-hive.md)

* [Använda Pig med HDInsight](hdinsight-use-pig.md)

* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)

[Preview Portal]: https://portal.azure.com/



<!--HONumber=sep16_HO1-->


