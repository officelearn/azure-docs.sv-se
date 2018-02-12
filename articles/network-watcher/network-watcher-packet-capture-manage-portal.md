---
title: "Hantera paket insamlingar med Nätverksbevakaren Azure - Azure-portalen | Microsoft Docs"
description: "Den här sidan förklarar hur du hanterar funktionen paket avbildning i Nätverksbevakaren med hjälp av Azure portal"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 40414afbfcd456353b4290585ccd9d594fbf55dd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2018
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Hantera paket insamlingar med Azure Nätverksbevakaren med hjälp av portalen

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Nätverket Watcher paketinsamling kan du skapa avbildning sessioner för att spåra trafik till och från en virtuell dator. Filter har angetts för hämtningens så du fångar upp trafiken som du vill använda. Det hjälper dig för att diagnostisera nätverk avvikelser reaktivt och proaktivt paketinsamling. Andra användningsområden omfattar att samla in nätverksstatistik får information om nätverket intrång felsöka klient-/ serverkommunikation och mycket mer. Genom att via fjärranslutning utlösa paket insamlingar, underlättar den här funktionen för att köra en paketinsamling manuellt och på den önskade datorn, vilket sparar värdefull tid.

Den här artikeln tar dig igenom de olika administrativa uppgifter som är tillgängliga för paketinsamling.

