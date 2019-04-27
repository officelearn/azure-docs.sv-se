---
title: Alternativ för kabelanslutning av Azure Data Box | Microsoft Docs
description: Beskriver de olika kabelanslutningsalternativen för Azure Data Box.
services: databox
author: WenJason
ms.service: databox
ms.subservice: pod
ms.topic: overview
origin.date: 10/24/2018
ms.date: 02/25/2019
ms.author: v-jay
ms.openlocfilehash: 4759676eff03606998759a586f758b7a26ead23d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728705"
---
# <a name="cabling-options-for-your-azure-data-box"></a>Alternativ för kabelanslutning av Azure Data Box

I den här artikeln beskrivs olika sätt som du kan kabelansluta Azure Data Box för dataöverföring.

## <a name="transfer-via-mgmt-port"></a>Överföring via MGMT-port

Det är alternativet är den absolut minsta konfigurationen för Data Box-enheten. Du kan konfigurera endast MGMT-porten för både hantering och data.

Innan du börjar ska du kontrollera att du har:

- En RJ45 Ethernet-kabel för MGMT.
- En datakälla som kör ett [operativsystem som stöds](data-box-system-requirements.md#supported-operating-systems-for-clients).

Kabelanslut enheten med hjälp av följande steg.

1. Använd en RJ45-kabel för att ansluta MGMT-porten till servern som innehåller data.

    ![Konfiguration på en port](media/data-box-cable-options/cabling-mgmt-only.png)

2. Ställ in följande på servern:

    - **IP-adress** på 192.168.100.5
    - **Undernät** på 255.255.255.0

3. Åtkomst till lokalt Webbgränssnitt för enheten på: 192.168.100.10. Logga in och lås upp Data Box-enheten med upplåsningslösenordet från Azure-portalen.


## <a name="transfer-via-data-port-with-static-ips"></a>Överföring via DATA-port med statiska IP-adresser

Du kan konfigurera två portar för Data Box-enheten, MGMT-porten för hanteringstrafik och en av dataportarna för data. Dataportarna kan vara DATA 1, DATA 2 eller DATA 3.

Vi rekommenderar starkt att om du bara konfigurerar en dataport bör den vara en 10-GbE-port som DATA 1 eller DATA 2. En 1-GbE-port skulle dramatiskt öka tiden det tar att överföra data.

Innan du börjar ska du kontrollera att du har:

- En RJ45 Ethernet-kabel för MGMT.
- En 10-GbE SFP+ Twinax-kopparkabel för varje 10-GbE-dataport som du vill ansluta.
- En eller flera datakällor som kör ett [operativsystem som stöds](data-box-system-requirements.md#supported-operating-systems-for-clients).

### <a name="option-1---initial-setup-via-server"></a>Alternativ 1 – Första installation via server

Kabelanslut enheten med hjälp av följande steg.

1. Använd en RJ45 Ethernet-kabeln från servern direkt till MGMT-porten för konfiguration.
2. Använd en RJ45-kabel för DATA 3 eller SFP+-kabel för att ansluta DATA 1 eller DATA 2 till servern som fungerar som datakälla. Vi rekommenderar att du använder 10-GbE DATA 1- eller DATA 2-portar för bra prestanda.
3. Ställ in följande på servern:

   - **IP-adress** på 192.168.100.5
   - **Undernät** på 255.255.255.0

     ![Konfiguration med två portar](media/data-box-cable-options/cabling-2-port-setup.png)

4. Åtkomst till lokalt Webbgränssnitt för enheten på: 192.168.100.10. Logga in och lås upp Data Box-enheten med upplåsningslösenordet från Azure-portalen.
5. Tilldela statiska IP-adresser till dataportarna du har konfigurerat.

### <a name="option-2---initial-setup-via-separate-computer"></a>Alternativ 2 – Första installation via en separat dator

Kabelanslut enheten med hjälp av följande steg.

1. Använd en RJ45 Ethernet-kabeln från servern direkt till MGMT-porten för konfiguration.
2. Använd en RJ45-kabel för DATA 3 eller SFP+-kabel för att ansluta DATA 1 eller DATA 2 till servern. Vi rekommenderar att du använder 10-GbE DATA 1- eller DATA 2-portar för bra prestanda. Dataportarna ansluts via en 10-GbE-switch till servern med data.
3. Konfigurera Ethernet-adaptern på den bärbara datorn du använder för att ansluta enheten med:

   - **IP-adress**: 192.168.100.5
   - **Undernät**: 255.255.255.0.

     ![Konfiguration med två portar med switch](media/data-box-cable-options/cabling-with-static-ip.png)

4. Åtkomst till lokalt Webbgränssnitt för enheten på: 192.168.100.10. Logga in och lås upp Data Box-enheten med upplåsningslösenordet från Azure-portalen.
5. Identifiera IP-adresserna som tilldelats av DHCP-servern.

## <a name="transfer-via-data-port-with-static-ips-using-a-switch"></a>Överföring via DATA-port med statiska IP-adresser med en switch 

Använd den här konfigurationen för flera datakällor i 1 GbE- och 10 gbE-nätverk.

Innan du börjar ska du kontrollera att du har:

- En RJ45 Ethernet-kabel för MGMT.
- En 10-GbE SFP+ Twinax-kopparkabel för varje 10-GbE-dataport som du vill ansluta.
- En eller flera datakällor som kör ett [operativsystem som stöds](data-box-system-requirements.md#supported-operating-systems-for-clients). Dessa datakällor kan vara i olika nätverk som 1 GbE- eller 10-GbE-nätverk.

Kabelanslut enheten med hjälp av följande steg.

1. Använd en RJ45 Ethernet-kabeln från servern direkt till MGMT-porten för konfiguration.
2. Använd en RJ45-kabel för DATA 3 eller SFP+-kabel för att ansluta DATA 1 eller DATA 2 till servern. Vi rekommenderar att du använder 10-GbE DATA 1- eller DATA 2-portar för bra prestanda.
3. Konfigurera Ethernet-adaptern på den bärbara datorn du använder för att ansluta enheten med:

   - **IP-adress**: 192.168.100.5
   - **Undernät**: 255.255.255.0.

     ![Konfiguration med två portar med switch](media/data-box-cable-options/cabling-with-switch-static-ip.png)

4. Åtkomst till lokalt Webbgränssnitt för enheten på: 192.168.100.10. Logga in och lås upp Data Box-enheten med upplåsningslösenordet från Azure-portalen.
5. Tilldela statiska IP-adresser till dataportarna du har konfigurerat.


## <a name="transfer-via-data-port-in-a-dhcp-environment"></a>Överföring via DATA-port i en DHCP-miljön

Använd den här konfigurationen om enheten kommer att finnas i DHCP-miljön.

Innan du börjar ska du kontrollera att du har:

- En RJ45-kabel om du vill ansluta DATA 1.
- En 10-GbE SFP+ Twinax-kopparkabel för varje 10-GbE-dataport som du vill ansluta.
- En eller flera datakällor som kör ett [operativsystem som stöds](data-box-system-requirements.md#supported-operating-systems-for-clients). Dessa datakällor kan vara i olika nätverk som 1 GbE- eller 10-GbE-nätverk.

Kabelanslut enheten med hjälp av följande steg.

1. Använd en RJ45- eller SFP+-kabel via en switch (där DHCP-servern är tillgänglig) till servern.

    ![Konfiguration med två portar med switch](media/data-box-cable-options/cabling-dhcp-data-only.png)
2. Använd DHCP-server eller DNS-server för att identifiera IP-adressen.
3. Via en server på samma nätverk öppnar du det lokala webbgränssnittet för enheten som använder IP-adressen som har tilldelats av DHCP-servern. Logga in och lås upp Data Box-enheten med upplåsningslösenordet från Azure-portalen.

## <a name="next-steps"></a>Nästa steg

- När du har kabelanslutit enheten går du till [Kopiera data till Azure Data Box](data-box-deploy-copy-data.md).
