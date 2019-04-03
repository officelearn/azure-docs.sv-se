---
title: Systemkrav för Microsoft Azure Data Box Gateway | Microsoft Docs
description: Läs mer om programvara och nätverkskrav för din Azure Data Box-Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: cac451634bfa357784f9fd3d3a24e06ef3a4ee19
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881774"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Azure Data Box Gateway-systemkrav

Den här artikeln beskriver viktiga systemkraven för din Microsoft Azure Data Box Gateway-lösning och för klienter som ansluter till Azure Data Box-Gateway. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box-Gateway och sedan refererar tillbaka till det som behövs under distributionen och efterföljande driften.

Systemkrav för den virtuella enheten Data Box-Gateway är:

- **Programvarukrav för värdar** -beskriver plattformarna som stöds, webbläsare för den lokala konfigurationen Användargränssnittet, SMB-klienter och ytterligare krav för värdar som ansluter till enheten.
- **Nätverkskrav för enheten** – innehåller information om alla nätverkskrav för driften av den virtuella enheten.


## <a name="specifications-for-the-virtual-device"></a>Specifikationer för den virtuella enheten

Underliggande värdsystemet för Data Box-Gateway är att dedikera följande resurser för att etablera din virtuella enhet:

| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Virtuella processorer (kärnor)   | Minst 4 |
| Minne  | Minst 8 GB|
| Tillgänglighet|Enkel nod|
| Diskar| OS-disk: 250 GB <br> Datadisk: minst 2 TB, tunt allokerad och måste backas upp av SSD-enheter|
| Nätverksgränssnitt|Minst 1 virtuellt nätverksgränssnitt|


## <a name="supported-os-for-clients-connected-to-device"></a>Operativsystem som stöds för klienter som är anslutna till enhet

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protokoll som stöds för klienter som ansluter till enheten

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Stöds virtualiseringsplattformar för enhet

| **Operativsystem/plattform**  |**Versioner**   |**Anteckningar**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |VMware-verktyg stöds inte.         |


## <a name="supported-storage-accounts"></a>Stöds storage-konton

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Webbläsare som stöds för lokala webbgränssnittet

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Port nätverkskrav

I följande tabell visas de portar som måste öppnas i brandväggen att tillåta SMB, i molnet eller hanteringstrafik. I den här tabellen *i* eller *inkommande* avser riktningen från vilka inkommande begäranden för klientåtkomst till din enhet. *Ut* eller *utgående* refererar till den riktning som din Data Box-Gateway-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönster för att filtrera det inkommande och utgående trafik. Din Data Box-Gateway-enhet och Data Box-Gateway-tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönster som associeras med dessa program kan användas för att konfigurera brandväggens regler. Det är viktigt att förstå att URL-mönster som associeras med dessa program kan ändra. Detta kräver i sin tur att nätverksadministratören kan övervaka och uppdatera brandväggsregler för din Data Box-Gateway som och när det behövs.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på Data Box Gateway fasta IP-adresser, liberally i de flesta fall. Du kan dock använda den här informationen för att ställa in avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - Enheten (källa) IP-adresser ska alltid vara inställd till alla moln-aktiverat nätverksgränssnitt.
> - Målets IP-adresser ska vara inställd på [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>URL-mönster för Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internetbandbredd

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Nästa steg

* [Distribuera din Azure Data Box-Gateway](data-box-gateway-deploy-prep.md)

