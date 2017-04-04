---
title: "Använda SSH med HDInsight (Hadoop) från Windows, Linux, Unix eller OS X | Microsoft Docs"
description: " Du kan komma åt HDInsight med hjälp av SSH (Secure Shell). Det här dokumentet innehåller information om hur du använder SSH för att ansluta till HDInsight från Windows-, Linux-, Unix- eller OS X-klienter."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/16/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 89d44476e9de8ac32195efaf66535cdd9fb4260e
ms.lasthandoff: 03/25/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Ansluta till HDInsight (Hadoop) med hjälp av SSH

Lär dig hur du använder [SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell) för att ansluta till HD Insight på ett säkert sätt. HDInsight kan använda Linux (Ubuntu) som operativsystem för noder i klustret. SSH kan användas för att ansluta till huvud- och kantnoderna i ett Linux-baserat kluster och köra kommandon direkt på dessa noder.

Följande tabell innehåller adress- och portinformationen som behövs för att ansluta till HDInsight med hjälp av SSH:

| Adress | Port | Ansluter till ... |
| ----- | ----- | ----- |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Kantnoden (om en sådan finns) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Den primära huvudnoden |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Den sekundära huvudnoden |

> [!NOTE]
> Ersätt `<edgenodename>` med namnet på kantnoden. Mer information om hur du använder kantnoder finns i [Använda kantnoder i HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).
>
> Ersätt `<clustername>` med namnet på HDInsight-klustret.
>
> Vi rekommenderar att du __alltid ansluter till kantnoden__ om du har en. Värdtjänster för huvudnoder är viktiga för klustrets hälsotillstånd. Kantnoden kör bara det som du placerar på den.

## <a name="ssh-clients"></a>SSH-klienter

De flesta operativsystem tillhandahåller `ssh`-klienten. Microsoft Windows tillhandahåller ingen SSH-klient som standard. En SSH-klient för Windows finns i vart och ett av följande paket:

