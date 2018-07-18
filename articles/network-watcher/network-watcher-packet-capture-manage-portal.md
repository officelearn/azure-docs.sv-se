---
title: Hantera infångade paket med Azure Network Watcher - Azure-portalen | Microsoft Docs
description: Den här sidan förklarar hur du hanterar paket avbildningsfunktionen i Network Watcher med hjälp av Azure portal
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 18e5f8eda51f8834f6346eef3d7ad31bc556290a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090205"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Hantera infångade paket med Azure Network Watcher med hjälp av portalen

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST-API](network-watcher-packet-capture-manage-rest.md)

Network Watcher-infångade kan du skapa avbildning sessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för avbildningssessionen att se till att du fångar upp trafiken som du vill. Det hjälper dig för att diagnostisera nätverk avvikelser både reaktivt och proaktivt infångade paket. Andra användningsområden är att samla in nätverksstatistik, få information om nätverk intrång, felsöka klient-/ serverkommunikation och mycket mer. Genom för att utlösa infångade paket via en fjärranslutning, förenklar med den här funktionen ansvaret för att köra ett infångat manuellt och på den önskade datorn, vilket sparar värdefull tid.

Den här artikeln tar dig igenom de olika administrativa uppgifter som är tillgängliga för infångade paket.

- [**Starta ett infångat paket**](#start-a-packet-capture)
- [**Stoppa ett infångat paket**](#stop-a-packet-capture)
- [**Ta bort ett infångat paket**](#delete-a-packet-capture)
- [**Ladda ned ett infångat paket**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

- En instans av Network Watcher i regionen som du vill skapa ett infångat paket
- En virtuell dator med packet capture tillägget aktiverat.

> [!IMPORTANT]
> Paketfångsten kräver tillägg för virtuell dator `AzureNetworkWatcherExtension`. Installera tillägget på en Windows-VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Windows](../virtual-machines/windows/extensions-nwa.md) och Linux VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Packet Capture agenttillägg via portalen

Om du vill installera packet capture VM-agenten via portalen, navigera till den virtuella datorn, klicka på **tillägg** > **Lägg till** och Sök efter **Network Watcher-Agent för Windows**

![Agent-vy][agent]

## <a name="packet-capture-overview"></a>Översikt över paket

Navigera till den [Azure-portalen](https://portal.azure.com) och klicka på **nätverk** > **Network Watcher** > **Paketfångst**

På översiktssidan visas en lista över alla paket fångar som finns oavsett status.

> [!NOTE]
> Paketfångsten kräver följande conectivity.
> * Utgående anslutning till lagringskonto via port 443.
> * Inkommande och utgående anslutning till 169.254.169.254
> * Inkommande och utgående anslutning till 168.63.129.16

![Packet capture översiktsskärmen][1]

## <a name="start-a-packet-capture"></a>Starta ett infångat paket

Klicka på **Lägg till** att skapa ett infångat paket.

De egenskaper som kan definieras på ett infångat paket finns:

**Viktigaste inställningar**

- **Prenumeration** – det här värdet är den prenumeration som används, krävs en instans av network watcher i varje prenumeration.
- **Resursgrupp** -resursgruppen för den virtuella datorn som bearbetas.
- **Rikta VM** -den virtuella datorn som kör paketfångsten
- **Namn på paketfångst** – det här värdet är namnet på infångandet paket.

**Avbilda konfiguration**

- **Lokal filsökväg** -lokal sökväg på den virtuella datorn där paketfångsten har sparats (endast giltigt när **[file]** har valts). Du måste ange en giltig sökväg. Om du använder en Linux-dator, sökvägen måste börja med / var / samlar in.
- **Storage-konto** – avgör om paketfångsten sparas i ett lagringskonto.
- **Filen** – avgör om ett infångat paket har sparats lokalt på den virtuella datorn.
- **Storage-konton** – valt lagringskonto att spara infångade i. Standardplatsen är name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription https://{storage konto-id} /resourcegroups/ {resursgruppnamn för name}/providers/microsoft.compute/virtualmachines/{virtual för datorn} / {dd} / {MM} / {DD} / packetcapture_ {HH}_{MM}_{SS} _ {XXX} .cap. (Endast aktiverad om **Storage** har valts)
- **Lokal filsökväg** – den lokala sökvägen på en virtuell dator för att spara paketfångsten. (Endast aktiverad om **filen** har valts). En giltig sökväg måste anges. Sökvägen måste börja med för en Linux-dator, */var/samlar in*.
- **Maximalt antal byte per paket** - antalet byte från varje paket som har hämtats, samlas alla byte om det lämnas tomt.
- **Maximalt antal byte per session** : Totalt antal byte som har hämtats, när värdet har nåtts packet capture stoppas.
- **Tidsgräns (sekunder)** – anger en tidsgräns att stoppa paketfångsten. Standardvärdet är 18000 sekunder.

> [!NOTE]
> Premium storage-konton stöds för närvarande inte för lagring av paket samlar in.

**Filtrering (valfritt)**

- **Protokollet** -protokollet för att filtrera för paketfångsten. Tillgängliga värden är TCP, UDP och alla.
- **Lokal IP-adress** – det här värdet filtrerar infångade att paket där den lokala IP-adressen matchar den här filtervärde.
- **Lokal port** – det här värdet filtrerar infångade att paket där den lokala porten som matchar den här filtervärde.
- **IP-Fjärradress** – det här värdet filtrerar infångade att paket där fjärr-IP matchar den här filtervärde.
- **Fjärrport** – det här värdet filtrerar infångade att paket där fjärrporten matchar den här filtervärde.

> [!NOTE]
> Värden för port och IP-adress kan vara ett enda värde, intervallet för värden eller en uppsättning. (det vill säga 80 – 1024 för port) Du kan definiera så många filter som du vill ha.

När värdena fylls, klickar du på **OK** att skapa paketfångsten.

![Skapa ett infångat paket][2]

När tidsgränsen på paketfångsten har överskridits stoppar paketfångsten och kan granskas. Du kan också manuellt stoppa paket insamlingar sessioner.

## <a name="delete-a-packet-capture"></a>Ta bort ett infångat paket

I vyn packet capture klickar du på den **snabbmenyn** (...) eller högerklickar och klickar på **ta bort** att stoppa paketfångsten

![ta bort ett infångat paket][3]

> [!NOTE]
> Filen i lagringskontot tas inte bort om du tar bort ett infångat paket.

Du uppmanas att bekräfta att du vill ta bort paketfångsten, klicka på **Ja**

![Bekräftelse][4]

## <a name="stop-a-packet-capture"></a>Stoppa ett infångat paket

I vyn packet capture klickar du på den **snabbmenyn** (...) eller högerklickar och klickar på **stoppa** att stoppa paketfångsten

## <a name="download-a-packet-capture"></a>Ladda ned ett infångat paket

När packet capture sessionen är klar, har avbilda filen överförts till blob-lagring eller till en lokal fil på den virtuella datorn. Lagringsplatsen för paketfångsten har definierats vid skapandet av sessionen. Ett praktiskt verktyg för att komma åt dessa avbilda filer som sparats i ett lagringskonto är Microsoft Azure Storage Explorer, som kan hämtas här:  http://storageexplorer.com/

Om ett lagringskonto anges sparas packet capture filer till ett lagringskonto på följande plats:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du automatiserar infångade paket med virtuella datorer aviseringar genom att visa [skapar en avisering utlösta paketfångsten](network-watcher-alert-triggered-packet-capture.md)

Hitta om vissa trafik är tillåten i eller utanför din virtuella dator genom att besöka [Kontrollera Kontrollera IP-flöde](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













