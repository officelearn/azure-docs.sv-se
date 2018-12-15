---
title: Använda SSH-tunnel för att komma åt Azure HDInsight
description: Lär dig hur du använder en SSH-tunnel för att på ett säkert sätt Bläddra webbresurser på Linux-baserade HDInsight-noder.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: ace025f096b756259d25ca2adb347dd23a12a910
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409532"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>Använda SSH-tunnlar för att komma åt Apache Ambari-webbgränssnittet, JobHistory, NameNode, Apache Oozie och andra web UIs

HDInsight-kluster ger åtkomst till Apache Ambari-webbgränssnittet via Internet, men vissa funktioner kräver en SSH-tunnel. Till exempel kan webbgränssnittet för Apache Oozie-tjänsten inte nås via internet utan en SSh-tunnel.

## <a name="why-use-an-ssh-tunnel"></a>Varför ska jag använda en SSH-tunnel

Flera menyer i Ambari fungerar endast en SSH-tunnel. Menyerna förlitar sig på webbplatser och tjänster som körs på andra nodtyper, till exempel arbetsnoder.

Följande Web UIs kräver en SSH-tunnel:

* JobHistory
* NameNode
* Trådstackar
* Oozie-Webbgränssnittet
* Loggar och HBase Master UI

Om du använder skriptåtgärder för att anpassa ditt kluster, kräver alla tjänster eller verktyg som du installerar som Exponerar en webbtjänst en SSH-tunnel. Om du installerar nyans med en skriptåtgärd kan använda du till exempel en SSH-tunnel till Hue-webbgränssnittet.

> [!IMPORTANT]  
> Om du har direkt åtkomst till HDInsight via ett virtuellt nätverk, behöver du inte använda SSH-tunnlar. Ett exempel för att komma åt HDInsight direkt via ett virtuellt nätverk finns i den [ansluta HDInsight till det lokala nätverket](connect-on-premises-network.md) dokumentet.

## <a name="what-is-an-ssh-tunnel"></a>Vad är en SSH-tunnel

[Secure Shell (SSH)-tunnel](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) ansluter en port på den lokala datorn till huvudnoden på HDInsight. Trafik som skickas till den lokala porten dirigeras via en SSH-anslutning till klustrets huvudnod. Begäran har åtgärdats som om den har sitt ursprung på huvudnoden. Svaret dirigeras sedan tillbaka via tunneln till din arbetsstation.

## <a name="prerequisites"></a>Förutsättningar

* En SSH-klient. De flesta operativsystem tillhandahåller en SSH-klient via den `ssh` kommando. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

