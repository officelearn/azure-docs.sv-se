---
title: Använda SSH-tunnlar för att få åtkomst till Azure HDInsight
description: Lär dig hur du använder en SSH-tunnel för att på ett säkert sätt bläddra bland webb resurser som finns på dina Linux-baserade HDInsight-noder.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: cad2988a9b6d6cdf557eeabee7cc93e0bbba9267
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879579"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Använd SSH-tunnlar för att komma åt Apache Ambari Web UI, JobHistory, NameNode, Apache Oozie och andra UIs

HDInsight-kluster ger åtkomst till webbgränssnittet Apache Ambari via Internet, men vissa funktioner kräver en SSH-tunnel. Webb gränssnittet för Apache Oozie-tjänsten kan till exempel inte nås via Internet utan en SSh-tunnel.

## <a name="why-use-an-ssh-tunnel"></a>Varför ska man använda en SSH-tunnel

Flera av menyerna i Ambari fungerar bara via en SSH-tunnel. Dessa menyer är beroende av webbplatser och tjänster som körs på andra nodtyper, till exempel arbetsnoder.

Följande webb UIs kräver en SSH-tunnel:

* JobHistory
* NameNode
* Tråd stackar
* Oozie webb gränssnitt
* GRÄNSSNITT för HBase Master och loggar

Om du använder skript åtgärder för att anpassa klustret, krävs en SSH-tunnel för alla tjänster eller verktyg som du installerar som exponerar en webb tjänst. Om du till exempel installerar nyans med en skript åtgärd måste du använda en SSH-tunnel för att få åtkomst till webb gränssnittet för nyans.

> [!IMPORTANT]  
> Om du har direkt åtkomst till HDInsight via ett virtuellt nätverk behöver du inte använda SSH-tunnlar. Ett exempel på att direkt komma åt HDInsight via ett virtuellt nätverk finns i [ansluta HDInsight till ditt lokala nätverks](connect-on-premises-network.md) dokument.

## <a name="what-is-an-ssh-tunnel"></a>Vad är en SSH-tunnel?

[SSH-tunnlar (Secure Shell)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) ansluter en port på den lokala datorn till en head-nod i HDInsight. Trafik som skickas till den lokala porten dirigeras via en SSH-anslutning till Head-noden. Begäran löses som om den kommer från Head-noden. Svaret dirigeras sedan tillbaka via tunneln till din arbets Station.

