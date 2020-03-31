---
title: Introduktion till paketinsamling i Azure Network Watcher | Microsoft-dokument
description: Den här sidan innehåller en översikt över hämtningsfunktionen för nätverksbevakningspaket
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1f17463125cead64bd58a2d07e53eee4d3cfcd70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840816"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introduktion till variabel paketfångst i Azure Network Watcher

Med variabel paketsamling för Network Watcher kan du skapa paketsessioner för att spåra trafik till och från en virtuell dator. Paketinsamling hjälper till att diagnostisera nätverksavvikelser både reaktivt och proaktivt. Andra användningsområden inkluderar att samla in nätverksstatistik, få information om nätverksintrång, att felsöka klient-server kommunikation och mycket mer.

Paketfångsten är ett tillägg för virtuella datorer som fjärrstartas via Network Watcher. Den här funktionen underlättar bördan av att köra en paketfångst manuellt på önskad virtuell dator, vilket sparar värdefull tid. Paketinsamling kan utlösas via portalen, PowerShell, CLI eller REST API. Ett exempel på hur paketinsamling kan utlösas är med aviseringar för virtuella datorer. Filter tillhandahålls för insamlingssessionen för att se till att du samlar in trafik som du vill övervaka. Filter baseras på information om 5-tuppel (protokoll, lokal IP-adress, fjärr-IP-adress, lokal port och fjärrport). De infångade data lagras i den lokala disken eller en lagringsblob. Det finns en gräns på 10 paketfångstsessioner per region och prenumeration. Den här gränsen gäller endast för sessioner och gäller inte för de sparade paketfångstfilerna, antingen lokalt på den virtuella datorn eller i ett lagringskonto.

> [!IMPORTANT]
> Paketfångsten kräver `AzureNetworkWatcherExtension`ett tillägg för den virtuella datorn . För att installera tillägget på en Windows VM besök [Azure Network Watcher Agent virtuell dator tillägg för Windows](../virtual-machines/windows/extensions-nwa.md) och för Linux VM besök Azure Network [Watcher Agent virtuell dator tillägg för Linux](../virtual-machines/linux/extensions-nwa.md).

För att minska den information du samlar in till endast den information du vill ha, är följande alternativ tillgängliga för en paketinsamlingssession:

**Fånga konfiguration**

|Egenskap|Beskrivning|
|---|---|
|**Maximala byte per paket (byte)** | Antalet byte från varje paket som fångas in, alla byte fångas om de lämnas tomma. Antalet byte från varje paket som fångas in, alla byte fångas om de lämnas tomma. Om du bara behöver IPv4-huvudet – ange 34 här |
|**Maximala byte per session (byte)** | Totalt antal byte som fångas in när värdet har uppnåtts avslutas sessionen.|
|**Tidsgräns (sekunder)** | Anger en tidsbegränsning för paketfångsten. Standardvärdet är 18000 sekunder eller 5 timmar.|

**Filtrering (valfritt)**

|Egenskap|Beskrivning|
|---|---|
|**Protokollet** | Protokollet som ska filtreras för paketfångsten. De tillgängliga värdena är TCP, UDP och Alla.|
|**Lokal IP-adress** | Det här värdet filtrerar paketfångsten till paket där den lokala IP-adressen matchar det här filtervärdet.|
|**Lokal port** | Det här värdet filtrerar paketfångsten till paket där den lokala porten matchar det här filtervärdet.|
|**Fjärr-IP-adress** | Det här värdet filtrerar paketfångsten till paket där fjärr-IP matchar det här filtervärdet.|
|**Fjärrport** | Det här värdet filtrerar paketfångsten till paket där fjärrporten matchar det här filtervärdet.|

### <a name="next-steps"></a>Nästa steg

Lär dig hur du kan hantera paketinsamlingar via portalen genom att besöka [Hantera paketfångst i Azure-portalen](network-watcher-packet-capture-manage-portal.md) eller med PowerShell genom att besöka [Hantera pakethämtning med PowerShell](network-watcher-packet-capture-manage-powershell.md).

Lär dig hur du skapar proaktiva paketinsamlingar baserat på aviseringar för virtuella datorer genom att besöka [Skapa en aviseringsutlöst paketfångst](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













