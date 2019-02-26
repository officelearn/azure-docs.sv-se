---
title: Verifiera VPN-dataflöde till ett Microsoft Azure-nätverk | Microsoft Docs
description: Syftet med det här dokumentet är att en användare verifiera nätverksgenomflöde från sina lokala resurser till en Azure virtuell dator.
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
ms.date: 06/15/2018
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 819415712d8e605825957aa602fc99dcf6902d82
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821669"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Hur du validerar VPN-dataflöde till ett virtuellt nätverk

En anslutning för VPN-gateway låter dig upprätta säkra, mellan lokala anslutning mellan ditt virtuella nätverk i Azure och din lokala IT-infrastruktur.

Den här artikeln visar hur du validerar dataflöde i nätverket från lokala resurser till en Azure-dator (VM). Det ger också felsökningsinformation.

>[!NOTE]
>Den här artikeln är avsedd för att diagnostisera och åtgärda vanliga problem. Om det inte går att lösa problemet med hjälp av följande information, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Översikt

VPN-gatewayanslutning omfattar följande komponenter:

- Den lokala VPN-enhet (visa en lista över [validerade VPN-enheter)](vpn-gateway-about-vpn-devices.md#devicetable).
- Offentliga Internet
- Azure VPN-gateway
- Azure VM

Följande diagram visar logiska anslutning av ett lokalt nätverk till ett Azure-nätverk via VPN.

![Logisk anslutning av kundens nätverk till MSFT-nätverk med VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Beräkna det högsta förväntade ingående/utgående trafik

1.  Fastställa krav för ditt programs baslinje dataflöde.
2.  Kontrollera dataflödesbegränsningar din Azure VPN gateway. Hjälp finns i avsnittet ”Gateway SKU: er” i [om VPN Gateway](vpn-gateway-about-vpngateways.md#gwsku).
3.  Fastställa den [dataflödesvägledning för Azure VM](../virtual-machines/virtual-machines-windows-sizes.md) för VM-storlek.
4.  Kontrollera din Internet-leverantör (ISP)-bandbredd.
5.  Beräkna din förväntade dataflödet - minsta bandbredd på (virtuell dator, Gateway, ISP) * 0,8.

Om ditt beräknade dataflöde inte uppfyller krav på ditt programs baslinje dataflöde som du behöver öka bandbredden för den resurs som du har identifierat flaskhalsar. Om du vill ändra storlek på Azure VPN Gateway, se [ändrar en gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Om du vill ändra storlek på en virtuell dator, se [ändra storlek på en virtuell dator](../virtual-machines/virtual-machines-windows-resize-vm.md). Om du inte har förväntade internetbandbredd, kan du även vill kontakta Internetleverantören.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Verifiera dataflöde i nätverket med hjälp av prestandaverktyg

Den här verifieringen ska utföras vid låg belastning, som VPN-tunnel dataflöde mättnad under testningen inte ger korrekta resultat.

Verktyget som vi använder för det här testet är iPerf fungerar på både Windows och Linux och den har både klienten och servern lägen. Den är begränsad till 3 Gbit/s för Windows virtuella datorer.

Det här verktyget utför inte alla läs-/ skrivåtgärder till disk. Den genererar endast självgenererat TCP-trafik överförs till den andra. Den genererade statistik baserat på experimentering som mäter bandbredden som är tillgänglig mellan klienten och servern noder. När du testar mellan två noder kan fungerar en som servern och den andra som en klient. När det här testet är slutfört, rekommenderar vi att du återställer roller för att testa båda ladda upp och hämta dataflöde på båda noderna.

### <a name="download-iperf"></a>Ladda ned iPerf
Ladda ned [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Mer information finns i [iPerf dokumentation](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Tredjepartsprodukter som den här artikeln beskriver tillverkas av företag som är oberoende av Microsoft. Microsoft lämnar inga garantier, uttryckliga eller på annat sätt, prestanda och pålitlighet.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Kör iPerf (iperf3.exe)
1. Aktivera en NSG/ACL-regel som tillåter trafik (för offentliga IP-adress testning på virtuella Azure-datorer).

2. Skapa ett brandväggsundantag för port 5001 på båda noderna.

    **Windows:** Kör följande kommando som administratör:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Om du vill ta bort regeln när testningen är klar, kör du följande kommando:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
     
    **Azure Linux:**  Azure Linux-avbildningar har Tillåtande brandväggar. Om det finns ett program som lyssnar på en port, tillåts trafik via. Anpassade avbildningar som är säkrade kanske måste uttryckligen öppnade portar. Vanliga Linux OS-lager brandväggar inkluderar `iptables`, `ufw`, eller `firewalld`.

3. Ändra till katalogen där iperf3.exe ska extraheras på server-noden. Sedan kör iPerf i serverläge och ställas in att lyssna på port 5001 som följande kommandon:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Ändra till den katalog där iperf verktyget extraheras och kör sedan följande kommando på klient-nod:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Klienten att trafik på port 5001 till servern i 30 sekunder. Flaggan ”-P” innebär det att vi använder 32 samtidiga anslutningar till noden.

    Följande skärmbild visar utdata från det här exemplet:

    ![Resultat](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (VALFRITT) För att bevara testning resultaten, kör du följande kommando:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. När du har slutfört föregående steg, kör du samma steg med de roller som har återförts, så att noden kommer nu att klienten och vice versa.

## <a name="address-slow-file-copy-issues"></a>Lösa långsam fil kopia problem
Du uppleva långsamma filen hantera när du använder Windows Explorer eller dra och släppa via en RDP-session. Det här problemet är vanligtvis på grund av en eller båda av följande faktorer:

- Filen kopiera program, till exempel Windows Explorer och ansluter via RDP, Använd inte flera trådar när du kopierar filer. För bättre prestanda, använda ett flertrådiga fil kopia program som [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) att kopiera filer med hjälp av 16 eller 32 trådar. Klicka för att ändra den filkopiering i Richcopy sifferkombination **åtgärd** > **kopiera alternativ** > **filkopieringen**.<br><br>
![Problem med långsamma Files-kopia](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Inte tillräckligt med VM-disk Läs/Skriv hastighet. Mer information finns i [felsökning för Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Den lokala enheten externt riktade gränssnitt
Om den lokala VPN-enheten mot Internet-IP-adress som ingår i den [lokalt nätverk](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definition i Azure kan uppstå oförmåga att sätta upp kopplar från VPN, sporadiska eller prestandaproblem.

## <a name="checking-latency"></a>Kontroll av svarstid
Använd tracert att spåra till Microsoft Azure Edge-enhet för att avgöra om det finns några fördröjningar som överskrider 100 ms mellan hopp.

Från det lokala nätverket, kör *tracert* till VIP för Azure-Gateway eller virtuell dator. När du ser bara * returneras du vet du har nått gränsen för Azure. När DNS-namn som innehåller ”MSN” returneras visas vet du att du har nått inom Microsofts stamnätverk.<br><br>
![Kontroll av svarstid](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Nästa steg
Mer information eller hjälp finns i följande länkar:

- [Optimera nätverkets dataflöde för Azure-datorer](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
