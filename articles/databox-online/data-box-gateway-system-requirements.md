---
title: Systemkrav för Microsoft Azure Data Box Gateway | Microsoft Docs
description: Läs mer om programvara och nätverkskrav för din Azure Data Box-Gateway
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 915190f6f2773d22d14c75be1140f96b7dd40559
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991580"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Systemkrav för Azure Data Box-Gateway (förhandsversion)

Den här artikeln beskriver viktiga systemkraven för din Microsoft Azure Data Box Gateway-lösning och för klienter som ansluter till Azure Data Box-Gateway. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box-Gateway och sedan refererar tillbaka till det som behövs under distributionen och efterföljande driften.

Systemkrav för den virtuella enheten Data Box-Gateway är:

- **Programvarukrav för värdar** -beskriver plattformarna som stöds, webbläsare för den lokala konfigurationen Användargränssnittet, SMB-klienter och ytterligare krav för värdar som ansluter till enheten.
- **Nätverkskrav för enheten** – innehåller information om alla nätverkskrav för driften av den virtuella enheten.

> [!IMPORTANT]
> Data Box-Gateway är i förhandsversion. Granska den [villkor för användning av förhandsversionen av](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen. 

## <a name="supported-os-for-clients-connected-to-device"></a>Operativsystem som stöds för klienter som är anslutna till enhet

Här är en lista över operativsystem som stöds för klienter eller värdar som är anslutna till Data Box-Gateway.

| **Operativsystem/plattform** | **Versioner** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 |
| Windows |8, 10 |
| SUSE Linux |Enterprise Server 12 (x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>Protokoll som stöds för klienter som ansluter till enheten

|**Protokoll** |**Versioner**   |**Anteckningar**  |
|---------|---------|---------|
|SMB    | 2.X, 3.X      | SMB-1 stöds inte.|
|NFS     | V3- och V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>Stöds virtualiseringsplattformar för enhet

| **Operativsystem/plattform**  |**Versioner**   |**Anteckningar**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5        |VMware-verktyg stöds inte.         |


## <a name="supported-storage-accounts"></a>Stöds storage-konton

Här är en lista över typerna som stöds för Data Box-Gateway.

| **Lagringskonto** | **Anteckningar** |
| --- | --- |
| Klassisk | Standard |
| Generellt syfte  |Standard. både V1 och V2 stöds. Både frekventa och lågfrekventa nivåer stöds. |


## <a name="supported-storage-types"></a>Lagringstyper som stöds

Här är en lista över typerna som stöds för Data Box-Gateway.

| **Filformat** | **Anteckningar** |
| --- | --- |
| Azure blockblob | |
| Sida för Azure-blob  | |
| Azure Files | |

## <a name="supported-browsers-for-local-web-ui"></a>Webbläsare som stöds för lokala webbgränssnittet

Här är en lista över webbläsare som stöds för det lokala webbgränssnittet för den virtuella enheten.

|Webbläsare  |Versioner  |Ytterligare krav/anteckningar  |
|---------|---------|---------|
|Google Chrome   |Senaste versionen         |         |
|Microsoft Edge    | Senaste versionen        |         |
|Internet Explorer     | Senaste versionen        |         |
|FireFox    |Senaste versionen         |         |


## <a name="networking-requirements"></a>Nätverkskrav

I följande tabell visas de portar som måste öppnas i brandväggen att tillåta SMB, i molnet eller hanteringstrafik. I den här tabellen *i* eller *inkommande* avser riktningen från vilka inkommande begäranden för klientåtkomst till din enhet. *Ut* eller *utgående* refererar till den riktning som din Data Box-Gateway-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

| Porten Nej.| In eller ut | Port omfång| Krävs|   Anteckningar                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80 (HTTP)|Utdata|WAN |Nej|Utgående port används för åtkomst till Internet för att hämta uppdateringar. <br>Utgående webbproxy kan konfigureras av användaren. |                          
| TCP 443 (HTTPS)|Utdata|WAN|Ja|Utgående port används för att komma åt data i molnet.<br>Utgående webbproxy kan konfigureras av användaren.|   
| UDP 53 (DNS)|Utdata|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en Internet-baserad DNS-server.<br>Vi rekommenderar att du använder lokala DNS-servern. |
| UDP 123 (NTP)|Utdata|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en Internetbaserad NTP-server.  |
| UDP 67 (DHCP)|Utdata|WAN|I vissa fall<br>Se information|Den här porten krävs endast om du använder en DHCP-server.  |
| TCP 80 (HTTP)|I|LAN|Ja|Det här är den inkommande porten för lokala Användargränssnittet på enheten för lokal hantering. <br>Åtkomst till lokala Användargränssnittet via HTTP omdirigeras automatiskt till HTTPS.  | 
| TCP 443 (HTTPS)|I|LAN|Ja|Det här är den inkommande porten för lokala Användargränssnittet på enheten för lokal hantering. | 

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönster för att filtrera det inkommande och utgående trafik. Din Data Box-Gateway-enhet och Data Box-Gateway-tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönster som associeras med dessa program kan användas för att konfigurera brandväggens regler. Det är viktigt att förstå att URL-mönster som associeras med dessa program kan ändra. Detta kräver i sin tur att nätverksadministratören kan övervaka och uppdatera brandväggsregler för din Data Box-Gateway som och när det behövs.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på Data Box Gateway fasta IP-adresser, liberally i de flesta fall. Du kan dock använda den här informationen för att ställa in avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - Enheten (källa) IP-adresser ska alltid vara inställd till alla moln-aktiverat nätverksgränssnitt.
> - Målets IP-adresser ska vara inställd på [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).

|     URL-mönster                                                                                                                                                                                                                                                                                                                                                                                                                                       |     Komponenten/funktioner                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.accesscontrol.windows.net/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Azure Data Box-Gateway-tjänsten<br>Access Control Service<br>Azure Service Bus<br>Autentiseringstjänsten    |
|    http://*.Backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    Enhetsaktivering                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    Återkallade certifikat                                                                               |
|    https://*.core.windows.net/* https://*. data.microsoft.com http://*. msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Azure storage-konton och övervakning                                                                |
|    http://windowsupdate.microsoft.com<br>http://*. windowsupdate.microsoft.com<br>https://*. windowsupdate.microsoft.com<br>http://*. update.microsoft.com<br>https://*. update.microsoft.com<br>http://*. windowsupdate.com<br>http://download.microsoft.com<br>http://*. download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*. ws.microsoft.com<br>https://*. ws.microsoft.com<br>http://*.MP.microsoft.com        |    Microsoft Update-servrar                                                                             |
|    http://*.Deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Akamai CDN                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    Supportpaket                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Telemetritjänsten i Windows, finns i uppdateringen för customer experience och diagnostiktelemetri      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>Internetbandbredd

Följande krav gäller för minsta Internet-bandbredd tillgänglig för dina Data Box Gateway-enheter.

- Din Data Box-Gateway har en dedikerad 20 Mbit/s internetbandbredd (eller mer) vid alla tidpunkter. Den här bandbredden ska inte delas med andra program. 
- Din Data Box-Gateway har en dedikerad 32 Mbit/s internetbandbredd (eller mer) när du använder nätverksbegränsning.

## <a name="next-step"></a>Nästa steg

* [Distribuera din Azure Data Box-Gateway](data-box-gateway-deploy-prep.md)

