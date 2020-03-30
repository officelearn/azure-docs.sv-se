---
title: Använda SSH-tunnlar för att komma åt Azure HDInsight
description: Lär dig hur du använder en SSH-tunnel för att säkert bläddra bland webbresurser som finns på dina Linux-baserade HDInsight-noder.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 6f4efd9a316b92f17f89cea66a7c81e84ac3cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72991359"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Använd SSH-tunnel för att komma åt Apache Ambari webbgränssnitt, JobHistory, NameNode, Apache Oozie och andra UIs

HDInsight-kluster ger tillgång till Apache Ambari webbgränssnittet via Internet, men vissa funktioner kräver en SSH-tunnel. Webbgränssnittet för Apache Oozie-tjänsten kan till exempel inte nås via internet utan en SSh-tunnel.

## <a name="why-use-an-ssh-tunnel"></a>Varför använda en SSH-tunnel

Flera av menyerna i Ambari fungerar bara genom en SSH-tunnel. Dessa menyer är beroende av webbplatser och tjänster som körs på andra nodtyper, till exempel arbetsnoder.

Följande webb-UIs kräver en SSH-tunnel:

* JobbHistoria
* NamnNod
* Gängstackar
* Oozie webb UI
* HBase-huvud- och loggargränssnitt

Om du använder skriptåtgärder för att anpassa klustret kräver alla tjänster eller verktyg som du installerar som exponerar en webbtjänst en SSH-tunnel. Om du till exempel installerar Hue med hjälp av en skriptåtgärd måste du använda en SSH-tunnel för att komma åt webbgränssnittet i Hue.

> [!IMPORTANT]  
> Om du har direkt åtkomst till HDInsight via ett virtuellt nätverk behöver du inte använda SSH-tunnlar. Ett exempel på direkt åtkomst till HDInsight via ett virtuellt nätverk finns i [Connect HDInsight till ditt lokala nätverksdokument.](connect-on-premises-network.md)

## <a name="what-is-an-ssh-tunnel"></a>Vad är en SSH-tunnel

[Säker Shell-tunnel (SSH) ansluter](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) en port på din lokala dator till en huvudnod på HDInsight. Trafik som skickas till den lokala porten dirigeras via en SSH-anslutning till huvudnoden. Begäran löses som om den hade sitt ursprung på huvudnoden. Svaret dirigeras sedan tillbaka genom tunneln till din arbetsstation.

