---
title: Systemkrav för Microsoft Azure Data Box Edge | Microsoft Docs
description: Läs mer om programvara och nätverkskrav för din Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/14/2019
ms.author: alkohli
ms.openlocfilehash: 60c4b22fb34a66a0ff68db26030be0e0ea3c0066
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470256"
---
# <a name="azure-data-box-edge-system-requirements-preview"></a>Systemkrav för Azure Data Box Edge (förhandsversion)

Den här artikeln beskriver viktiga systemkraven för din Microsoft Azure Data Box Edge-lösning och för klienter som ansluter till Azure Data Box Edge. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box-Edge. Du kan referera tillbaka till den här informationen som behövs under distributionen och efterföljande driften.

Systemkraven för Data Box Edge är:

- **Programvarukrav för värdar** -beskriver plattformarna som stöds, webbläsare för den lokala konfigurationen Användargränssnittet, SMB-klienter och eventuella ytterligare krav för klienter som har åtkomst till enheten.
- **Nätverkskrav för enheten** – innehåller information om alla nätverkskrav för driften av den fysiska enheten.

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Läs [användningsvillkoren för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen.

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

## <a name="port-configuration-for-data-box-edge"></a>Portkonfiguration för Data Box Edge

I följande tabell visas de portar som måste öppnas i brandväggen att tillåta SMB, i molnet eller hanteringstrafik. I den här tabellen *i* eller *inkommande* avser riktningen från vilka inkommande begäranden för klientåtkomst till din enhet. *Ut* eller *utgående* refererar till den riktning som din Data Box Edge-enhet skickar data externt, utöver distributionen, till exempel utgående till internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="port-configuration-for-iot-edge"></a>Portkonfiguration för IoT Edge

Azure IoT Edge kan utgående kommunikation från en lokal Edge-enhet till Azure-molnet med IoT Hub-protokoll som stöds. Inkommande kommunikation är endast krävs för specifika scenarier där Azure IoT Hub måste push-teknik meddelanden för Azure IoT Edge-enhet (till exempel molnet till Device messaging).

Använd följande tabell för portkonfiguration för de servrar som är värd för Azure IoT Edge-körningen:

| Porten Nej. | In eller ut | Port omfång | Krävs | Riktlinjer |
|----------|-----------|------------|----------|----------|
| TCP 5671 (AMQP)| Utdata       | WAN        | Ja      | Standard kommunikationsprotokoll för IoT Edge. Måste vara öppna om Azure IoT Edge inte är konfigurerad för andra protokoll som stöds eller AMQP är det önskade kommunikationsprotokollet. <br>5672 för AMQP stöds inte av IoT Edge. <br>Blockera den här porten när protokoll som stöds av Azure IoT Edge använder en annan IoT-hubb. |
| TCP 443 (HTTPS)| Utdata       | WAN        | Ja      | Utgående öppen för IoT Edge etablering. Om du har en transparent gateway med lövenheter som kan skicka metodbegäranden. I det här fallet behöver inte port 443 är öppen för externa nätverk att ansluta till IoT Hub eller tillhandahåller tjänster för IoT Hub via Azure IoT Edge. Därför kan den inkommande regeln begränsas till endast öppna inkommande från det interna nätverket. |
| TCP 5671 (AMQP) | I        |            | Nej       | Inkommande anslutningar ska blockeras.|
| TCP 443 (HTTPS) | I        |            | I vissa fall kan du se kommentarer | Inkommande anslutningar bör öppnas bara för specifika scenarier. Om icke-HTTP-protokoll som AMQP, MQTT inte kan konfigureras, kan meddelanden skickas via WebSockets via port 443. |

Fullständig information går du till [brandvägg och port konfigurationsregler för IoT Edge-distribution](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönster för att filtrera det inkommande och utgående trafik. Din Data Box Edge-enhet och tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönster som associeras med dessa program kan användas för att konfigurera brandväggens regler. Det är viktigt att förstå att URL-mönster som associeras med dessa program kan ändra. De här ändringarna kräver nätverksadministratören kan övervaka och uppdatera brandväggsregler för din Data Box Edge som och när det behövs.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på Data Box Edge fasta IP-adresser, liberally i de flesta fall. Du kan dock använda den här informationen för att ställa in avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - Enheten (källa) IP-adresser ska alltid vara inställd till alla moln-aktiverat nätverksgränssnitt.
> - Målets IP-adresser ska vara inställd på [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>URL-mönster för gateway-funktionen

|    URL-mönster                                                                                                                                                                                                                                                                                                                                                                                                                                                      |    Komponenten eller funktioner                                                                           |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                                                                           |    Azure Data Box Edge service<br>Azure Service Bus<br>Autentiseringstjänsten                           |
|    http://\*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                                |    Enhetsaktivering                                                                                    |
|    http://crl.microsoft.com/pki/\*<br>http://www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    Återkallade certifikat                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                            |    Azure storage-konton och övervakning                                                                |
|    http://windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Microsoft Update-servrar                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    Akamai CDN                                                                                           |
|    https://\*.partners.extranet.microsoft.com/\*                                                                                                                                                                                                                                                                                                                                                                                                                    |    Supportpaket                                                                                      |
|    http://\*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                                     |    Telemetritjänsten i Windows, finns i uppdateringen för customer experience och diagnostiktelemetri      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                         |


### <a name="url-patterns-for-compute-feature"></a>URL-mönster för compute-funktion

| URL-mönster                      | Komponenten eller funktioner                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | Microsoft behållarregister (krävs)               |   |
| https://\*.azurecr.io                     | Personliga och 3 part behållarregister (valfritt) |   |
| https://\*.azure-devices.net              | IoT Hub-åtkomst (krävs)                             |   |

## <a name="internet-bandwidth"></a>Internetbandbredd

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Nästa steg

* [Distribuera din Azure Data Box-Edge](data-box-Edge-deploy-prep.md)
