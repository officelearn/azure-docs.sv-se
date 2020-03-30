---
title: Systemkrav för Microsoft Azure Data Box Gateway| Microsoft-dokument
description: Lär dig mer om programvaru- och nätverkskraven för din Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: e8932097bcdef782b1a551d386c2872e02d8abfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260194"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Systemkrav för Azure Data Box Gateway

I den här artikeln beskrivs de viktiga systemkraven för din Microsoft Azure Data Box Gateway-lösning och för klienter som ansluter till Azure Data Box Gateway. Vi rekommenderar att du granskar informationen noggrant innan du distribuerar databoxgatewayen och sedan refererar till den efter behov under distributionen och efterföljande åtgärd.

Systemkraven för den virtuella enheten Data Box Gateway omfattar:

- **Programvarukrav för värdar** - beskriver plattformar som stöds, webbläsare för det lokala konfigurationsgränssnittet, SMB-klienter och eventuella ytterligare krav för de värdar som ansluter till enheten.
- **Nätverkskrav för enheten** - ger information om eventuella nätverkskrav för driften av den virtuella enheten.


## <a name="specifications-for-the-virtual-device"></a>Specifikationer för den virtuella enheten

Det underliggande värdsystemet för Data Box Gateway kan avsätta följande resurser för att etablera din virtuella enhet:

| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Virtuella processorer (kärnor)   | Minst 4 |
| Minne  | Minst 8 GB|
| Tillgänglighet|Enkel nod|
| Diskar| OS-disk: 250 GB <br> Datadisk: minst 2 TB, tunt allokerad och måste backas upp av SSD-enheter|
| Nätverksgränssnitt|Minst 1 virtuellt nätverksgränssnitt|


## <a name="supported-os-for-clients-connected-to-device"></a>Operativsystem som stöds för klienter som är anslutna till enheten

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protokoll som stöds för klienter som ansluter till enheten

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Virtualiseringsplattformar som stöds för enheten

| **Operativsystem/plattform**  |**Versioner**   |**Obs!**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |VMware-verktyg stöds inte.         |


## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Webbläsare som stöds för lokalt webbgränssnitt

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Krav på nätverksport

I följande tabell visas de portar som måste öppnas i brandväggen för att tillåta SMB-, moln- eller hanteringstrafik. I den här tabellen refererar *i* eller *inkommande* till den riktning från vilken inkommande klient begär åtkomst till enheten. *Utgående* eller *utgående* refererar till i vilken riktning databoxgatewayenheten skickar data externt, utöver distributionen: till exempel utgående till Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönstren för att filtrera inkommande och utgående trafik. Data Box Gateway-enheten och tjänsten Data Box Gateway är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönstren som är associerade med dessa program kan användas för att konfigurera brandväggsregler. Det är viktigt att förstå att url-mönstren som är associerade med dessa program kan ändras. Detta kräver i sin tur nätverksadministratören att övervaka och uppdatera brandväggsregler för din Data Box Gateway om och när det behövs.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på fast IP-adresser för databoxgateway, frikostigt i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - Enhets-IPs-adresser (källa) ska alltid ställas in på alla molnaktiverade nätverksgränssnitt.
> - Mål-IP-adresser ska ställas in [på AZURE datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>URL-mönster för Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internet bandbredd

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Nästa steg

* [Distribuera din Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