* [Bash i Ubuntu för Windows 10](https://msdn.microsoft.com/commandline/wsl/about): `ssh`-kommandot är tillgängligt via Bash för Windows-kommandoraden.

* [Git (https://git-scm.com/)](https://git-scm.com/): `ssh`-kommandot är tillgängligt via GitBash-kommandoraden.

* [GitHub Desktop (https://desktop.github.com/)](https://desktop.github.com/): `ssh`-kommandot är tillgängligt via Git Shell-kommandoraden. GitHub Desktop kan konfigureras att använda Bash, Windows-kommandotolken eller PowerShell som kommandorad för Git Shell.

* [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH): PowerShell-teamet porterar OpenSSH till Windows och tillhandahåller testutgåvor.

    > [!WARNING]
    > OpenSSH-paketet innehåller SSH-serverkomponenten, `sshd`. Den här komponenten startar en SSH-server på din dator så att andra kan ansluta till den. Konfigurera inte den här komponenten och öppna inte port 22 om du inte vill ha en SSH-server på din dator. Det krävs inte för att kommunicera med HDInsight.

Det finns också flera grafiska SSH-klienter, till exempel [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) och [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Dessa klienter kan användas för att ansluta till HDInsight, men processen för att ansluta till en server skiljer sig från anslutningsprocessen med `ssh`-verktyget. Mer information finns i dokumentationen för den grafiska klient som du använder.

## <a id="sshkey"></a>Autentisering: SSH-nycklar

SSH-nycklar använder [kryptografik med offentliga nycklar](https://en.wikipedia.org/wiki/Public-key_cryptography) för att skydda klustret. SSH-nycklar är säkrare än lösenord och är ett enkelt sätt att skydda HDInsight-klustret.

Om ditt SSH-konto skyddas med en nyckel måste klienten tillhandahålla den matchande privata nyckeln när du ansluter:

* De flesta klienter kan konfigureras att använda en __standardnyckel__. Exempelvis söker `ssh`-klienten efter en privat nyckel i `~/.ssh/id_rsa` i Linux- och Unix-miljöer.

* Du kan ange __sökvägen till en privat nyckel__. Med `ssh`-klienten används `-i`-parametern för att ange sökvägen till den privata nyckeln. Till exempel `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Om du har __flera privata nycklar__ för användning med olika servrar kan verktyg som [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) användas för att automatiskt välja den nyckel som ska användas.

> [!IMPORTANT]
>
> Om du skyddar den privata nyckeln med en lösenfras måste du ange lösenfrasen när du använder nyckeln. Verktyg som `ssh-agent` kan underlätta för dig genom att cachelagra lösenordet.

### <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Använd `ssh-keygen`-kommandot för att skapa filer för offentliga och privata nycklar. Följande kommando genererar ett 2048-bitars RSA-nyckelpar som kan användas med HDInsight:

    ssh-keygen -t rsa -b 2048

Du uppmanas att ange information när nyckeln skapas. Till exempel var nycklarna lagras eller om du vill använda en lösenfras. När processen har slutförts har två filer skapats: en offentlig nyckel och en privat nyckel.

* Den __offentliga nyckeln__ används för att skapa ett HDInsight-kluster. Den offentliga nyckeln har filnamnstillägget `.pub`.

* Den __privata nyckeln__ används för att autentisera din klient mot HDInsight-klustret.

> [!IMPORTANT]
> Du kan skydda dina nycklar med hjälp av en lösenfras. Detta är ett lösenord för den privata nyckeln. Även om någon får tag på din privata nyckel behöver de lösenfrasen för att kunna använda nyckeln.

### <a name="create-hdinsight-using-the-public-key"></a>Skapa HDInsight med hjälp av den offentliga nyckeln

| Genereringsmetod | Så här använder du den offentliga nyckeln |
| ------- | ------- |
| **Azure Portal** | Avmarkera __Använd samma lösenord som klusterinloggning__ och välj sedan __Offentlig nyckel__ som SSH-autentiseringstyp. Välj slutligen filen för den offentliga nyckeln eller klistra in textinnehållet från filen i fältet __Offentlig SSH-nyckel__.</br>![Dialogrutan Offentlig SSH-nyckel vid generering av HDInsight-kluster](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | Använd `-SshPublicKey`-parametern för cmdleten `New-AzureRmHdinsightCluster` och skicka innehållet i den offentliga nyckeln som en sträng.|
| **Azure CLI 1.0** | Använd `--sshPublicKey`-parametern för kommandot `azure hdinsight cluster create` och skicka innehållet i den offentliga nyckeln som en sträng. |
| **Resource Manager-mall** | Ett exempel på hur du använder SSH-nycklar med en mall finns i avsnittet [Deploy HDInsight on Linux with SSH key](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Distribuera HDInsight i Linux med en SSH-nyckel). `publicKeys`-elementet i filen [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) används för att skicka nycklarna till Azure när klustret skapas. |

## <a id="sshpassword"></a>Autentisering: lösenord

SSH-konton kan skyddas med ett lösenord. När du ansluter till HDInsight med hjälp av SSH uppmanas du att ange lösenordet.

> [!WARNING]
> Vi rekommenderar inte att du använder lösenordsautentisering för SSH. Lösenord kan gissas och är sårbara för råstyrkeattacker. I stället rekommenderar vi att du använder [SSH-nycklar för autentisering](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>Skapa HDInsight med ett lösenord

| Genereringsmetod | Så här anger du lösenordet |
| --------------- | ---------------- |
| **Azure Portal** | SSH-användarkontot har som standard samma lösenord som kontot för klusterinloggning. Om du vill använda ett annat lösenord avmarkerar du __Använd samma lösenord som klusterinloggning__ och anger sedan lösenordet i fältet __SSH-lösenord__.</br>![Dialogrutan SSH-lösenord när ett HDInsight-kluster skapas](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | Använd `--SshCredential`-parametern för cmdleten `New-AzureRmHdinsightCluster` och skicka ett `PSCredential`-objekt som innehåller SSH-användarkontonamnet och SSH-lösenordet. |
| **Azure CLI 1.0** | Använd `--sshPassword`-parametern för `azure hdinsight cluster create`-kommandot och ange lösenordsvärdet. |
| **Resource Manager-mall** | Ett exempel på hur du använder ett lösenord med en mall finns i [Deploy HDInsight on Linux with SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Distribuera HDInsight i Linux med SSH-lösenord). `linuxOperatingSystemProfile`-elementet i filen [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) används för att skicka SSH-kontonamnet och SSH-lösenordet till Azure när klustret skapas.|

### <a name="change-the-ssh-password"></a>Ändra SSH-lösenordet

Information om hur du ändrar lösenordet för SSH-användarkontot finns i avsnittet __Change passwords__ (Ändra lösenord) i dokumentet [Manage HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) (Hantera HDInsight).

## <a id="domainjoined"></a>Autentisering: Domänanslutet HDInsight-kluster

Om du använder ett __domänanslutet HDInsight-kluster__ måste du använda `kinit`-kommandot efter anslutning med SSH. Det här kommandot frågar efter en domänanvändare och ett lösenord och autentiserar din session med Azure Active Directory-domänen som är associerad med klustret.

Mer information finns i avsnittet [Configure domain-joined HDInsight](hdinsight-domain-joined-configure.md) (Konfigurera domänanslutna HDInsight-kluster).

## <a name="connect-to-worker-and-zookeeper-nodes"></a>Ansluta till arbetarnoder och Zookeeper-noder

Arbetarnoderna och Zookeeper-noderna kan inte nås direkt från Internet, men de kan nås från huvudnoderna eller kantnoderna i klustret. Här är de allmänna steg som du följer för att ansluta till andra noder:

1. Använd SSH för att ansluta till en huvud- eller kantnod:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. Från SSH-anslutningen till huvud- eller kantnoden använder du `ssh`-kommandot för att ansluta till en arbetarnod i klustret:

        ssh sshuser@wn0-myhdi

    Om du vill hämta en lista över domännamnen för noderna i klustret tittar du på exemplen i [Manage HDInsight by using the Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) (Hantera HDInsight med hjälp av Ambari REST-API:et).

Om SSH-kontot skyddas med ett __lösenord__ uppmanas du att ange lösenordet för att upprätta anslutningen.

Om SSH-kontot skyddas med __SSH-nycklar__ måste du se till att din lokala miljö har konfigurerats för vidarebefordran med SSH-agenten.

> [!NOTE]
> Ett annat sätt att direkt komma åt alla noder i klustret är att installera HDInsight i ett virtuellt Azure-nätverk. Därefter kan du ansluta till din fjärrdatorn i samma virtuella nätverk och direkt komma åt alla noder i klustret.
>
> Mer information finns i [Use a virtual network with HDInsight](hdinsight-extend-hadoop-virtual-network.md) (Använda ett virtuellt nätverk med HDInsight).

### <a name="configure-ssh-agent-forwarding"></a>Konfigurera vidarebefordran med SSH-agenten

> [!IMPORTANT]
> I följande steg förutsätter vi att du har ett Linux-/UNIX-baserat system och att du arbetar med Bash i Windows 10. Om de här stegen inte fungerar på din dator kan du behöva läsa dokumentationen för SSH-klienten.

1. Använd en textredigerare och öppna `~/.ssh/config`. Om den här filen inte finns kan du skapa den genom att ange `touch ~/.ssh/config` på en kommandorad.

2. Lägg till följande text i `config`-filen.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Ersätt informationen för __Host__ med adressen för den nod som du ansluter till med hjälp av SSH. I föregående exempel används kantnoden. Den här posten konfigurerar vidarebefordran med SSH-agenten för den angivna noden.

3. Testa SSH-agentvidarebefordran med hjälp av följande kommando från terminalen:

        echo "$SSH_AUTH_SOCK"

    Det här kommandot returnerar information liknande följande text:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Om inget returneras så körs inte `ssh-agent`. Läs informationen om skripten för agentstart i [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Använda ssh-agent med ssh) eller läs dokumentationen för din SSH-klient för specifika steg som beskriver hur du installerar och konfigurerar `ssh-agent`.

4. När du har verifierat att **ssh-agent** körs använder du följande för att lägga till din privata SSH-nyckel till agenten:

        ssh-add ~/.ssh/id_rsa

    Om din privata nyckel finns i en annan fil ersätter du `~/.ssh/id_rsa` med sökvägen till filen.

5. Anslut till kant- eller huvudnoder i klustret med hjälp av SSH. Använd sedan SSH-kommandot för att ansluta till en arbetar- eller Zookeeper-nod. Anslutningen upprättas med hjälp av den vidarebefordrade nyckeln.

## <a name="next-steps"></a>Nästa steg

* [Använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)
* [Använda ett virtuellt nätverk med HDInsight](hdinsight-extend-hadoop-virtual-network.md)
* [Använda kantnoder i HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
