---
title: InvalidNetworkConfigurationErrorCode från att skapa kluster i Azure HDInsight
description: Olika orsaker till att kluster skapas med InvalidNetworkConfigurationErrorCode i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 1f60ef5e267b0ee3233f9adb9dc9e8ccd3ac1c65
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734780"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Det går inte att skapa kluster med InvalidNetworkConfigurationErrorCode i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

Om du ser felkod `InvalidNetworkConfigurationErrorCode` med beskrivningen "Virtual Network-konfigurationen är inte kompatibel med HDInsight-krav", tyder det vanligt vis på ett problem med konfigurationen av det [virtuella nätverket](../hdinsight-plan-virtual-network-deployment.md) för klustret. Följ avsnitten nedan för att lösa problemet, baserat på resten av fel beskrivningen.

## <a name="hostname-resolution-failed"></a>"Det gick inte att matcha värdnamn"

### <a name="issue"></a>Problem

Fel beskrivningen innehåller "matchning av värdnamn" misslyckades ".

### <a name="cause"></a>Orsak

Felet pekar på ett problem med den anpassade DNS-konfigurationen. DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till Azures rekursiva matchare för att matcha värdnamn i det virtuella nätverket (se [namn matchning i virtuella nätverk](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) för mer information). Åtkomst till Azures rekursiva matchare tillhandahålls via den virtuella IP-168.63.129.16. Den här IP-adressen är bara tillgänglig från virtuella Azure-datorer. Det fungerar därför inte om du använder en OnPrem DNS-server, eller om DNS-servern är en virtuell Azure-dator, som inte ingår i klustrets vNet.

### <a name="resolution"></a>Lösning

1. Använd SSH i den virtuella datorn som är en del av klustret och kör kommandot `hostname -f`. Detta returnerar värdens fullständigt kvalificerade domän namn (enligt `<host_fqdn>` anvisningarna nedan).

1. Kör sedan kommandot `nslookup <host_fqdn>` (till `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`exempel). Om det här kommandot matchar namnet på en IP-adress innebär det att din DNS-server fungerar som den ska. I det här fallet kan du generera ett support ärende med HDInsight och vi kommer att undersöka problemet. I support ärendet inkluderar du de fel söknings steg som du utförde. Detta hjälper oss att lösa problemet snabbare.

1. Om kommandot ovan inte returnerar en IP-adress ska du köra `nslookup <host_fqdn> 168.63.129.16` (till `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`exempel). Om det här kommandot kan matcha IP-adressen innebär det att antingen DNS-servern inte vidarebefordrar frågan till Azures DNS, eller om den inte är en virtuell dator som är en del av samma vNet som klustret.

1. Om du inte har en virtuell Azure-dator som kan fungera som en anpassad DNS-server i klustrets vNet måste du först lägga till den. Skapa en virtuell dator i det virtuella nätverket, som kommer att konfigureras som DNS-vidarebefordrare.

1. När du har distribuerat en virtuell dator i ditt vNet konfigurerar du reglerna för vidarebefordring av DNS på den här virtuella datorn. Vidarebefordra alla namn matchnings begär Anden från IDN till 168.63.129.16 och resten av DNS-servern. [Här](../hdinsight-plan-virtual-network-deployment.md) är ett exempel på den här installationen för en anpassad DNS-server.

1. Lägg till IP-adressen för den virtuella datorn som första DNS-post för Virtual Network DNS-konfiguration.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Det gick inte att ansluta till Azure Storage kontot"

### <a name="issue"></a>Problem

Fel beskrivningen innehåller "Det gick inte att ansluta till Azure Storage kontot" eller "Det gick inte att ansluta till Azure SQL".

### <a name="cause"></a>Orsak

Azure Storage och SQL har inte fasta IP-adresser, så vi måste tillåta utgående anslutningar till alla IP-adresser för att tillåta åtkomst till dessa tjänster. De exakta lösnings stegen beror på om du har konfigurerat en nätverks säkerhets grupp (NSG) eller användardefinierade regler (UDR). Mer information om dessa konfigurationer finns i avsnittet om att [kontrol lera nätverks trafik med HDInsight med nätverks säkerhets grupper och användardefinierade vägar](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) .

### <a name="resolution"></a>Lösning

* Om klustret använder en [nätverks säkerhets grupp (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Gå till Azure Portal och identifiera den NSG som är associerad med under nätet där klustret distribueras. I avsnittet **utgående säkerhets regler** tillåter du utgående åtkomst till Internet utan begränsning (Observera att ett lägre **prioritets** nummer innebär högre prioritet). I avsnittet **undernät** bekräftar du även om den här NSG tillämpas på klustrets undernät.

* Om ditt kluster använder en [användardefinierad routning (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Gå till Azure Portal och identifiera routningstabellen som är associerad med under nätet där klustret distribueras. När du har hittat routningstabellen för under nätet, kontrollerar du avsnittet **vägar** i den.

    Om det finns definierade vägar kontrollerar du att det finns vägar för IP-adresser för den region där klustret distribuerades och att **NextHopType** för varje väg är **Internet**. Det bör finnas en väg definierad för varje obligatorisk IP-adress som dokumenteras i den tidigare artikeln.

---

### <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
