---
title: Felsöka problem med virtuella nätverksinstallationer i Azure | Microsoft-dokument
description: Lär dig hur du felsöker problem med den virtuella nätverksinstallationen i Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: b998043bc7d896989590ac21db5f309a81cc02bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056838"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problem med virtuella nätverksinstallationer i Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan uppleva problem med vm- eller VPN-anslutning och fel när du använder en tredje part Network Virtual Appliance (NVA) i Microsoft Azure. Den här artikeln innehåller grundläggande steg som hjälper dig att validera grundläggande Azure-plattformskrav för NVA-konfigurationer.

Teknisk support för nva:er från tredje part och deras integrering med Azure-plattformen tillhandahålls av NVA-leverantören.

> [!NOTE]
> Om du har ett anslutnings- eller routningsproblem som involverar en NVA bör du [kontakta leverantören av NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) direkt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Checklista för felsökning med NVA-leverantör

- Programuppdateringar för PROGRAMVARA FÖR NVA
- Inställningar och funktioner för tjänstkonto
- Användardefinierade vägar (UDRs) på virtuella nätverksundernät som dirigerar trafik till NVA
- UDRs på virtuella nätverk undernät som dirigerar trafik från NVA
- Routningstabeller och regler inom NVA (till exempel från NIC1 till NIC2)
- Spåra NVA-nätverkskort för att verifiera mottagande och sändning av nätverkstrafik
- När du använder en standard-SKU och offentliga IPs måste det finnas en NSG skapad och en explicit regel för att tillåta att trafiken dirigeras till NVA.

## <a name="basic-troubleshooting-steps"></a>Grundläggande felsökningssteg

- Kontrollera den grundläggande konfigurationen
- Kontrollera NVA-prestanda
- Avancerad felsökning av nätverk

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Kontrollera minimikraven för konfiguration för NVA:er på Azure

Varje NVA har grundläggande konfigurationskrav för att fungera korrekt på Azure. I följande avsnitt beskrivs stegen för att verifiera dessa grundläggande konfigurationer. Kontakta leverantören [av NVA om](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)du vill ha mer information.

**Kontrollera om IP-vidarebefordran är aktiverad på NVA**

Använda Azure-portalen

1. Leta reda på NVA-resursen i [Azure-portalen](https://portal.azure.com), välj Nätverk och välj sedan nätverksgränssnittet.
2. Välj IP-konfiguration på sidan Nätverksgränssnitt.
3. Kontrollera att IP-vidarebefordran är aktiverad.

Använd PowerShell

1. Öppna PowerShell och logga sedan in på ditt Azure-konto.
2. Kör följande kommando (ersätt de parenteserade värdena med din information):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Kontrollera egenskapen **EnableIPForwarding.**
4. Om IP-vidarebefordran inte är aktiverad kör du följande kommandon för att aktivera den:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Sök efter NSG när du använder Standard SKU Pubilc IP** När du använder en standard-SKU och offentliga IPs måste det finnas en NSG skapad och en explicit regel för att tillåta trafik till NVA.

**Kontrollera om trafiken kan dirigeras till NVA**

1. Öppna **Network Watcher**på [Azure Portal](https://portal.azure.com)och välj **Nästa hopp**.
2. Ange en virtuell dator som är konfigurerad för att omdirigera trafiken till NVA och en mål-IP-adress där nästa hopp ska visas. 
3. Om NVA inte visas som **nästa hopp**kontrollerar och uppdaterar du Azure-vägtabellerna.

**Kontrollera om trafiken kan nå NVA**

1. Öppna **Network Watcher**i [Azure Portal](https://portal.azure.com)och välj sedan **IP-flödesverifiering**. 
2. Ange den virtuella datorn och IP-adressen för NVA och kontrollera sedan om trafiken blockeras av någon nätverkssäkerhetsgrupper (NSG).
3. Om det finns en NSG-regel som blockerar trafiken, lokalisera NSG i **effektiva säkerhetsregler** och uppdatera den så att trafiken kan passera. Kör sedan **IP Flow Verify** igen och använd Connection **felsöka** för att testa TCP-kommunikation från virtuell dator till din interna eller externa IP-adress.

**Kontrollera om NVA och virtuella datorer lyssnar efter förväntad trafik**

1. Anslut till NVA med RDP eller SSH och kör sedan följande kommando:

    För Windows:

        netstat -an

    För Linux:

        netstat -an | grep -i listen
2. Om du inte ser TCP-porten som används av NVA-programvaran som visas i resultaten måste du konfigurera programmet på NVA och VM för att lyssna och svara på trafik som når dessa portar. [Kontakta NVA-leverantören för hjälp efter behov](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Kontrollera NVA-prestanda

### <a name="validate-vm-cpu"></a>Validera VM-processor

Om CPU-användningen kommer nära 100 procent kan det uppstå problem som påverkar nätverkspaketdropparna. Din virtuella dator rapporterar genomsnittlig PROCESSOR för en viss tidsperiod i Azure-portalen. Under en CPU-topp undersöker du vilken process på gästdatorn som orsakar den höga processorn och mildrar den om möjligt. Du kan också behöva ändra storlek på den virtuella datorn till en större SKU-storlek eller, för skalningsuppsättning för virtuella datorer, öka antalet instanser eller ange automatisk skalning på CPU-användning. Om något av dessa problem finns [i NVA-leverantören för att få hjälp](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), efter behov.

### <a name="validate-vm-network-statistics"></a>Validera vm-nätverksstatistik

Om vm-nätverket använder toppar eller visar perioder med hög användning kan du också behöva öka SKU-storleken på den virtuella datorn för att få högre dataflödesfunktioner. Du kan också distribuera om den virtuella datorn genom att aktivera accelererat nätverk. Om du vill kontrollera om NVA stöder funktionen Accelerated Networking [kontaktar du NVA-leverantören för hjälp](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)om det behövs.

## <a name="advanced-network-administrator-troubleshooting"></a>Avancerad felsökning av nätverksadministratör

### <a name="capture-network-trace"></a>Fånga nätverksspårning
Samla in en samtidig nätverksspårning på källdatorns virtuella dator, NVA och måldatorn medan du kör **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** eller **Nmap**och stoppa sedan spårningen.

1. Om du vill fånga en samtidig nätverksspårning kör du följande kommando:

   **För Windows**

   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

   **För Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Använd **PsPing** eller **Nmap** från källdatorn till `PsPing 10.0.0.4:80` måldatorn (till exempel: eller `Nmap -p 80 10.0.0.4`).
3. Öppna nätverksspårningen från den virtuella måldatorn med hjälp av [Network Monitor](https://www.microsoft.com/download/details.aspx?id=4865) eller tcpdump. Använd ett visningsfilter för IP-adressen för käll-VM som `IPv4.address==10.0.0.4 (Windows netmon)` du `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` körde **PsPing** eller **Nmap** från, till exempel eller (Linux).

### <a name="analyze-traces"></a>Analysera spår

Om du inte ser paketen som är inkommande till den första vm-spårningen, är det troligt att en NSG- eller UDR-inkring eller nva-routningstabellerna är felaktiga.

Om du ser att paket, men inget svar, kommer in kan du behöva åtgärda problem med ett program på den virtuella datorn eller med brandväggen. Om något av dessa problem finns [i NVA-leverantören för att få hjälp efter behov](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).