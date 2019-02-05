---
title: Felsökning av nätverksproblem virtuell installation i Azure | Microsoft Docs
description: Lär dig mer om att felsöka nätverksproblem för virtuell installation i Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 0d5b345936f6c931f4210e6dc50f94544a52f571
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700578"
---
#  <a name="network-virtual-appliance-issues-in-azure"></a>Virtuell installation nätverksproblem i Azure

Det kan förekomma VM eller VPN-anslutningsproblem och fel när du använder en tredje part virtuell nätverksinstallation (NVA) i Microsoft Azure. Den här artikeln innehåller grundläggande steg för att hjälpa dig att validera grundläggande krav för Azure-plattformen för NVA-konfigurationer.

Teknisk support för tredje parts nva: er och deras integrering med Azure-plattformen tillhandahålls av NVA-leverantören. 

> [!NOTE]
> Om du har en anslutning eller routningsproblem som inbegriper en NVA, bör du [Kontakta leverantören av NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) direkt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Checklista för felsökning med NVA-leverantör

- Programuppdateringar för NVA VM-programvara
- Tjänstkontot installation och funktioner
- Användardefinierade vägar (Udr) på undernät för virtuella nätverk som dirigerar trafiken till NVA
- Udr: er på undernät för virtuella nätverk som dirigerar trafik från NVA
- Routning tabeller och regler i NVA (till exempel från NIC1 till NIC2)
- Spårning på NVA-nätverkskort för att verifiera ta emot och skicka nätverkstrafik
- När du använder en Standard-SKU och offentliga IP-adresser måste det finnas en NSG som skapats och en regel för explicit för att tillåta trafik ska dirigeras till NVA.

## <a name="basic-troubleshooting-steps"></a>Grundläggande åtgärder för felsökning

- Kontrollera den grundläggande konfigurationen
- Kontrollera NVA-prestanda
- Avancerade nätverksfelsökning

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Kontrollera de lägsta konfigurationskrav för nva: er på Azure

