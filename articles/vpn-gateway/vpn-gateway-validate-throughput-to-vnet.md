---
title: Verifiera VPN-genomströmning till ett Microsoft Azure-virtuellt nätverk | Microsoft Docs
description: Syftet med det här dokumentet är att en användare verifiera genomströmningen i nätverket från sina lokala resurser till en virtuell Azure-dator.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2017
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: cad7719eb077d7aca9c1db5741a5fe1e0ca910a2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Hur du verifierar VPN-genomströmning till ett virtuellt nätverk

En VPN-gateway-anslutningen kan du upprätta säkra, mellan lokala anslutningen mellan ditt virtuella nätverk i Azure och ditt lokala IT-infrastruktur.

Den här artikeln visar hur du verifierar dataflödet i nätverket från de lokala resurserna till en Azure virtuell dator (VM). Det ger också felsökningsinformation.

>[!NOTE]
>Den här artikeln är avsedd för att diagnostisera och lösa vanliga problem. Om du inte kan lösa problemet med hjälp av följande information [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Översikt

VPN-gatewayanslutning omfattar följande komponenter:

- Lokala VPN-enhet (visa en lista över [verifiera VPN-enheter)](vpn-gateway-about-vpn-devices.md#devicetable).
- Offentliga Internet
- Azure VPN-gateway
- Azure VM

Följande diagram visar logiska anslutningen till ett lokalt nätverk till Azure-nätverk via VPN.

![Logiskt anslutning av kundens nätverk till MSFT nätverk via VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Beräkna det högsta förväntade ingång-/ utgång

1.  Fastställa kraven för ditt program baslinjen genomflöde.
2.  Bestämma din Azure VPN gateway genomflöde. Mer hjälp finns i avsnittet ”sammanställda genomflöde av SKU- och VPN-typ” av [planering och design för VPN-Gateway](vpn-gateway-plan-design.md).
3.  Ta reda på [Azure VM genomströmning vägledning](../virtual-machines/virtual-machines-windows-sizes.md) för VM-storlek.
4.  Kontrollera din Internet-leverantör (ISP) bandbredd.
5.  Beräkna din förväntade genomströmning - minsta bandbredd (VM-Gateway ISP) * 0,8.

Om din beräknade genomströmning inte uppfyller kraven för ditt program baslinjen dataflöde, måste du öka bandbredden för den resurs som du identifierade prioriteten. Om du vill ändra storlek på en Azure VPN-Gateway, se [ändra en gateway-SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Om du vill ändra storlek på en virtuell dator, se [ändra storlek på en virtuell dator](../virtual-machines/virtual-machines-windows-resize-vm.md). Om du inte har den förväntade internetbandbredd, kan du också vill kontakta Leverantören.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Verifiera dataflödet i nätverket med hjälp av prestandaverktygen

Den här verifieringen ska utföras vid låg belastning, som VPN-tunnel genomströmning mättnad under testningen inte ger korrekta resultat.

Verktyget vi använder för det här testet är iPerf som fungerar i både Windows och Linux och har både klienten och servern lägen. Det är begränsat till 3 Gbit/s för virtuella Windows-datorer.

Det här verktyget inte att utföra några åtgärder för läsning och skrivning till disk. Den genererar endast självgenererat TCP-trafik från en slutpunkt till den andra. Den genererade statistik baserat på experiment som mäter bandbredden som är tillgänglig mellan klienten och servern noder. När du testar mellan två noder som en fungerar som servern och den andra som en klient. När det här testet har slutförts, rekommenderar vi att du återställer roller för att testa både ladda upp och ladda ned dataflödet på båda noderna.

### <a name="download-iperf"></a>Hämta iPerf
Hämta [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Mer information finns i [iPerf dokumentationen](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Tredjepartsprodukter som den här artikeln beskrivs tillverkas oberoende av Microsoft. Microsoft lämnar inga garantier, uttryckliga eller på annat sätt, prestanda och pålitlighet.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Kör iPerf (iperf3.exe)
1. Aktivera en NSG/ACL-regel som tillåter trafik (för offentliga IP-adressen testa på Azure VM).

2. Aktivera ett brandväggsundantag för port 5001 på båda noderna.

    **Windows:** kör följande kommando som administratör:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Om du vill ta bort regeln när testningen är klar, köra det här kommandot:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Azure Linux:** Azure Linux bilder har Tillåtande brandväggar. Om det finns ett program som lyssnar på en port, tillåts trafik via. Anpassade avbildningar som är skyddade behöva portar som öppnats explicit. Vanliga Linux OS-lager brandväggar inkluderar `iptables`, `ufw`, eller `firewalld`.

3. Ändra till katalogen där iperf3.exe extraherade på server-noden. Sedan kör iPerf i serverläge och ställas in att lyssna på porten 5001 som följande kommandon:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Ändra till katalogen där iperf verktyget extraheras och kör sedan följande kommando på noden klienten:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Klienten att trafik på port 5001 till servern i 30 sekunder. Flaggan '-P-värde som anger att vi använder 32 samtidiga anslutningar till noden.

    Följande skärmbild visar utdata från det här exemplet:

    ![Resultat](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (VALFRITT) För att bevara testresultaten, kör du kommandot:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. När du har slutfört föregående steg, kör du samma steg med de roller som återställs, så att servernoden kommer nu att klienten och vice versa.

## <a name="address-slow-file-copy-issues"></a>Åtgärda problem med långsam kopia
Det kan uppstå långsam filen kopieringen när Utforskaren eller dra och släppa via en RDP-session. Det här problemet beror normalt på en eller båda av följande faktorer:

- Kopiera program, till exempel Utforskaren och RDP-filen kan inte använda flera trådar när du kopierar filer. För bättre prestanda, Använd ett flertrådat filen kopiera program som [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) att kopiera filer med hjälp av 16 eller 32 trådar. Så här ändrar du numret tråd filkopiering i Richcopy **åtgärd** > **kopiera alternativ** > **filkopieringen**.<br><br>
![Problem med långsam filens kopia](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Otillräcklig VM disk läsning och skrivning hastighet. Mer information finns i [felsökning av Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Lokal enhet externa Internetriktade gränssnittet
Om lokala VPN-enhet mot Internet IP-adress som ingår i den [lokala nätverket](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definition i Azure kan uppstå när det gäller att sätta upp kopplar från VPN, sporadiska eller prestandaproblem.

## <a name="checking-latency"></a>Kontroll av svarstid
Använd tracert att spåra till Microsoft Azure-enhet för att avgöra om det är fördröjningar mer än 100 millisekunder, mellan hopp.

Kör från det lokala nätverket *tracert* till VIP Azure Gateway eller VM. När du ser endast * returneras genom att du vet att du har nått gränsen för Azure. När DNS-namn som innehåller ”MSN” returneras visas vet du Microsoft-stamnät har uppnåtts.<br><br>
![Kontroll av svarstid](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Nästa steg
Mer information eller hjälp finns på följande länkar:

- [Optimera dataflödet i nätverket för virtuella Azure-datorer](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
