---
title: Introduktion till infångade paket i Azure Network Watcher | Microsoft Docs
description: Den här sidan innehåller en översikt över funktionen Network Watcher packet capture
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4cfbfc4bed5438ed901fca86d8c2939d3860c68e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684162"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introduktion till variabla infångade paket i Azure Network Watcher

Variabla infångade paket Network Watcher kan du skapa packet capture-sessioner för att spåra trafik till och från en virtuell dator. Packet capture bidrar till att diagnostisera nätverk avvikelser både reaktivt och proactivity. Andra användningsområden är att samla in nätverksstatistik, få information om nätverk intrång, felsöka klient-/ serverkommunikation och mycket mer.

Paketfångsten är ett tillägg för virtuell dator som startas via en fjärranslutning via Network Watcher. Den här funktionen förenklar arbetet med att köra ett infångat manuellt på den önskade virtuella datorn, vilket sparar värdefull tid. Paketfångsten kan aktiveras via portal, PowerShell, CLI eller REST API. Ett exempel på hur du kan aktivera infångade paket är med VM-aviseringar. Filter tillhandahålls för avbildningssessionen att se till att du fånga in trafik som du vill övervaka. Filter baseras på 5-tuppel (protokoll, lokala IP-adressen, IP-Fjärradress, lokal port och portar) information. Hämtade data lagras i den lokala disken eller en storage-blob. Det finns en gräns på 10 packet capture sessioner per region per prenumeration. Den här gränsen gäller enbart för sessioner och gäller inte för sparade paket avbilda filer lokalt på den virtuella datorn eller i ett lagringskonto.

> [!IMPORTANT]
> Paketfångsten kräver tillägg för virtuell dator `AzureNetworkWatcherExtension`. Installera tillägget på en Windows-VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Windows](../virtual-machines/windows/extensions-nwa.md) och Linux VM finns [tillägg för virtuell dator i Azure Network Watcher-Agent för Linux](../virtual-machines/linux/extensions-nwa.md).

För att minska den information du avbildning till bara den information som du vill, finns följande alternativ för en packet capture session:

**Avbilda konfiguration**

|Egenskap |Beskrivning|
|---|---|
|**Maximalt antal byte per paket (byte)** | Hur många byte från varje paket som har hämtats, samlas alla byte om det lämnas tomt. Hur många byte från varje paket som har hämtats, samlas alla byte om det lämnas tomt. Om du behöver endast IPv4-rubrik – ange 34 här |
|**Maximalt antal byte per session (byte)** | Sammanlagt antal byte i som fångas när värdet har nåtts sessionen avslutas.|
|**Tidsgräns (sekunder)** | Anger en tidsbegränsning på paketet avbilda session. Standardvärdet är 18000 sekunder eller 5 timmar.|

**Filtrering (valfritt)**

|Egenskap |Beskrivning|
|---|---|
|**Protokoll** | Protokollet som används för att filtrera för paketfångsten. Tillgängliga värden är TCP, UDP och alla.|
|**Lokal IP-adress** | Det här värdet filtrerar infångade att paket där den lokala IP-adressen matchar den här filtervärde.|
|**Lokal port** | Det här värdet filtrerar infångade att paket där den lokala porten som matchar den här filtervärde.|
|**IP-Fjärradress** | Det här värdet filtrerar infångade att paket där fjärr-IP matchar den här filtervärde.|
|**Fjärrport** | Det här värdet filtrerar infångade att paket där fjärrporten matchar den här filtervärde.|

### <a name="next-steps"></a>Nästa steg

Lär dig hur du kan hantera infångade paket via portalen genom att besöka [hantera infångade paket i Azure-portalen](network-watcher-packet-capture-manage-portal.md) eller med PowerShell genom att besöka [hantera Paketfångst med PowerShell](network-watcher-packet-capture-manage-powershell.md).

Lär dig hur du skapar proaktiv infångade paket baserat på varningar för virtuell dator genom att besöka [skapar en avisering utlösta paketfångsten](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