## <a name="prerequisites"></a>Krav

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* En webbläsare som kan konfigureras för att använda en SOCKS5-proxy.

    > [!WARNING]  
    > SOCKS-proxysupporten som är inbyggd i Windows Internet-inställningar stöder inte SOCKS5 och fungerar inte med stegen i det här dokumentet. Följande webbläsare är beroende av Windows-proxyinställningar och fungerar för närvarande inte med stegen i det här dokumentet:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome förlitar sig också på proxyinställningarna för Windows. Du kan dock installera tillägg som stöder SOCKS5. Vi rekommenderar [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Skapa en tunnel med kommandot SSH

Använd följande kommando för att skapa `ssh` en SSH-tunnel med kommandot. Ersätt `sshuser` med en SSH-användare för ditt `CLUSTERNAME` HDInsight-kluster och ersätt med namnet på ditt HDInsight-kluster:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Det här kommandot skapar en anslutning som dirigerar trafik till lokal port 9876 till klustret via SSH. Alternativen är:

* **D 9876** - Den lokala hamnen som dirigerar trafik genom tunneln.
* **C** - Komprimera alla data, eftersom webbtrafik oftast är text.
* **2** - Tvinga SSH att endast prova protokollversion 2.
* **q** - Tyst läge.
* **T** - Inaktivera pseudo-tty tilldelning, eftersom du bara vidarebefordra en port.
* **n** - Förhindra läsning av STDIN, eftersom du bara vidarebefordrar en port.
* **N** - Kör inte ett fjärrkommando, eftersom du bara vidarebefordrar en port.
* **f** - Kör i bakgrunden.

När kommandot är klart dirigeras trafik som skickas till port 9876 på den lokala datorn till klusterhuvudnoden.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Skapa en tunnel med PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) är en grafisk SSH-klient för Windows. Om du inte är bekant med PuTTY läser du [PuTTY-dokumentationen](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Gör så här för att skapa en SSH-tunnel med PuTTY:

### <a name="create-or-load-a-session"></a>Skapa eller läsa in en session

1. Öppna PuTTY och se till att **session** väljs på den vänstra menyn. Om du redan har sparat en session väljer du sessionsnamnet i listan **Sparade sessioner** och väljer **Läs in**.

1. Om du inte redan har en sparad session anger du anslutningsinformationen:

    |Egenskap |Värde |
    |---|---|
    |Värdnamn (eller IP-adress)|SSH-adressen för HDInsight-klustret. Till exempel **det här klustret-ssh.azurehdinsight.net**.|
    |Port|22|
    |Anslutningstyp|SSH|

1. Välj **Spara**

    ![HDInsight skapa kittsession](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Expandera **Anslutning,** expandera **SSH**i avsnittet **Kategori** till vänster om dialogrutan och välj sedan **Tunnlar**.

1. Ange följande information om formuläret **Alternativ för att styra SSH-portens vidarebefordran:**

    |Egenskap |Värde |
    |---|---|
    |Källport|Porten på klienten som du vill vidarebefordra. Till exempel **9876**.|
    |Mål|SSH-adressen för HDInsight-klustret. Till exempel **det här klustret-ssh.azurehdinsight.net**.|
    |Dynamisk|Aktiverar dynamisk SOCKS-proxyroutning.|

    ![Tunnelalternativ för PuTTY-konfiguration](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Välj **Lägg till** om du vill lägga till inställningarna och välj sedan **Öppna** för att öppna en SSH-anslutning.

1. Logga in på servern när du uppmanas att göra det.

## <a name="use-the-tunnel-from-your-browser"></a>Använd tunneln från webbläsaren

> [!IMPORTANT]  
> Stegen i det här avsnittet använder webbläsaren Mozilla FireFox, eftersom den innehåller samma proxyinställningar på alla plattformar. Andra moderna webbläsare, till exempel Google Chrome, kan kräva ett tillägg som FoxyProxy för att fungera med tunneln.

1. Konfigurera webbläsaren så att den använder **localhost** och porten som du använde när du skapade tunneln som en **SOCKS v5-proxy.** Så här ser Firefox-inställningarna ut. Om du har använt en annan port än 9876 ändrar du porten till den du använde:

    ![Proxyinställningar för Firefox-webbläsare](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > Om du väljer **Fjärr-DNS** matchas DNS-begäranden (Domain Name System) med hjälp av HDInsight-klustret. Den här inställningen löser DNS med hjälp av huvudnoden i klustret.

2. Kontrollera att tunneln fungerar genom att [https://www.whatismyip.com/](https://www.whatismyip.com/)besöka en plats som . IP-adressen som returneras ska vara en som används av Microsoft Azure-datacentret.

## <a name="verify-with-ambari-web-ui"></a>Verifiera med Ambari webbgränssnitt

När klustret har upprättats använder du följande steg för att kontrollera att du kan komma åt tjänstweet från Ambari-webbplatsen:

1. Gå till `http://headnodehost:8080` i webbläsaren. Adressen `headnodehost` skickas över tunneln till klustret och lös till huvudnoden som Ambari körs på. När du uppmanas till det anger du administratörens användarnamn (administratör) och lösenord för klustret. Du kan bli tillfrågad en andra gång av Ambari webbgränssnitt. Om så är fallet, gå in på informationen igen.

   > [!NOTE]  
   > När du `http://headnodehost:8080` använder adressen för att ansluta till klustret ansluter du via tunneln. Kommunikationen är säkrad med SSH-tunneln i stället för HTTPS. Om du vill ansluta via `https://clustername.azurehdinsight.net`internet `clustername` med HTTPS använder du , där är namnet på klustret.

2. Välj HDFS i listan till vänster på sidan i webbgränssnittet i Ambari.

    ![Apache Ambari hdfs tjänst vald](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. När HDFS-tjänstinformationen visas väljer du **Snabblänkar**. En lista över klusterhuvudnoderna visas. Markera en av huvudnoderna och välj sedan **NameNode UI**.

    ![Bilden med snabblänksmenyn expanderad](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > När du väljer __Snabblänkar__kan du få en vänteindikator. Detta tillstånd kan uppstå om du har en långsam internetuppkoppling. Vänta en minut eller två för att data ska tas emot från servern och försök sedan med listan igen.
    >
    > Vissa poster i **snabblänksmenyn** kan stängas av till höger på skärmen. Om så är fallet expanderar du menyn med musen och använder högerpil för att rulla skärmen åt höger för att se resten av menyn.

4. En sida som liknar följande bild visas:

    ![Bild av hadoop namenode-användargränssnittet](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Lägg märke till webbadressen till den här sidan. det bör likna `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Den här URI:n använder nodens interna fullständigt kvalificerade domännamn (FQDN) och är endast tillgänglig när du använder en SSH-tunnel.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar och använder en SSH-tunnel läser du följande dokument för andra sätt att använda Ambari:

* [Hantera HDInsight-kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)