* En webbläsare som kan konfigureras för att använda en SOCKS5-proxy.

    > [!WARNING]
    > Stöd för proxy SOCKS, inbyggd i Windows Internet-inställningar har inte stöd för SOCKS5 och fungerar inte med stegen i det här dokumentet. Följande webbläsare förlitar sig på Windows-proxyinställningar och för närvarande arbetar inte med stegen i det här dokumentet:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome förlitar sig även på Windows-proxyinställningar. Du kan dock installera tillägg som stöder SOCKS5. Vi rekommenderar att [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Skapa en tunnel med SSH-kommandot

Använd följande kommando för att skapa en SSH-tunnel med hjälp av den `ssh` kommando. Ersätt **sshuser** med en SSH-användare för din HDInsight-kluster och Ersätt **clustername** med namnet på ditt HDInsight-kluster:

```bash
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Det här kommandot skapar en anslutning som dirigerar trafik till den lokala porten 9876 till klustret via SSH. Alternativen är:

* **D 9876** – den lokala porten som dirigerar trafik via tunneln.
* **C** -Komprimera alla data, eftersom Internet-trafik är främst text.
* **2** -force SSH försöker protocol version 2.
* **frågor och** -tyst läge.
* **T** -inaktivera pseudo-tty-allokering, eftersom du bara vidarebefordrar en port.
* **n** -förhindra läsning av STDIN, eftersom du bara vidarebefordrar en port.
* **N** -inte köra fjärrkommandon, eftersom du bara vidarebefordrar en port.
* **f** -köras i bakgrunden.

När kommandot har slutförts, dirigeras trafik som skickas till port 9876 på den lokala datorn till klustrets huvudnod.

## <a name="useputty"></a>Skapa en tunnel med PuTTY

[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) är en grafisk SSH-klient för Windows. Om du inte är bekant med PuTTY läser den [PuTTY-dokumentationen](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Använd följande steg för att skapa en SSH-tunnel som använder PuTTY:

### <a name="create-or-load-a-session"></a>Skapa eller läsa in en session

1. Öppna PuTTY och kontrollera **Session** väljs på den vänstra menyn. Om du redan har sparat en session väljer sessionsnamnet från den **sparade sessioner** listan och klicka på **belastningen**.

1. Om du inte redan har en sparad session, anger du information om din:
    * **Värdnamn (eller IP-adress)** -SSH-adressen för HDInsight-klustret. Till exempel **mycluster-ssh.azurehdinsight.net**
    * **Port** – 22
    * **Anslutningstypen** – SSH
1. Klicka på **Spara**

    ![Skapa SSH-session](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

2. I den **kategori** till vänster om dialogrutan, expandera **anslutning**, expandera **SSH**, och välj sedan **tunnlar**.

3. Ange följande information på den **alternativ som styr SSH port vidarebefordran** formuläret:
   
   * **Källport** – Porten på den klient som du vill vidarebefordra. Till exempel **9876**.

   * **Mål** -SSH-adressen för HDInsight-klustret. Till exempel **mycluster-ssh.azurehdinsight.net**.

   * **Dynamisk** – Aktiverar dynamisk SOCKS-proxyroutning.
     
     ![Bild av tunneling alternativ](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Klicka på **Lägg till** lägga till inställningarna och klicka sedan på **öppna** att öppna en SSH-anslutning.

5. När du uppmanas logga in på servern.

## <a name="use-the-tunnel-from-your-browser"></a>Använda tunneln från din webbläsare

> [!IMPORTANT]  
> Stegen i det här avsnittet använder webbläsaren Mozilla FireFox eftersom den innehåller samma proxyinställningar på alla plattformar. Andra webbläsare, till exempel Google Chrome kan kräva ett tillägg, till exempel FoxyProxy att arbeta med tunneln.

1. Konfigurera webbläsaren att använda **localhost** och den port du använde när du skapar tunnel som en **SOCKS v5** proxy. Här är hur Firefox-inställningar som ska se ut. Om du använder en annan port än 9876, byta till den som du använde:
   
    ![Bild av Firefox-inställningar](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]  
   > Att välja **fjärr-DNS** löser Domain Name System (DNS)-begäranden med hjälp av HDInsight-kluster. Den här inställningen löser DNS med hjälp av huvudnod i klustret.

2. Kontrollera att tunneln fungerar genom att gå till en plats som [ http://www.whatismyip.com/ ](http://www.whatismyip.com/). Den returnerade IP-Adressen måste vara en används av Microsoft Azure-datacenter.

## <a name="verify-with-ambari-web-ui"></a>Verifiera med Ambari-webbgränssnittet

När klustret har upprättats, Använd följande steg för att verifiera att du kan komma åt tjänsten web UIs Ambari Web:

1. I webbläsaren går du till http://headnodehost:8080. Den `headnodehost` adress skickas via tunneln till klustret och Lös till huvudnoden som Ambari körs på. När du uppmanas, anger du det användarnamn för administratör (admin) och lösenord för klustret. Du kan uppmanas en gång med Ambari-webbgränssnittet. I så, fall ange informationen.

   > [!NOTE]  
   > När du använder den http://headnodehost:8080 -adressen för att ansluta till klustret, du ansluter via tunneln. Kommunikationen skyddas med hjälp av SSH-tunnel i stället för HTTPS. Om du vill ansluta via internet med hjälp av HTTPS, https://clustername.azurehdinsight.net, där **clustername** är namnet på klustret.

2. Ambari-Webbgränssnittet Välj HDFS i listan till vänster på sidan.

    ![Bild med HDFS valt](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. När information om tjänstens HDFS visas, väljer du **snabblänkar**. En lista över klustrets huvudnoder visas. Välj ett av huvudnoderna och välj sedan **NameNode UI**.

    ![Bild med expanderad meny med snabblänkar](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]  
   > När du väljer __snabblänkar__, kan du få en wait-indikator. Det här tillståndet kan inträffa om du har en långsam Internetanslutning. Vänta en minut eller två innan data tas emot från servern och försök sedan listan igen.
   >
   > Vissa poster i den **snabblänkar** menyn kan vara klipps bort höger sida av skärmen. I så, fall expanderar du menyn med hjälp av musen och Använd högerpilen för att rulla skärmen till höger för att se resten av menyn.

4. Visas en sida som liknar följande bild:

    ![Bild av NameNode Användargränssnittet](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]  
   > Lägg märke till URL-Adressen för den här sidan. Det bör likna **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. URI: N använder det interna fullständigt kvalificerade domännamnet (FQDN) för noden och är endast tillgänglig när du använder en SSH-tunnel.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar och använder en SSH-tunnel, se följande dokument för andra sätt att använda Ambari:

* [Hantera HDInsight-kluster med hjälp av Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Mer information om hur du använder SSH med HDInsight finns i [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