Varje NVA har krav för grundläggande konfiguration ska fungera korrekt i Azure. Följande avsnitt innehåller steg för att kontrollera dessa grundläggande konfigurationer. Mer information [Kontakta leverantören av NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Kontrollera om IP-vidarebefordring är aktiverat på NVA**

Använda Azure-portalen

1.  Leta upp den NVA-resursen i den [Azure-portalen](https://portal.azure.com), Välj nätverk och välj sedan ett nätverksgränssnitt.
2.  Välj IP-konfiguration på sidan nätverk gränssnitt.
3.  Kontrollera att IP-vidarebefordring är aktiverat.

Använd PowerShell

1. Öppna PowerShell och logga sedan in på ditt Azure-konto.
2. Kör följande kommando (Ersätt inom hakparenteser värden med din information):

        Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>  

3. Kontrollera den **EnableIPForwarding** egenskapen.
 
4. Om IP-vidarebefordring inte är aktiverad, kör du följande kommandon för att aktivera den:

          $nic2 = Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
          $nic2.EnableIPForwarding = 1
          Set-AzureRmNetworkInterface -NetworkInterface $nic2
          Execute: $nic2 #and check for an expected output:
          EnableIPForwarding   : True
          NetworkSecurityGroup : null

**Sök efter NSG när du använder Standard-SKU offentlig IP** när du använder en Standard-SKU och offentliga IP-adresser måste det finnas en NSG som skapats och en regel för explicit för att tillåta trafik till NVA.

**Kontrollera om trafiken kan dirigeras till en NVA**

1. På [Azure-portalen](https://portal.azure.com)öppnar **Network Watcher**väljer **nästa hopp**.
2. Ange en virtuell dator som är konfigurerad för att dirigera trafiken till NVA och målets IP-adress som du vill visa nästa hopp. 
3. Om en NVA inte visas som den **nästa hopp**, kontrollera och uppdatera Azure-routningstabeller.

**Kontrollera om trafik kan nå en NVA**

1.  I [Azure-portalen](https://portal.azure.com)öppnar **Network Watcher**, och välj sedan **IP-Flow verifiera**. 
2.  Ange den virtuella datorn och IP-adressen för NVA och kontrollera om trafik blockeras av eventuella nätverkssäkerhetsgrupper (NSG).
3.  Om det finns en NSG-regel som blockerar trafiken, hitta Nätverkssäkerhetsgruppen i **effektiva** regler och sedan uppdatera den för att tillåta trafik passerar. Kör sedan **IP-Flow verifiera** igen och Använd **anslutningsfelsökning** att testa TCP-kommunikation från VM till din interna eller externa IP-adress.

**Kontrollera om virtuella datorer och NVA lyssnar på förväntade trafik**

1.  Ansluta till NVA med hjälp av RDP eller SSH och kör sedan följande kommando:

    För Windows:

        netstat -an

    För Linux:

        netstat -an | grep -i listen
2.  Om du inte ser den TCP-port som används av NVA-programvaran som anges i resultaten måste du konfigurera programmet på NVA och virtuell dator för att lyssna och svara på trafik som når dessa portar. [Kontakta leverantören NVA för att få hjälp för](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Kontrollera NVA-prestanda

### <a name="validate-vm-cpu"></a>Verifiera Virtuella CPU

Om processoranvändningen hämtar nära 100 procent, kan det uppstå problem som påverkar nätverk paket släpper. VM-rapporter Genomsnittlig CPU för ett specifikt tidsintervall i Azure-portalen. Under en CPU-topp undersöka vilken process på gästen VM som orsakar hög CPU och lösa det, om möjligt. Du kan också behöva ändra storlek på den virtuella datorn till en större SKU-storlek eller för virtual machine scale Sets, öka instansantalet eller inställd på automatisk skalning på CPU-användning. För någon av dessa frågor [kontakta NVA-leverantören för att få hjälp](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), efter behov.

### <a name="validate-vm-network-statistics"></a>Verifiera Virtuella datornätverket statistik 

Om det Virtuella datornätverket använder toppar eller visar perioder med hög användning, du kan också behöva öka SKU-storleken på den virtuella datorn för att få högre kapacitet. Du kan också distribuera om den virtuella datorn genom att använda Accelererat nätverk aktiverat. Kontrollera om NVA har stöd för Accelererat nätverk funktionen [kontakta NVA-leverantören för att få hjälp](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), efter behov.

## <a name="advanced-network-administrator-troubleshooting"></a>Avancerade nätverksadministratör felsökning

### <a name="capture-network-trace"></a>Fånga nätverksspår
Avbilda en samtidig nätverksspårning på den Virtuella källdatorn och NVA mål VM medan du kör **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** eller **Nmap**, och sedan stoppa spårningen.

1. Om du vill samla in en samtidig nätverksspårning, kör du följande kommando:

    För Windows:

        netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

    För Linux:

        sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Använd **PsPing** eller **Nmap** från den Virtuella källdatorn till mål VM (till exempel: `PsPing 10.0.0.4:80` eller `Nmap -p 80 10.0.0.4`).

3. Öppna nätverksspårningen från målet VM med hjälp av [Network Monitor](https://www.microsoft.com/download/details.aspx?id=4865) eller tcpdump. Tillämpa ett visningsfilter för IP-Adressen för den Virtuella källdatorn som du körde **PsPing** eller **Nmap** från, till exempel `IPv4.address==10.0.0.4 (Windows netmon)` eller `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analysera spår

Om du inte ser inkommande paket i serverdelens VM spårningen det troligen en NSG eller UDR-störningar eller NVA routningstabeller är felaktiga.

Om du ser att paket, men inget svar, kommer in kan du behöva åtgärda problem med ett program på den virtuella datorn eller med brandväggen. För någon av dessa frågor [kontakta NVA-leverantören för att få hjälp för](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

