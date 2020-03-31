---
title: Systemkrav för Microsoft Azure Data Box Edge| Microsoft-dokument
description: Lär dig mer om programvaru- och nätverkskraven för din Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260233"
---
# <a name="azure-data-box-edge-system-requirements"></a>Systemkrav för Azure Data Box Edge

I den här artikeln beskrivs de viktiga systemkraven för microsoft Azure Data Box Edge-lösningen och för klienterna som ansluter till Azure Data Box Edge. Vi rekommenderar att du granskar informationen noggrant innan du distribuerar databoxens edge. Du kan referera tillbaka till den här informationen efter behov under distributionen och efterföljande åtgärd.

Systemkraven för Data Box Edge inkluderar:

- **Programvarukrav för värdar** - beskriver plattformar som stöds, webbläsare för det lokala konfigurationsgränssnittet, SMB-klienter och eventuella ytterligare krav för de klienter som kommer åt enheten.
- **Nätverkskrav för enheten** - ger information om eventuella nätverkskrav för driften av den fysiska enheten.

## <a name="supported-os-for-clients-connected-to-device"></a>Operativsystem som stöds för klienter som är anslutna till enheten

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protokoll som stöds för klienter som ansluter till enheten

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Webbläsare som stöds för lokalt webbgränssnitt

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Krav på nätverksport

### <a name="port-requirements-for-data-box-edge"></a>Portkrav för Data Box Edge

I följande tabell visas de portar som måste öppnas i brandväggen för att tillåta SMB-, moln- eller hanteringstrafik. I den här tabellen refererar *i* eller *inkommande* till den riktning från vilken inkommande klient begär åtkomst till enheten. *Utgående* eller *utgående* refererar till i vilken riktning databoxens edge-enhet skickar data externt, utöver distributionen, till exempel utgående till internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Portkrav för IoT Edge

Azure IoT Edge tillåter utgående kommunikation från en lokal Edge-enhet till Azure-moln med hjälp av IoT Hub-protokoll som stöds. Inkommande kommunikation krävs endast för specifika scenarier där Azure IoT Hub behöver trycka ned meddelanden till Azure IoT Edge-enheten (till exempel Meddelanden från molnet till enheten).

Använd följande tabell för portkonfiguration för servrarna som är värdar för Azure IoT Edge-körningen:

| Port nr. | In eller ut | Port-scope | Krävs | Riktlinjer |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Ut       | WAN        | Ja      | Utgående öppen för IoT Edge-etablering. Den här konfigurationen krävs när du använder manuella skript eller DPS (Azure IoT Device Provisioning Service).|

Fullständig information finns i [brandväggs- och portkonfigurationsregler för IoT Edge-distribution](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brandväggsregler

Nätverksadministratörer kan ofta konfigurera avancerade brandväggsregler baserat på URL-mönstren för att filtrera inkommande och utgående trafik. Din Data Box Edge-enhet och tjänsten är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagringskonton och Microsoft Update-servrar. URL-mönstren som är associerade med dessa program kan användas för att konfigurera brandväggsregler. Det är viktigt att förstå att url-mönstren som är associerade med dessa program kan ändras. Dessa ändringar kräver att nätverksadministratören övervakar och uppdaterar brandväggsregler för databoxens edge om och när det behövs.

Vi rekommenderar att du ställer in brandväggsreglerna för utgående trafik, baserat på fast IP-adresser i Data Box Edge, frikostigt i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brandväggsregler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - Enhets-IPs-adresser (källa) ska alltid ställas in på alla molnaktiverade nätverksgränssnitt.
> - Mål-IP-adresser ska ställas in [på AZURE datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>URL-mönster för gateway-funktionen

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-mönster för beräkningsfunktionen

| URL-mönster                      | Komponent eller funktionalitet                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft-behållarregister (obligatoriskt)               |
| https://\*.azurecr.io                     | Behållareregister från personliga och tredje part (valfritt) | 
| https://\*.azure-devices.net              | IoT Hub-åtkomst (obligatoriskt)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL-mönster för gateway för Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL-mönster för beräkning för Azure Government

| URL-mönster                      | Komponent eller funktionalitet                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft-behållarregister (obligatoriskt)               |
| https://\*.azure-devices.us              | IoT Hub-åtkomst (obligatoriskt)           |
| https://\*.azurecr.us                    | Behållareregister från personliga och tredje part (valfritt) | 

## <a name="internet-bandwidth"></a>Internet bandbredd

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Beräkna storleksöverväganden

Använd din upplevelse när du utvecklar och testar din lösning för att säkerställa att det finns tillräckligt med kapacitet på din Data Box Edge-enhet och du får optimal prestanda från din enhet.

Faktorer som du bör tänka på är:

- **Behållardetaljerna** - Tänk på följande.

    - Hur många behållare finns i din arbetsbelastning? Du kan ha en hel del lätta behållare jämfört med några resurskrävande.
    - Vilka resurser tilldelas dessa behållare jämfört med vilka resurser de förbrukar?
    - Hur många lager delar dina behållare?
    - Finns det oanvända behållare? En stoppad behållare tar fortfarande upp diskutrymme.
    - På vilket språk är dina behållare skrivna?
- **Storleken på de data som bearbetas** - Hur mycket data kommer dina behållare att bearbetas? Kommer dessa data att förbruka diskutrymme eller så bearbetas data i minnet?
- **Förväntad prestanda** - Vilka är de önskade prestandaegenskaperna för din lösning? 

För att förstå och förfina prestanda för din lösning kan du använda:

- Beräkningsmåtten som är tillgängliga i Azure-portalen. Gå till din Data Box Edge-resurs och gå sedan till **Övervakning > mått**. Titta på **Edge-beräkningen - Minnesanvändning** och **Edge-beräkning - Procent CPU** för att förstå tillgängliga resurser och hur de resurser som förbrukas.
- De övervakningskommandon som är tillgängliga via Enhetens PowerShell-gränssnitt, till exempel:

    - `dkrdbe stats`för att få en livestream av resursanvändningsstatistik för behållare.to get a live stream of container(s) resource usage statistics. Kommandot stöder cpu-, minnesanvändning, minnesgräns och nätverks-IO-mått.
    - `dkrdbe system df`för att få information om hur mycket diskutrymme som används. 
    - `dkrdbe image prune`för att rensa oanvända bilder och frigöra utrymme.
    - `dkrdbe ps --size`om du vill visa den ungefärliga storleken på en behållare som körs. 

    Mer information om tillgängliga kommandon finns i [Övervaka och felsöka beräkningsmoduler](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Se slutligen till att du validerar din lösning på din datauppsättning och kvantifierar prestandan på Data Box Edge innan du distribuerar i produktion.


## <a name="next-step"></a>Nästa steg

- [Distribuera din Azure Data Box Edge](data-box-edge-deploy-prep.md)
