---
title: Microsoft Azure Data Box Edge system krav | Microsoft Docs
description: Läs om program-och nätverks kraven för din Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260233"
---
# <a name="azure-data-box-edge-system-requirements"></a>Azure Data Box Edge system krav

I den här artikeln beskrivs viktiga system krav för din Microsoft Azure Data Box Edge-lösning och för klienter som ansluter till Azure Data Box Edge. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box Edge. Du kan referera tillbaka till den här informationen vid behov under distributionen och efterföljande åtgärder.

System kraven för Data Box Edge är:

- **Program varu krav för värdar** – beskriver plattformar som stöds, webbläsare för det lokala konfigurations gränssnittet, SMB-klienter och eventuella ytterligare krav för klienter som har åtkomst till enheten.
- **Nätverks krav för enheten** – ger information om eventuella nätverks krav för den fysiska enhetens drift.

## <a name="supported-os-for-clients-connected-to-device"></a>Operativ system som stöds för klienter som är anslutna till enheten

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protokoll som stöds för klienter som ansluter till enheten

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Webbläsare som stöds för lokalt webb gränssnitt

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Krav för nätverks port

### <a name="port-requirements-for-data-box-edge"></a>Port krav för Data Box Edge

I följande tabell visas de portar som måste öppnas i brand väggen för att tillåta SMB-, moln-eller hanterings trafik. I den här tabellen avser *i* eller *inkommande* den riktning från vilken inkommande klient begär åtkomst till din enhet. *Out* eller *utgående* avser i vilken riktning din data Box Edge enhet skickar data externt, utöver distributionen, till exempel utgående till Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Port krav för IoT Edge

Azure IoT Edge tillåter utgående kommunikation från en lokal Edge-enhet till Azure-molnet med IoT Hub protokoll som stöds. Inkommande kommunikation krävs endast för vissa scenarier där Azure IoT Hub behöver skicka meddelanden till Azure IoT Edge-enheten (till exempel molnet till enhets meddelanden).

Använd följande tabell för port konfiguration för servrar som är värdar för Azure IoT Edge Runtime:

| Port nr. | In eller ut | Port omfång | Krävs | Riktlinjer |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Genomför       | Wide        | Ja      | Utgående öppen för IoT Edge etablering. Den här konfigurationen krävs när du använder manuella skript eller Azure IoT Device Provisioning-tjänsten (DPS).|

Fullständig information finns i [brand Väggs-och port konfigurations regler för IoT Edge distribution](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brand Väggs regler

Nätverks administratörer kan ofta konfigurera avancerade brand Väggs regler baserat på URL-mönster för att filtrera inkommande och utgående trafik. Din Data Box Edge enhet och tjänsten är beroende av andra Microsoft-program som Azure Service Bus, Azure Active Directory Access Control, lagrings konton och Microsoft Update-servrar. URL-mönstren som är kopplade till dessa program kan användas för att konfigurera brand Väggs regler. Det är viktigt att förstå att URL-mönstren som är kopplade till dessa program kan ändras. Dessa ändringar kräver att nätverks administratören övervakar och uppdaterar brand Väggs regler för din Data Box Edge som och när det behövs.

Vi rekommenderar att du ställer in brand Väggs regler för utgående trafik, baserat på Data Box Edge fasta IP-adresser, i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brand Väggs regler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - IP-adresserna för enheten (källa) ska alltid anges till alla molnbaserade nätverks gränssnitt.
> - Mål-IP-adresser ska anges till [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>URL-mönster för gateway-funktion

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-mönster för beräknings funktion

| URL-mönster                      | Komponent eller funktion                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*. cdn.mscr.io | Microsoft container Registry (krävs)               |
| https://\*. azurecr.io                     | Personliga och tredje parts behållar register (valfritt) | 
| https://\*. azure-devices.net              | IoT Hub åtkomst (krävs)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL-mönster för gateway för Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL-mönster för beräkning för Azure Government

| URL-mönster                      | Komponent eller funktion                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*. cdn.mscr.com | Microsoft container Registry (krävs)               |
| https://\*. azure-devices.us              | IoT Hub åtkomst (krävs)           |
| https://\*. azurecr.us                    | Personliga och tredje parts behållar register (valfritt) | 

## <a name="internet-bandwidth"></a>Internet bandbredd

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Överväganden vid beräkning av storlek

Använd din upplevelse när du utvecklar och testar din lösning för att se till att det finns tillräckligt med kapacitet på din Data Box Edge enhet och du får optimala prestanda från din enhet.

Faktorer som du bör ta hänsyn till:

- **Behållar information** – Tänk på följande.

    - Hur många behållare finns i arbets belastningen? Du kan ha många enkla behållare jämfört med några resurs intensiva.
    - Vilka resurser allokeras till dessa behållare jämfört med vilka resurser de använder?
    - Hur många lager gör behållarna till delar?
    - Finns det oanvända behållare? En stoppad behållare tar fortfarande upp disk utrymme.
    - I vilket språk är dina behållare skrivna?
- **Storleken på de data som bearbetas** – hur mycket data kommer behållarna att bearbeta? Kommer dessa data att förbruka disk utrymme eller så bearbetas data i minnet?
- **Förväntade prestanda** – vilka är de önskade prestanda egenskaperna för lösningen? 

Om du vill förstå och förfina lösningens prestanda kan du använda:

- Beräknings måtten som är tillgängliga i Azure Portal. Gå till din Data Box Edge-resurs och gå sedan till **övervakning > mått**. Titta på processor **användningen för Edge Compute-Memory** och **Edge Compute-procent** för att förstå de tillgängliga resurserna och hur är resurserna förbrukade.
- De övervaknings kommandon som är tillgängliga via PowerShell-gränssnittet på enheten, till exempel:

    - `dkrdbe stats` för att hämta en Live-ström med användnings statistik för container (er). Kommandot stöder CPU, minnes användning, minnes gräns och nätverkets IO-mått.
    - `dkrdbe system df` för att hämta information om mängden disk utrymme som används. 
    - `dkrdbe image prune` att rensa oanvända avbildningar och frigöra utrymme.
    - `dkrdbe ps --size` för att visa den ungefärliga storleken på en behållare som körs. 

    Mer information om tillgängliga kommandon finns i [övervaka och felsöka Compute-moduler](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Till sist kontrollerar du att du validerar din lösning på din data uppsättning och kvantifierar prestandan på Data Box Edge innan du distribuerar i produktionen.


## <a name="next-step"></a>Nästa steg

- [Distribuera Azure Data Box Edge](data-box-edge-deploy-prep.md)
