---
title: Använda SSH-tunnlar för att komma åt Azure HDInsight | Microsoft Docs
description: Lär dig hur du använder en SSH-tunnel att på ett säkert sätt Bläddra webbresurser finns på Linux-baserat HDInsight-noder.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 797538a6d023e1a4b95680057eb0f72489290f40
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311530"
---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Använda SSH-tunnlar för att komma åt Ambari-webbgränssnittet, jobbhistorik, NameNode, Oozie och andra webb-användargränssnitt

HDInsight-kluster ger åtkomst till Ambari-webbgränssnittet via Internet, men vissa funktioner kräver en SSH-tunnel. Till exempel kan webbgränssnittet för Oozie-tjänsten inte nås via internet utan en SSh-tunnel.

## <a name="why-use-an-ssh-tunnel"></a>Varför ska jag använda en SSH-tunnel

Flera menyer i Ambari fungerar bara med en SSH-tunnel. Dessa menyer förlitar sig på webbplatser och tjänster som körs på andra nodtyper, till exempel arbetsnoderna.

Följande Web användargränssnitt kräver en SSH-tunnel:

* Jobbhistorik
* NameNode
* Trådstackar
* Oozie webbgränssnittet
* HBase-huvud- och loggar UI

Om du kan använda Script Actions för att anpassa ditt kluster, kräver alla tjänster eller verktyg som du installerar som Exponerar en webbtjänst en SSH-tunnel. Om du installerar Hue med hjälp av en skriptåtgärd använda du till exempel en SSH-tunnel till Hue-webbgränssnittet.

> [!IMPORTANT]
> Om du har direkt åtkomst till HDInsight via ett virtuellt nätverk, behöver du inte använda SSH-tunnlar. Ett exempel med direkt åtkomst till HDInsight via ett virtuellt nätverk finns i [ansluta HDInsight till ditt lokala nätverk](connect-on-premises-network.md) dokumentet.

## <a name="what-is-an-ssh-tunnel"></a>Vad är en SSH-tunnel

[Secure Shell (SSH) tunneling](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) ansluter en port på den lokala datorn till en huvudnod i HDInsight. Trafik som skickas till den lokala porten dirigeras via en SSH-anslutning till huvudnod. Begäran har lösts som om den har sitt ursprung i huvudnod. Svaret dirigeras sedan tillbaka via tunnel till din arbetsstation.

## <a name="prerequisites"></a>Förutsättningar

* En SSH-klient. De flesta operativsystem ge en SSH-klienten via den `ssh` kommando. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

