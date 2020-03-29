---
title: InvalidNetworkConfigurationErrorCode-fel - Azure HDInsight
description: Olika orsaker till misslyckade klusterskapanden med InvalidNetworkConfigurationErrorCode i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720392"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Klusterskapande misslyckas med InvalidNetworkConfigurationErrorCode i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

Om du ser `InvalidNetworkConfigurationErrorCode` felkod med beskrivningen "Virtual Network-konfigurationen är inte kompatibel med HDInsight-krav", indikerar det vanligtvis ett problem med den [virtuella nätverkskonfigurationen](../hdinsight-plan-virtual-network-deployment.md) för klustret. Följ nedanstående avsnitt för att lösa problemet, beroende på resten av felbeskrivningen.

## <a name="hostname-resolution-failed"></a>"HostName-lösningen misslyckades"

### <a name="issue"></a>Problem

Felbeskrivningen innehåller "HostName-lösning misslyckades".

### <a name="cause"></a>Orsak

Det här felet pekar på ett problem med anpassad DNS-konfiguration. DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till Azures rekursiva resolvers för att matcha värdnamn i det virtuella nätverket (se [Namnmatchning i virtuella nätverk](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) för mer information). Åtkomst till Azures rekursiva resolvers tillhandahålls via den virtuella IP 168.63.129.16. Den här IP-adressen är endast tillgänglig från virtuella Azure-datorer. Så det fungerar inte om du använder en OnPrem DNS-server, eller din DNS-server är en Azure VM, som inte är en del av klustrets virtuella nätverk.

### <a name="resolution"></a>Lösning

1. Ssh i den virtuella datorn som är en `hostname -f`del av klustret och kör kommandot . Detta returnerar värdens fullständigt kvalificerade domännamn (kallas `<host_fqdn>` i instruktionerna nedan).

1. Kör sedan kommandot `nslookup <host_fqdn>` (till `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`exempel ). Om det här kommandot matchar namnet till en IP-adress betyder det att DNS-servern fungerar korrekt. I det här fallet tar du upp ett supportärende med HDInsight så undersöker vi ditt problem. I supportäreläggaren ska du inkludera felsökningsstegen som du har kört. Detta kommer att hjälpa oss att lösa problemet snabbare.

1. Om kommandot ovan inte returnerar en IP-adress `nslookup <host_fqdn> 168.63.129.16` körs `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`du (till exempel ). Om det här kommandot kan lösa IP-adressen betyder det att antingen dns-servern inte vidarebefordrar frågan till Azures DNS, eller så är det inte en virtuell dator som ingår i samma virtuella nätverk som klustret.

1. Om du inte har en virtuell Azure-dator som kan fungera som en anpassad DNS-server i klustrets virtuella nätverk måste du lägga till den här först. Skapa en virtuell dator i det virtuella nätverket, som kommer att konfigureras som DNS-vidarebefordrare.

1. När du har distribuerat en virtuell dator i det virtuella nätverket konfigurerar du DNS-vidarebefordringsreglerna på den här virtuella datorn. Vidarebefordra alla iDNS-namnmatchningsbegäranden till 168.63.129.16 och resten till DNS-servern. [Här](../hdinsight-plan-virtual-network-deployment.md) är ett exempel på den här inställningen för en anpassad DNS-server.

1. Lägg till IP-adressen för den här virtuella datorn som första DNS-post för DNS-konfigurationen för virtuellt nätverk.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Det gick inte att ansluta till Azure Storage-konto"

### <a name="issue"></a>Problem

Felbeskrivningen innehåller "Det gick inte att ansluta till Azure Storage Account" eller "Det gick inte att ansluta till Azure SQL".

### <a name="cause"></a>Orsak

Azure Storage och SQL har inte fasta IP-adresser, så vi måste tillåta utgående anslutningar till alla IP-adresser för att tillåta åtkomst till dessa tjänster. De exakta matchningsstegen beror på om du har konfigurerat en NSG (Network Security Group) eller ANVÄNDARDEFINIERADE REGLER (UDR). Mer information om dessa konfigurationer finns i avsnittet om [hur du kontrollerar nätverkstrafik med HDInsight med nätverkssäkerhetsgrupper och användardefinierade vägar.](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)

### <a name="resolution"></a>Lösning

* Om klustret använder en [NSG -grupp (Network Security Group).](../../virtual-network/virtual-network-vnet-plan-design-arm.md)

    Gå till Azure-portalen och identifiera NSG som är associerad med undernätet där klustret distribueras. I avsnittet **Utgående säkerhetsregler** tillåter du utgående åtkomst till internet utan begränsning (observera att ett mindre **prioritetsnummer** här betyder högre prioritet). I avsnittet **undernät** bekräftar du också om den här NSG:n tillämpas på klustrets undernät.

* Om klustret använder en [användardefinierad VÄG (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Gå till Azure-portalen och identifiera den vägtabell som är associerad med undernätet där klustret distribueras. När du har hittat vägtabellen för undernätet bör du kontrollera **flödesavsnittet** i det.

    Om det finns definierade vägar kontrollerar du att det finns vägar för IP-adresser för den region där klustret distribuerades och **att NextHopType** för varje väg är **Internet**. Det bör finnas en väg som definieras för varje obligatorisk IP-adress som dokumenteras i ovannämnda artikel.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Virtuell nätverkskonfiguration är inte kompatibel med HDInsight-krav"

### <a name="issue"></a>Problem

Felbeskrivningar innehåller meddelanden som liknar följande:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Orsak

Sannolikt ett problem med den anpassade DNS-installationen.

### <a name="resolution"></a>Lösning

Kontrollera att 168.63.129.16 finns i den anpassade DNS-kedjan. DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till Azures rekursiva resolvers för att matcha värdnamn i det virtuella nätverket. Mer information finns [i Namnmatchning i virtuella nätverk](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Åtkomst till Azures rekursiva resolvers tillhandahålls via den virtuella IP 168.63.129.16.

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kör följande kommando:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Du bör se något liknande följande:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Baserat på resultatet - välj något av följande steg för att följa:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 finns inte med i denna lista

**Alternativ 1**  
Lägg till 168.63.129.16 som den första anpassade DNS för det virtuella nätverket med hjälp av stegen som beskrivs i [Planera ett virtuellt nätverk för Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Dessa steg gäller endast om din anpassade DNS-server körs på Linux.

**Alternativ 2**  
Distribuera en virtuell DNS-server för det virtuella nätverket. Detta innebär följande steg:

* Skapa en virtuell dator i det virtuella nätverket, som kommer att konfigureras som DNS-vidarebefordrare (det kan vara en Linux eller windows VM).
* Konfigurera DNS-vidarebefordringsregler på den här virtuella datorn (vidarebefordra alla iDNS-namnmatchningsbegäranden till 168.63.129.16 och resten till DNS-servern).
* Lägg till IP-adressen för den här virtuella datorn som första DNS-post för DNS-konfiguration i virtuellt nätverk.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 finns med i listan

I det här fallet kan du skapa ett supportärende med HDInsight, så undersöker vi ditt problem. Inkludera resultatet av nedanstående kommandon i supportärendet. Detta kommer att hjälpa oss att undersöka och lösa problemet snabbare.

Redigera och kör sedan följande från en ssh-session på huvudnoden:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
