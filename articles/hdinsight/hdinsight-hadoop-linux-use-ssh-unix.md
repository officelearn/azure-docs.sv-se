---
title: "Använda SSH med HDInsight (Hadoop) från Windows, Linux, Unix eller OS X | Microsoft Docs"
description: " Du kan komma åt HDInsight med hjälp av SSH (Secure Shell). Det här dokumentet innehåller information om hur du använder SSH med HDInsight från Windows-, Linux-, Unix- eller OS X-klienter."
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
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 4cde035f75bfa3c448f12e9ebf2896b9a54a6873
ms.lasthandoff: 02/28/2017

---
# <a name="use-ssh-with-hdinsight-hadoop-from-bash-on-windows-10-linux-unix-or-os-x"></a>Använda SSH med HDInsight (Hadoop) från Bash på Windows 10, Linux, Unix eller OS X

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

Med [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) kan du logga in i ett Linux-baserat HDInsight-kluster och köra kommandon med hjälp av ett kommandoradsgränssnitt. Det här dokumentet innehåller grundläggande information om SSH och specifik information om hur du använder SSH med HDInsight.

## <a name="what-is-ssh"></a>Vad är SSH?

SSH är ett kryptografiskt nätverksprotokoll som gör att du på ett säkert sätt kan kommunicera med en fjärrserver via ett oskyddat nätverk. SSH används för att tillhandahålla säker inloggning till en fjärrserver via en kommandorad. I detta fall, huvudnoderna eller kantnoderna i ett HDInsight-kluster.

Du kan också använda en SSH-tunnel för att skicka nätverkstrafik från klienten till HDInsight-klustret. Genom att använda en tunnel kan du komma åt tjänster i HDInsight-klustret som inte är direkt exponerade mot Internet. Mer information om hur du använder en SSH-tunnel med HDInsight finns i [Use SSH tunneling with HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) (Använda en SSH-tunnel med HDInsight).

## <a name="ssh-clients"></a>SSH-klienter

Många operativsystem tillhandahåller funktioner för SSH-klienten genom `ssh`- och `scp`-kommandoradsverktygen.

* __SSH__: En allmän SSH-klient som kan användas för att upprätta en kommandoradssession via en fjärranslutning och för att skapa tunnlar.
* __SCP__: Ett verktyg som kopierar filer mellan lokala och fjärranslutna system med hjälp av SSH-protokollet.

