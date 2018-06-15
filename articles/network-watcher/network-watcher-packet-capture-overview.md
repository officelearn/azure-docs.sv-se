---
title: Introduktion till paketinsamling i Azure-Nätverksbevakaren | Microsoft Docs
description: Den här sidan innehåller en översikt över Nätverksbevakaren paket avbilda kapaciteten
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 152cc8fb61aa6115c7b5863e4d798db9e7aa5b7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864296"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introduktion till variabeln paketinsamling i Azure Nätverksbevakaren

Nätverket Watcher variabeln paketinsamling kan du skapa paket capture-sessioner för att spåra trafik till och från en virtuell dator. Paketet avbilda bidrar till att diagnostisera nätverk avvikelser båda reaktivt och proactivity. Andra användningsområden omfattar att samla in nätverksstatistik får information om nätverket intrång felsöka klient-/ serverkommunikation och mycket mer.

Paketinsamling är ett tillägg för virtuell dator som startas från en fjärrdator via Nätverksbevakaren. Den här funktionen underlättar för att köra en paketinsamling manuellt på den önskade virtuella datorn, vilket sparar värdefull tid. Paketinsamling kan aktiveras via portalen, PowerShell, CLI eller REST API. Ett exempel på hur paketinsamling kan utlösas är med virtuella aviseringar. Filter har angetts för hämtningens så du fånga in trafik som du vill övervaka. Filter baseras på 5-tuppel (protokoll, lokal IP-adress, fjärranslutna IP-adress, lokal port och Fjärrport) information. Fångade data lagras i den lokala disken eller en lagringsblob. Det finns en begränsning på 10 paket avbilda sessioner per region per prenumeration. Den här gränsen gäller bara för sessioner och gäller inte för sparade paket avbilda filer lokalt på den virtuella datorn eller i ett lagringskonto.

> [!IMPORTANT]
> Paketinsamling kräver ett tillägg för virtuell dator `AzureNetworkWatcherExtension`. Installera tillägget på en Windows VM finns [tillägg för virtuell dator i Azure Network Watcher Agent för Windows](../virtual-machines/windows/extensions-nwa.md) och för Linux VM besöka [tillägg för virtuell dator i Azure Network Watcher Agent för Linux](../virtual-machines/linux/extensions-nwa.md).

För att minska den information du fånga in till bara den information som du vill, finns följande alternativ för en paket-avbildningssessionen:

**Samla in konfiguration**

|Egenskap|Beskrivning|
|---|---|
|**Maximalt antal byte per paket (byte)** | Antalet byte från varje paket som har hämtats avbildas alla byte om värdet är tomt. Antalet byte från varje paket som har hämtats avbildas alla byte om värdet är tomt. Om du behöver endast IPv4-huvud – ange 34 här |
|**Maximalt antal byte per session (byte)** | Sammanlagt antal byte som fångas när värdet är nådd sessionen avslutas.|
|**Tidsgräns (sekunder)** | Anger en tidsbegränsning på paketet avbilda session. Standardvärdet är 18000 sekunder eller 5 timmar.|

**Filtrering (valfritt)**

|Egenskap|Beskrivning|
|---|---|
|**Protokoll** | Protokoll att filtrera för paket-avbildning. Tillgängliga värden är TCP, UDP och alla.|
|**Lokal IP-adress** | Det här värdet filtrerar paketinsamling att paket där den lokala IP-adressen matchar den här filtervärdet.|
|**Lokal port** | Det här värdet filtrerar paketinsamling att paket där den lokala porten matchar filtret värdet.|
|**Fjärranslutna IP-adress** | Det här värdet filtrerar paketinsamling att paket där fjärranslutna IP-Adressen matchar den här filtervärdet.|
|**Fjärrport** | Det här värdet filtrerar paketinsamling att paket där fjärrporten matchar filtret värdet.|

### <a name="next-steps"></a>Nästa steg

Lär dig hur du kan hantera paket insamlingar via portalen genom att besöka [hantera paketinsamling i Azure portal](network-watcher-packet-capture-manage-portal.md) eller med PowerShell genom att besöka [hantera paket avbilda med PowerShell](network-watcher-packet-capture-manage-powershell.md).

Lär dig hur du skapar proaktiv paket insamlingar baserat på virtuella aviseringar genom att besöka [skapar en avisering utlösta paketinsamling](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