- [**Starta en paketinsamling**](#start-a-packet-capture)
- [**Stoppa en paketinsamling**](#stop-a-packet-capture)
- [**Ta bort en paketinsamling**](#delete-a-packet-capture)
- [**Hämta en paketinsamling**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

- En instans av Nätverksbevakaren i den region som du vill skapa en paketinsamling
- En virtuell dator med filnamnstillägget paket avbilda aktiverad.

> [!IMPORTANT]
> Paketinsamling kräver ett tillägg för virtuell dator `AzureNetworkWatcherExtension`. Installera tillägget på en Windows VM finns [tillägg för virtuell dator i Azure Network Watcher Agent för Windows](../virtual-machines/windows/extensions-nwa.md) och för Linux VM besöka [tillägg för virtuell dator i Azure Network Watcher Agent för Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Tillägget av paketet avbilda agent via portalen

För att installera paketet avbilda VM-agenten via portalen, navigerar du till den virtuella datorn, klickar du på **tillägg** > **Lägg till** och Sök efter **nätverk Watcher agenten för Windows**

![Visa Agent][agent]

## <a name="packet-capture-overview"></a>Översikt över avbildning av paket

Navigera till den [Azure-portalen](https://portal.azure.com) och på **nätverk** > **Nätverksbevakaren** > **paket avbilda**

Översiktssidan visas en lista över alla paket fångar som finns oavsett tillståndet.

> [!NOTE]
> Paketinsamling kräver följande anslutningsbarhet.
> * Utgående anslutning till lagringskontot via port 443.
> * Inkommande och utgående anslutning till 169.254.169.254
> * Inkommande och utgående anslutning till 168.63.129.16

![paketet avbilda översiktsskärm][1]

## <a name="start-a-packet-capture"></a>Starta en paketinsamling

Klicka på **Lägg till** att skapa en paketinsamling.

De egenskaper som kan definieras på en paketinsamling är:

**Huvudinställningarna**

- **Prenumerationen** -värdet är den prenumeration som används, krävs en instans av nätverksbevakaren i varje prenumeration.
- **Resursgruppen** -resursgruppen för den virtuella datorn som bearbetas.
- **Rikta virtuella** -den virtuella datorn som kör paketinsamling
- **Avbilda paketnamn** -värdet är namnet på paketinsamling.

**Samla in konfiguration**

- **Lokal filsökväg** -lokal sökväg på den virtuella datorn där paketinsamling sparas (endast giltigt när **[fil]** har valts). Du måste ange en giltig sökväg. Om du använder en Linux-dator sökvägen måste börja med / var / avbildas.
- **Lagringskontot** -anger om paketinsamling sparas i ett lagringskonto.
- **Filen** -avgör om en paketinsamling sparas lokalt på den virtuella datorn.
- **Storage-konton** – markerat lagringskonto för att spara paketinsamling i. Standardplatsen är name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription https://{storage konto-id} /resourcegroups/ {resursgruppens name}/providers/microsoft.compute/virtualmachines/{virtual datornamn} / {åå} / {MM} / {DD} / packetcapture_ {HH}_{MM}_{SS} _ {XXX} CAP. (Endast aktiverad om **lagring** har valts)
- **Lokal filsökväg** -lokal sökväg på en virtuell dator för att spara paketinsamling. (Endast aktiverad om **filen** har valts). En giltig sökväg måste anges. Sökvägen måste börja med för en virtuell Linux-dator, */var/samlar in*.
- **Maximalt antal byte per paket** - antalet byte från varje paket som har hämtats avbildas alla byte om värdet är tomt.
- **Maximalt antal byte per session** – Totalt antal byte som har hämtats när värdet är nådd paket avbilda stoppas.
- **Tidsgräns (sekunder)** -anger en tidsgräns för paketinsamling att stoppa. Standardvärdet är 18000 sekunder.

> [!NOTE]
> Premium-lagringskonton stöds inte för närvarande för lagring av paket avbildas.

**Filtrering (valfritt)**

- **Protokollet** -protokollet för att filtrera för paket-avbildning. Tillgängliga värden är TCP, UDP och alla.
- **Lokal IP-adress** -värdet filtrerar paketinsamling att paket där den lokala IP-adressen matchar den här filtervärdet.
- **Lokal port** -värdet filtrerar paketinsamling att paket där den lokala porten matchar filtret värdet.
- **IP-Fjärradress** -värdet filtrerar paketinsamling att paket där fjärranslutna IP-Adressen matchar den här filtervärdet.
- **Fjärrport** -värdet filtrerar paketinsamling att paket där fjärrporten matchar filtret värdet.

> [!NOTE]
> Värden för porten och IP-adress kan vara ett värde, värdeintervallet eller en uppsättning. (det vill säga 80 1024 för port) Du kan definiera så många filter som du vill.

När värdena fylls, klickar du på **OK** att skapa paketinsamling.

![Skapa en paketinsamling][2]

När tidsgränsen på paketinsamling har upphört att gälla paketinsamling stoppas och kan granskas. Du kan också manuellt stoppa paket insamlingar sessionerna.

## <a name="delete-a-packet-capture"></a>Ta bort en paketinsamling

I vyn paket avbildning, klickar du på den **snabbmenyn** (...) eller högerklicka och klicka på **ta bort** att stoppa paketinsamling

![Ta bort en paketinsamling][3]

> [!NOTE]
> Om du tar bort en paketinsamling tar inte bort filen i lagringskontot.

Du uppmanas att bekräfta att du vill ta bort paketinsamling, klickar du på **Ja**

![Bekräftelse][4]

## <a name="stop-a-packet-capture"></a>Stoppa en paketinsamling

I vyn paket avbildning, klickar du på den **snabbmenyn** (...) eller högerklicka och klicka på **stoppa** att stoppa paketinsamling

## <a name="download-a-packet-capture"></a>Hämta en paketinsamling

När paketet avbildningssessionen är klar överförs filen avbildning till blob-lagring eller till en lokal fil på den virtuella datorn. Lagringsplatsen för paketinsamling har definierats vid skapandet av sessionen. Ett enkelt verktyg för att komma åt dessa avbilda filer som sparats till ett lagringskonto är Microsoft Azure Lagringsutforskaren, som kan hämtas här: http://storageexplorer.com/

Om ett storage-konto anges sparas paket avbilda filer till ett lagringskonto på följande plats:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Nästa steg

Lär dig att automatisera insamlingar paket med virtuella aviseringar genom att visa [skapar en avisering utlösta paketinsamling](network-watcher-alert-triggered-packet-capture.md)

Hitta om vissa trafik tillåts i eller utanför den virtuella datorn genom att besöka [Kontrollera Kontrollera IP-flöde](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