Windows 10 Anniversary Edition tillhandahåller Bash som en funktion för utvecklare. Den tillhandahåller `ssh`, `scp`, och andra Linux-kommandon. Mer information om hur du använder Bash för Windows 10 finns i artikeln om [Bash i Ubuntu för Windows](https://msdn.microsoft.com/commandline/wsl/about).

Om du använder Windows och inte har tillgång till Bash för Windows 10 rekommenderar vi följande SSH-klienter:

* [Git för Windows](https://git-for-windows.github.io/): Tillhandahåller `ssh`- och `scp`-kommandoradsverktygen.
* [Cygwin](https://cygwin.com/): Tillhandahåller `ssh`- och `scp`-kommandoradsverktygen.

> [!NOTE]
> I stegen i det här dokumentet förutsätter vi att du har åtkomst till `ssh`-kommandot. Om du använder en klient som puTTY eller MobaXterm läser du dokumentationen för produkten för information om motsvarande kommando och parametrar.

## <a name="ssh-authentication"></a>SSH-autentisering

En SSH-anslutning kan autentiseras med ett lösenord eller med [kryptering med offentliga nycklar (https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography). Det säkraste alternativet är att använda en nyckel eftersom den inte är sårbar för många av de attacker som lösenord kan utsättas för. Det är dock mer komplicerat att skapa och hantera nycklar än att använda ett lösenord.

Om du använder kryptografi med offentliga nycklar måste du skapa ett par med en _offentlig_ och en _privat_ nyckel.

* Den **offentliga nyckeln** läses in till noderna i ditt HDInsight-kluster, eller en annan tjänst som du vill använda med kryptering med offentliga nycklar.

* Den **privata nyckeln** är den nyckel som du uppger till HDInsight-klustret när du loggar in med en SSH-klient för att verifiera din identitet. Skydda den privata nyckeln. Dela den inte med andra.

    Du kan öka säkerheten ytterligare genom att skapa en lösenfras för den privata nyckeln. Om du använder en lösenfras måste du ange den när du autentiserar med SSH.

### <a name="create-a-public-and-private-key"></a>Skapa en offentlig och privat nyckel

`ssh-keygen`-verktyget är det enklaste sättet att skapa en offentlig och privat nyckel för användning med HDInsight. Använd följande kommando från en kommandorad för att skapa ett nytt nyckelpar för användning med HDInsight:

> [!NOTE]
> Om du använder en SSH-klient med ett grafiskt användargränssnitt, till exempel MobaXTerm eller puTTY, läser du dokumentationen för klienten för information om hur du skapar nycklar.

    ssh-keygen -t rsa -b 2048

Du uppmanas att ange följande information:

* Filplatsen: Standardplatsen är `~/.ssh/id_rsa`.

* En valfri lösenfras: Om du anger en lösenfras måste du ange den igen när du autentiserar dig mot HDInsight-klustret.

> [!IMPORTANT]
> Lösenfrasen är ett lösenord för den privata nyckeln. När du använder den privata nyckeln för att autentisera måste du ange lösenfrasen innan nyckeln kan användas. Om någon får tag i din privata nyckel kan de inte använda den utan lösenfrasen.
>
> Men om du glömmer bort lösenfrasen går det inte att återställa eller få tillbaka den.

När kommandot har körts har du två nya filer:

* __id\_rsa__: Den här filen innehåller den privata nyckeln.

    > [!WARNING]
    > Begränsa åtkomsten till den här filen för att förhindra obehörig åtkomst till tjänster som skyddas av den offentliga nyckeln.

* __id\_rsa.pub__: Den här filen innehåller den offentliga nyckeln. Du använder den här filen när du skapar ett HDInsght-kluster.

    > [!NOTE]
    > Det spelar ingen roll vem som har åtkomst till den _offentliga_ nyckeln. Allt den offentliga nyckeln kan göra i sig är att verifiera den privata nyckeln. Tjänster som SSH-servern använder den offentliga nyckeln för att verifiera din identitet när du autentiserar med hjälp av den privata nyckeln.

## <a name="configure-ssh-on-hdinsight"></a>Konfigurera SSH för HDInsight

När du skapar ett Linux-baserat HDInsight-kluster måste du ange ett _SSH-användarnamn_ och antingen ett _lösenord_ eller en _offentlig nyckel_. När klustret skapas används den här informationen för att skapa en inloggning på HDInsight-klusternoderna. Lösenordet eller den offentliga nyckeln används för att skydda användarkontot.

Mer information om hur du konfigurerar SSH när klustret skapas finns i följande dokument:

* [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Skapa HDInsight med hjälp av Azure-portalen)
* [Create HDInsight using the Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) (Skapa HDInsight med hjälp av Azure CLI)
* [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Skapa HDInsight med hjälp av Azure PowerShell)
* [Create HDInsight using Azure Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Skapa HDInsight med hjälp av Azure Resource Manager-mallar)
* [Create HDInsight using the .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) (Skapa HDInsight med hjälp av .NET SDK)
* [Create HDInsight using REST](hdinsight-hadoop-create-linux-clusters-curl-rest.md) (Skapa HDInsight med hjälp av REST)

### <a name="additional-ssh-users"></a>Ytterligare SSH-användare

Ytterligare SSH-användare kan läggas till i klustret när det har skapats, men detta rekommenderas inte.

* Nya SSH-användare måste läggas till varje nod i klustret.

* Nya SSH-användare har samma åtkomst till HDInsight som standardanvändaren. Det går inte att begränsa åtkomsten till data eller jobb i HDInsight baserat på SSH-användarkonto.

Om du vill begränsa åtkomsten per användare måste du använda ett domänanslutet HDInsight-kluster. Domänanslutna HDInsight-användare använder Active Directory för att styra åtkomsten till klusterresurser.

Med ett domänanslutet HDInsight-kluster kan du autentisera med hjälp av Active Directory när du har anslutit via SSH. Flera användare kan ansluta med SSH och sedan autentisera mot deras Active Directory-konto när de har anslutit. Mer information finns i avsnittet [Domänanslutet HDInsight](#domainjoined).

##<a id="connect"></a> Ansluta till HDInsight

När alla noder i ett HDInsight-kluster kör SSH-servern kan du bara ansluta till huvud- eller kantnoderna via det offentliga Internet.

* Du ansluter till _huvudnoderna_ genom att använda `CLUSTERNAME-ssh.azurehdinsight.net`, där __CLUSTERNAME__ är namnet på HDInsight-klustret. Anslutningar på port 22 (standard för SSH) ansluter till den primära huvudnoden. Port 23 ansluter till den sekundära huvudnoden.

* Du ansluter till en _kantnod_ genom att använda `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`, där __EDGENAME__ är namnet på kantnoden och __CLUSTERNAME__ är namnet på HDInsight-klustret. Använd port 22 när du ansluter till kantnoden.

Följande exempel visar hur du ansluter till huvudnoderna och kantnoderna i ett kluster med namnet __myhdi__ med hjälp av SSH-användarnamnet __sshuser__. Kantnoden heter __myedge__.

| Om du vill göra det här … | Använder du det här … |
| ----- | ----- |
| Ansluta till den primära huvudnoden | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| Ansluta till den sekundära huvudnoden | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| Ansluta till gränsnoden | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

Om du använder ett lösenord för att skydda SSH-kontot uppmanas du att ange lösenordet.

Om du använder en offentlig nyckel för att skydda SSH-kontot kan du behöva ange sökvägen till den matchande privata nyckeln med hjälp av växeln `-i`. Följande exempel visar hur du använder växeln `-i`:

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>Ansluta till andra noder

Arbetarnoderna och Zookeeper-noderna kan inte nås direkt utanför klustret, men de kan nås från huvudnoderna eller kantnoderna i klustret. Här är de allmänna steg som du följer för att ansluta till andra noder:

1. Använd SSH för att ansluta till en huvud- eller kantnod:

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Från SSH-anslutningen till huvud- eller kantnoden använder du `ssh`-kommandot för att ansluta till en arbetarnod i klustret:

        ssh sshuser@wn0-myhdi

    Om du vill hämta en lista över arbetarnoder i klustret hittar du exempel på hur du hämtar klusternodernas fullständigt kvalificerade namn i dokumentet [Manage HDInsight by using the Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) (Hantera HDInsight med hjälp av Ambari REST-API:et).

Om SSH-kontot skyddas med ett lösenord uppmanas du att ange lösenordet för att upprätta anslutningen.

Om du använder en SSH-nyckel för att autentisera ditt konto måste du kontrollera att din lokala miljö är konfigurerad för SSH-agentvidarebefordran.

> [!IMPORTANT]
> I följande steg förutsätter vi att du har ett Linux-/UNIX-baserat system och att du arbetar med Bash i Windows 10. Om de här stegen inte fungerar på din dator kan du behöva läsa dokumentationen för SSH-klienten.

1. Använd en textredigerare och öppna `~/.ssh/config`. Om den här filen inte finns kan du skapa den genom att ange `touch ~/.ssh/config` på en kommandorad.

2. Lägg till följande i filen. Ersätt *KLUSTERNAMN* med namnet på ditt HDInsight-kluster.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Detta konfigurerar SSH-agentvidarebefordran för ditt HDInsight-kluster.

3. Testa SSH-agentvidarebefordran med hjälp av följande kommando från terminalen:

        echo "$SSH_AUTH_SOCK"

    Det här kommandot returnerar information liknande följande text:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Om inget returneras så körs inte `ssh-agent`. Läs informationen om skripten för agentstart i [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Använda ssh-agent med ssh) eller läs dokumentationen för din SSH-klient för specifika steg som beskriver hur du installerar och konfigurerar `ssh-agent`.

4. När du har verifierat att **ssh-agent** körs använder du följande för att lägga till din privata SSH-nyckel till agenten:

        ssh-add ~/.ssh/id_rsa

    Om din privata nyckel finns i en annan fil ersätter du `~/.ssh/id_rsa` med sökvägen till filen.

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>Domänanslutet HDInsight

[Domänanslutna HDInsight](hdinsight-domain-joined-introduction.md) integrerar Kerberos med Hadoop i HDInsight. Eftersom SSH-användaren inte är en Active Directory-domänanvändare kan du inte köra Hadoop-kommandon förrän du autentiserar med Active Directory. Använd följande steg för att autentisera SSH-sessionen med Active Directory:

1. Anslut till ett domänanslutet HDInsight-kluster med SSH. Följande kommando ansluter till exempel till ett HDInsight-kluster med namnet __myhdi__ med hjälp av SSH-kontot __sshuser__.

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Använd följande för att autentisera med hjälp av en domänanvändare och ett lösenord:

        kinit

     När du uppmanas att göra det anger du ett domänanvändarnamn och lösenordet för domänanvändaren.

    Mer information om hur du konfigurerar domänanvändare för domänanslutna HDInsight-kluster finns i [Configure Domain-joined HDInisight clusters](hdinsight-domain-joined-configure.md) (Konfigurera domänanslutna HDInisight-kluster).

Efter autentiseringen med `kinit`-kommandot kan du använda Hadoop-kommandon som `hdfs dfs -ls /` eller `hive`.

## <a id="tunnel"></a>SSH-tunnel

SSH kan användas för lokala tunnelbegäranden, till exempel webbegäranden, till HDInsight-klustret. Begäran vidarebefordras till klustret och blir sedan löst inom klustret.

> [!IMPORTANT]
> En SSH-tunnel är ett krav för att komma åt webbgränssnittet för vissa Hadoop-tjänster. Till exempel kan både jobbhistorikgränssnittet eller resurshanterargränssnittet bara användas med en SSH-tunnel.

Mer information om hur du skapar och använder en SSH-tunnel finns i [Use SSH Tunneling to access Ambari web UI, JobHistory, NameNode, Oozie, and other web UIs](hdinsight-linux-ambari-ssh-tunnel.md) (Använda SSH-tunnlar för att komma åt Ambari-webbgränssnittet, JobHistory, NameNode, Oozie och andra webbgränssnitt).

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du autentiserar genom att använda en SSH-nyckel kan du lära dig hur du använder MapReduce med Hadoop på HDInsight.

* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