## <a name="prerequisites"></a>Förutsättningar

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* En webbläsare som kan konfigureras för att använda en SOCKS5-proxy.

    > [!WARNING]  
    > Stöd för SOCKS-proxy inbyggd i Windows Internet-inställningar stöder inte SOCKS5, och fungerar inte med stegen i det här dokumentet. Följande webbläsare förlitar sig på Windows-proxyinställningar och fungerar för närvarande inte med stegen i det här dokumentet:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome förlitar sig även på inställningarna för Windows-proxy. Du kan dock installera tillägg som stöder SOCKS5. Vi rekommenderar [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Skapa en tunnel med SSH-kommandot

Använd följande kommando för att skapa en SSH-tunnel med `ssh` hjälp av kommandot. Ersätt `sshuser` med en SSH-användare för ditt HDInsight-kluster och `clustername` Ersätt med namnet på ditt HDInsight-kluster:

```cmd
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Det här kommandot skapar en anslutning som dirigerar trafik till den lokala porten 9876 till klustret via SSH. Alternativen är:

* **D 9876** – den lokala port som dirigerar trafik via tunneln.
* **C** – komprimera alla data eftersom webb trafik huvudsakligen är text.
* **2** – framtvinga SSH för att testa protokoll version 2.
* **q** -tyst läge.
* **T** -inaktivera pseudo-tty-allokering eftersom du bara vidarebefordrar en port.
* **n** -förhindra läsning av STDIN, eftersom du bara vidarebefordrar en port.
* **N** -kör inte ett fjärrkommando eftersom du bara vidarebefordrar en port.
* **f** – kör i bakgrunden.

När kommandot har slutförts dirigeras trafik som skickas till port 9876 på den lokala datorn till klustrets huvud nod.

## <a name="useputty"></a>Skapa en tunnel med hjälp av SparaTillFil

[SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty) är en grafisk SSH-klient för Windows. Om du inte är bekant med SparaTillFil kan du läsa filen med information om [SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Använd följande steg för att skapa en SSH-tunnel med hjälp av SparaTillFil:

### <a name="create-or-load-a-session"></a>Skapa eller läsa in en session

1. Öppna SparaTillFil och se till att **sessionen** är markerad på den vänstra menyn. Om du redan har sparat en session väljer du namnet på sessionen från listan **sparade sessioner** och väljer **Läs in**.

1. Om du inte redan har en sparad session anger du anslutnings informationen:
    * **Värdnamn (eller IP-adress)** – SSH-adressen för HDInsight-klustret. Till exempel **MyCluster-SSH.azurehdinsight.net**
    * **Port** – 22
    * **Anslutnings typ** – SSH

1. Välj **spara**

    ![Skapa SSH-session](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. I avsnittet **kategori** till vänster om dialog rutan expanderar du **anslutning**, expanderar **SSH**och väljer sedan **tunnlar**.

1. Ange följande information om alternativen för att **kontrol lera vidarebefordring av SSH-portar** :
   
   * **Källport** – Porten på den klient som du vill vidarebefordra. Till exempel **9876**.

   * **Mål** – SSH-adressen för HDInsight-klustret. Till exempel **mycluster-ssh.azurehdinsight.net**.

   * **Dynamisk** – Aktiverar dynamisk SOCKS-proxyroutning.
     
     ![bild av tunnel alternativ](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Välj **Lägg** till för att lägga till inställningarna och klicka sedan på **Öppna** för att öppna en SSH-anslutning.

1. När du uppmanas till det loggar du in på servern.

## <a name="use-the-tunnel-from-your-browser"></a>Använda tunneln från din webbläsare

> [!IMPORTANT]  
> Stegen i det här avsnittet använder Mozilla FireFox-webbläsaren, eftersom den ger samma proxyinställningar på alla plattformar. Andra moderna webbläsare, till exempel Google Chrome, kan kräva ett tillägg, till exempel FoxyProxy, för att arbeta med tunneln.

1. Konfigurera webbläsaren att använda **localhost** och den port som du använde när du skapade tunneln som en **SOCKS V5** -proxy. Så här ser Firefox-inställningarna ut. Om du använde en annan port än 9876 ändrar du porten till den som du använde:
   
    ![bild av Firefox-inställningar](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)
   
   > [!NOTE]  
   > Om du väljer **fjärranslutna DNS** matchas Domain Name System DNS-begäranden (DNS) med hjälp av HDInsight-klustret. Den här inställningen löser DNS med hjälp av Head-noden i klustret.

2. Kontrol lera att tunneln fungerar genom att besöka en plats [https://www.whatismyip.com/](https://www.whatismyip.com/), till exempel. Den IP-adress som returneras ska vara en som används av Microsoft Azure Data centret.

## <a name="verify-with-ambari-web-ui"></a>Verifiera med Ambari webb gränssnitt

När klustret har upprättats kan du använda följande steg för att kontrol lera att du har åtkomst till service Web UIs från Ambari-webbplatsen:

1. Gå till `http://headnodehost:8080`i webbläsaren. `headnodehost` Adressen skickas via tunneln till klustret och matchas mot Head-noden som Ambari körs på. När du uppmanas till det anger du administratörens användar namn (admin) och lösen ord för klustret. Du kan uppmanas att ange en andra gång av Ambari-webbgränssnittet. I så fall, anger du informationen igen.

   > [!NOTE]  
   > När du använder `http://headnodehost:8080` adressen för att ansluta till klustret ansluter du via tunneln. Kommunikationen skyddas med SSH-tunneln i stället för HTTPS. Om du vill ansluta via Internet med https använder `https://clustername.azurehdinsight.net`du, `clustername` där är namnet på klustret.

2. Från Ambari-webbgränssnittet väljer du HDFS i listan till vänster på sidan.

    ![Bild med HDFS valt](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. När HDFS-tjänsteinformationen visas väljer du **snabb länkar**. En lista med kluster huvud-noder visas. Välj en av huvudnoderna och välj sedan **NameNode-användargränssnittet**.

    ![Bild med snabb menyers menyn utökad](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > När du väljer __snabb länkar__kan du få en väntande indikator. Det här tillståndet kan inträffa om du har en långsam Internet anslutning. Vänta en minut eller två innan data tas emot från servern och försök sedan igen.
    >
    > Vissa poster i menyn **snabb länkar** kan vara avhuggna av höger sida av skärmen. I så fall, expanderar du menyn med musen och använder högerpilen för att rulla skärmen till höger för att se resten av menyn.

4. En sida som liknar följande bild visas:

    ![Bild av NameNode-ANVÄNDARGRÄNSSNITTET](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Lägg märke till URL: en för den här sidan. Det bör likna `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Denna URI använder det interna fullständigt kvalificerade domän namnet (FQDN) för noden och är bara tillgänglig när du använder en SSH-tunnel.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar och använder en SSH-tunnel, se följande dokument för andra sätt att använda Ambari:

* [Hantera HDInsight-kluster med hjälp av Apache Ambari](hdinsight-hadoop-manage-ambari.md)