* En webbläsare som kan konfigureras för att använda en proxyserver för SOCKS5.

    > [!WARNING]
    > SOCKS proxy-stöd är inbyggda i Windows Internet-inställningar har inte stöd för SOCKS5 och fungerar inte med stegen i det här dokumentet. Följande webbläsare förlitar sig på Windows-inställningar för proxy och för närvarande arbetar inte med stegen i det här dokumentet:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome använder också Windows-proxyinställningar. Du kan dock installera tillägg som stöder SOCKS5. Vi rekommenderar [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Skapa en tunnel med hjälp av SSH-kommandot

Använd följande kommando för att skapa en SSH-tunnel med hjälp av den `ssh` kommando. Ersätt **sshuser** med en SSH-användare för din HDInsight-kluster och Ersätt **klusternamn** med namnet på ditt HDInsight-kluster:

```bash
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Detta kommando skapar en anslutning som dirigerar trafik till lokal port 9876 till klustret via SSH. Alternativen är:

* **D 9876** – den lokala porten som dirigerar trafik via tunneln.
* **C** -Komprimera alla data eftersom webbtrafik är främst text.
* **2** -force SSH försöka protocol version 2.
* **q** -tyst läge.
* **T** -inaktivera pseudokolumner tty-allokering, eftersom du bara vidarebefordrar en port.
* **n** -förhindra läsningen av STDIN, eftersom du bara vidarebefordrar en port.
* **N** -inte köra fjärrkommandon, eftersom du bara vidarebefordrar en port.
* **f** -köras i bakgrunden.

När kommandot har slutförts dirigeras trafik som skickas till port 9876 på den lokala datorn till klustrets huvudnod.

## <a name="useputty"></a>Skapa en tunnel med PuTTY

[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) är en grafisk SSH-klient för Windows. Använd följande steg för att skapa en SSH-tunnel med PuTTY:

1. Öppna PuTTY och ange anslutningsinformationen. Om du inte är bekant med PuTTY finns i [PuTTY-dokumentation (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html).

2. I den **kategori** till vänster om dialogrutan, expanderar **anslutning**, expandera **SSH**, och välj sedan **tunnlar**.

3. Ange följande information på den **alternativ som styr SSH-port vidarebefordran** formulär:
   
   * **Källport** – Porten på den klient som du vill vidarebefordra. Till exempel **9876**.

   * **Mål** -SSH-adress för Linux-baserade HDInsight-klustret. Till exempel **mycluster-ssh.azurehdinsight.net**.

   * **Dynamisk** – Aktiverar dynamisk SOCKS-proxyroutning.
     
     ![Bild av tunneling alternativ](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Klicka på **Lägg till** lägga till inställningarna och klicka sedan på **öppna** att öppna en SSH-anslutning.

5. När du uppmanas logga in på servern.

## <a name="use-the-tunnel-from-your-browser"></a>Använda tunneln från din webbläsare

> [!IMPORTANT]
> Stegen i det här avsnittet använder Mozilla FireFox webbläsaren, eftersom det ger samma proxyinställningar på alla plattformar. Andra moderna webbläsare, till exempel Google Chrome kan kräva ett tillägg, till exempel FoxyProxy att arbeta med tunneln.

1. Konfigurera webbläsaren att använda **localhost** och den port du använde när du skapar tunnel som en **SOCKS v5** proxy. Här är hur Firefox inställningar se ut. Om du använder en annan port än 9876, byta till den som du:
   
    ![Bild av Firefox inställningar](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > Att välja **fjärr-DNS** löser Domain Name System (DNS)-begäranden med hjälp av HDInsight-klustret. Den här inställningen löser DNS med hjälp av huvudnod i klustret.

2. Kontrollera att tunneln fungerar genom att gå till en plats som [ http://www.whatismyip.com/ ](http://www.whatismyip.com/). Den returnerade IP-Adressen ska vara en används av Microsoft Azure-datacenter.

## <a name="verify-with-ambari-web-ui"></a>Kontrollera med Ambari-webbgränssnittet

När klustret har upprättats kan du använda följande steg för att verifiera att du kan komma åt tjänsten web användargränssnitt från Ambari Web:

1. I din webbläsare går du till http://headnodehost:8080. Den `headnodehost` adress skickas via tunneln till klustret och Lös till huvudnod Ambari körs på. När du uppmanas ange administratörsanvändarnamnet (admin) och lösenord för klustret. Du kan uppmanas en gång med Ambari-webbgränssnittet. I så fall, ange informationen.

   > [!NOTE]
   > När du använder den http://headnodehost:8080 adress för att ansluta till klustret, du ansluter via tunneln. Kommunikationen säkras med SSH-tunnel i stället för HTTPS. Om du vill ansluta via internet med hjälp av HTTPS https://clustername.azurehdinsight.net, där **klusternamn** är namnet på klustret.

2. Välj HDFS i listan till vänster på sidan Ambari-Webbgränssnittet.

    ![Bild med HDFS som valts](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. När HDFS tjänstinformation visas, väljer du **snabblänkar**. En lista över klustrets huvudnoder visas. Välj en av huvudnoderna och välj sedan **NameNode UI**.

    ![Bild med menyn snabblänkar expanderas](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > När du väljer __snabblänkar__, kan du få en indikator för vänta. Det här tillståndet kan uppstå om du har en långsam Internetanslutning. Vänta en minut eller två för data som ska tas emot från servern, försök sedan listan igen.
   >
   > Vissa poster i den **snabblänkar** menyn kan vara skärs bort på höger sida av skärmen. I så fall, expandera menyn med hjälp av musen och Använd högerpilen för att rulla skärmen till höger för att se resten av menyn.

4. En sida som liknar följande bild visas:

    ![Bild av Gränssnittet NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]
   > Lägg märke till URL: en för den här sidan. Det bör likna **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. URI: N använder interna fullständigt kvalificerade domännamnet (FQDN) för noden och är bara tillgänglig när du använder en SSH-tunnel.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar och använder en SSH-tunnel finns i följande dokument för andra sätt att använda Ambari:

* [Hantera HDInsight-kluster med Ambari](hdinsight-hadoop-manage-ambari.md)

Mer information om hur du använder SSH med HDInsight finns [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

