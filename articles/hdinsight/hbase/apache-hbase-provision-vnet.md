---
title: Skapa HBase-kluster i ett virtuellt nätverk - Azure
description: Kom igång med HBase i Azure HDInsight. Lär dig hur du skapar HDInsight HBase-kluster i Azure Virtual Network.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: e4e15d1c6554fc567f668b2033bff5b5664db918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972799"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Skapa Apache HBase-kluster på HDInsight i Azure Virtual Network

Lär dig hur du skapar Azure HDInsight Apache HBase-kluster i ett [Virtuellt Azure-nätverk](https://azure.microsoft.com/services/virtual-network/).

Med integrering av virtuella nätverk kan Apache HBase-kluster distribueras till samma virtuella nätverk som dina program så att program kan kommunicera med HBase direkt. Fördelarna innefattar:

* Direkt anslutning av webbprogrammet till noderna i HBase-klustret, vilket möjliggör kommunikation via HBase Java remote procedure call (RPC) API:er.
* Förbättrad prestanda genom att inte låta trafiken gå över flera gateways och belastningsutjämnare.
* Möjligheten att behandla känslig information på ett säkrare sätt utan att exponera en offentlig slutpunkt.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Skapa Apache HBase-kluster till virtuellt nätverk

I det här avsnittet skapar du ett Linux-baserat Apache HBase-kluster med det beroende Azure Storage-kontot i ett virtuellt Azure-nätverk med hjälp av en [Azure Resource Manager-mall](../../azure-resource-manager/templates/deploy-powershell.md). Andra metoder för att skapa kluster och förstå inställningarna finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur du använder en mall för att skapa Apache Hadoop-kluster i HDInsight finns [i Skapa Apache Hadoop-kluster i HDInsight med Azure Resource Manager-mallar](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Vissa egenskaper är hårdkodade i mallen. Ett exempel:
>
> * **Plats**: Östra USA 2
> * **Klusterversion**: 3.6
> * **Antal klusterarbetarenod:** 2
> * **Standardlagringskonto:** en unik sträng
> * **Namn på virtuellt nätverk**: CLUSTERNAME-vnet
> * **Virtuellt nätverksadressutrymme:** 10.0.0.0/16
> * **Undernätsnamn**: undernät1
> * **Undernätsadressintervall:** 10.0.0.0/24
>
> `CLUSTERNAME`ersätts med det klusternamn som du anger när du använder mallen.

1. Välj följande avbildning för att öppna mallen i Azure-portalen. Mallen finns i [Snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)för Azure .

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Välj **Redigera mall**i dialogrutan **Anpassad distribution** .

1. Ändra värdet `Standard_A3` till `Standard_A4_V2`. Välj sedan **Spara**.

1. Fyll i den återstående mallen med följande information:

    |Egenskap |Värde |
    |---|---|
    |Prenumeration|Välj en Azure-prenumeration som används för att skapa HDInsight-klustret, det beroende lagringskontot och det virtuella Azure-nätverket.|
    Resursgrupp|Välj **Skapa ny**och ange ett nytt resursgruppsnamn.|
    |Location|Välj en plats för resursgruppen.|
    |Klusternamn|Ange ett namn på det Hadoop-kluster som ska skapas.|
    |Användarnamn och lösenord för klusterinloggning|Standardanvändarnamnet är **admin**. Ange ett lösenord.|
    |Ssh Användarnamn och lösenord|Standardanvändarnamnet är **sshuser**.  Ange ett lösenord.|

    Välj **Jag godkänner de villkor som anges ovan**.

1. Välj **Köp**. Det tar cirka 20 minuter att skapa ett kluster. När klustret har skapats kan du välja klustret i portalen för att öppna det.

När du har slutfört artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Instruktioner för hur du tar bort ett kluster finns [i Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure-portalen](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Om du vill börja arbeta med det nya HBase-klustret kan du använda procedurerna som finns i [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Ansluta till Apache HBase-klustret med Apache HBase Java RPC-API:er

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell infrastruktur som en virtuell tjänstdator (IaaS) i samma virtuella Azure-nätverk och samma undernät. Instruktioner om hur du skapar en ny virtuell IaaS-dator finns i [Skapa en virtuell dator som kör Windows Server](../../virtual-machines/windows/quick-create-portal.md). När du följer stegen i det här dokumentet måste du använda följande värden för nätverkskonfigurationen:

* **Virtuellt nätverk**: CLUSTERNAME-vnet
* **Undernät**: undernät1

> [!IMPORTANT]  
> Ersätt `CLUSTERNAME` med det namn du använde när du skapade HDInsight-klustret i föregående steg.

Med hjälp av dessa värden placeras den virtuella datorn i samma virtuella nätverk och undernät som HDInsight-klustret. Den här konfigurationen gör det möjligt för dem att kommunicera direkt med varandra. Det finns ett sätt att skapa ett HDInsight-kluster med en tom kantnod. Kantnoden kan användas för att hantera klustret.  Mer information finns i [Använda tomma kantnoder i HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Skaffa fullständigt kvalificerat domännamn

När du använder ett Java-program för att fjärransluta till HBase måste du använda det fullständigt kvalificerade domännamnet (FQDN). För att avgöra detta måste du hämta det anslutningsspecifika DNS-suffixet för HBase-klustret. För att göra det kan du använda någon av följande metoder:

* Använd en webbläsare för att ringa ett [Apache Ambari-samtal:](https://ambari.apache.org/)

    Bläddra till `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Den returnerar en JSON-fil med DNS-suffixen.

* Använd Ambaris webbplats:

    1. Bläddra till `https://CLUSTERNAME.azurehdinsight.net`.
    2. Välj **Värdar** på den övre menyn.

* Använd Curl för att ringa REST-samtal:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Leta reda på posten "host_name" i JSON-data (JavaScript Object Notation) som returneras. Den innehåller FQDN för noderna i klustret. Ett exempel:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Den del av domännamnet som börjar med klusternamnet är DNS-suffixet. Till exempel `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>Verifiera kommunikation i virtuellt nätverk

Om du vill kontrollera att den virtuella datorn kan `ping headnode0.<dns suffix>` kommunicera med HBase-klustret använder du kommandot från den virtuella datorn. Till exempel `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Om du vill använda den här informationen i ett Java-program kan du följa stegen i [Använd Apache Maven för att skapa Java-program som använder Apache HBase med HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) för att skapa ett program. Om du vill att programmet ska ansluta till en fjärr-HBase-server ändrar du filen **hbase-site.xml** i det här exemplet för att använda FQDN för Zookeeper. Ett exempel:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Mer information om namnmatchning i virtuella Azure-nätverk, inklusive hur du använder din egen DNS-server, finns i [DNS (Name Resolution).](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du skapar ett Apache HBase-kluster. Du kan läsa mer här:

* [Kom igång med HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Använd tomma kantnoder i HDInsight](../hdinsight-apps-use-edge-node.md)
* [Konfigurera Apache HBase-replikering i HDInsight](apache-hbase-replication.md)
* [Skapa Apache Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Kom igång med Apache HBase med Apache Hadoop i HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)
