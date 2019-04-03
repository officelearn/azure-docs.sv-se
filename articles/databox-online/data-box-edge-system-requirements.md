---
title: Systemkrav för Microsoft Azure Data Box Edge | Microsoft Docs
description: Läs mer om programvara och nätverkskrav för din Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: 720f4433c1e7b0d271e5729236f439c92a84687e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884660"
---
# <a name="azure-data-box-edge-system-requirements"></a>Azure Data Box Edge-systemkrav

Den här artikeln beskriver viktiga systemkraven för din Microsoft Azure Data Box Edge-lösning och för klienter som ansluter till Azure Data Box Edge. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box-Edge. Du kan referera tillbaka till den här informationen som behövs under distributionen och efterföljande driften.

Systemkraven för Data Box Edge är:

- **Programvarukrav för värdar** -beskriver plattformarna som stöds, webbläsare för den lokala konfigurationen Användargränssnittet, SMB-klienter och eventuella ytterligare krav för klienter som har åtkomst till enheten.
- **Nätverkskrav för enheten** – innehåller information om alla nätverkskrav för driften av den fysiska enheten.

## <a name="supported-os-for-clients-connected-to-device"></a>Operativsystem som stöds för klienter som är anslutna till enhet

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protokoll som stöds för klienter som ansluter till enheten

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Stöds storage-konton

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Webbläsare som stöds för lokala webbgränssnittet

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Port nätverkskrav

### <a name="port-requirements-for-data-box-edge"></a>Krav på nätverksportar för Data Box Edge

I följande tabell visas de portar som måste öppnas i brandväggen att tillåta SMB, i molnet eller hanteringstrafik. I den här tabellen *i* eller *inkommande* avser riktningen från vilka inkommande begäranden för klientåtkomst till din enhet. *Ut* eller *utgående* refererar till den riktning som din Data Box Edge-enhet skickar data externt, utöver distributionen, till exempel utgående till internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Krav på nätverksportar för IoT Edge

Azure IoT Edge kan utgående kommunikation från en lokal Edge-enhet till Azure-molnet med IoT Hub-protokoll som stöds. Inkommande kommunikation är endast krävs för specifika scenarier där Azure IoT Hub måste push-teknik meddelanden för Azure IoT Edge-enhet (till exempel molnet till Device messaging).

Använd följande tabell för portkonfiguration för de servrar som är värd för Azure IoT Edge-körningen:

| Porten Nej. | In eller ut | Port omfång | Krävs | Riktlinjer |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Utdata       | WAN        | Ja      | Utgående öppen för IoT Edge etablering. Den här konfigurationen krävs när du använder manuella skript eller Azure IoT Device Provisioning-tjänsten (DPS).|

Fullständig information går du till [brandvägg och port konfigurationsregler för IoT Edge-distribution](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönster för att filtrera det inkommande och utgående trafik. Din Data Box Edge-enhet och tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönster som associeras med dessa program kan användas för att konfigurera brandväggens regler. Det är viktigt att förstå att URL-mönster som associeras med dessa program kan ändra. De här ändringarna kräver nätverksadministratören kan övervaka och uppdatera brandväggsregler för din Data Box Edge som och när det behövs.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på Data Box Edge fasta IP-adresser, liberally i de flesta fall. Du kan dock använda den här informationen för att ställa in avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - Enheten (källa) IP-adresser ska alltid vara inställd till alla moln-aktiverat nätverksgränssnitt.
> - Målets IP-adresser ska vara inställd på [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>URL-mönster för gateway-funktionen

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-mönster för compute-funktion

| URL-mönster                      | Komponenten eller funktioner                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | Microsoft behållarregister (krävs)               |   |
| https://\*.azurecr.io                     | Personliga och tredje parts behållarregister (valfritt) |   |
| https://\*.azure-devices.net              | IoT Hub-åtkomst (krävs)                             |   |

### <a name="url-patterns-for-azure-government"></a>URL-mönster för Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internetbandbredd

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Nästa steg

- [Distribuera din Azure Data Box-Edge](data-box-edge-deploy-prep.md)
