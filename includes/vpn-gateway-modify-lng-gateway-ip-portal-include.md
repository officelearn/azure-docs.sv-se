---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187178"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Ändra IP-adressen för den lokala Nätverksgatewayen – ingen gateway-anslutning

Använd exemplet för att ändra en lokal nätverksgateway som inte har någon gatewayanslutning. När du ändrar det här värdet kan du också ändra adressprefixen på samma gång.

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Ändra IP-adressen i rutan **IP-adress** .
3. Spara inställningarna genom att klicka på **Spara**.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Ändra IP-adress för den lokala Nätverksgatewayen – befintlig gateway-anslutning

Om du vill ändra en lokal nätverksgateway som har en anslutning måste du först ta bort anslutningen. När anslutningen har tagits bort kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Du kan också ändra adressprefixen på samma gång. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressen för gatewayen behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.
 
#### <a name="1-remove-the-connection"></a>1. ta bort anslutningen.

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **anslutningar**.
2. Klicka på **...** på raden för anslutningen och klicka sedan på **ta bort**.
3. Klicka på **Spara** för att spara inställningarna.

#### <a name="2-modify-the-ip-address"></a>2. ändra IP-adressen.

Du kan också ändra adressprefixen på samma gång.

1. Ändra IP-adressen i rutan **IP-adress** .
2. Spara inställningarna genom att klicka på **Spara**.

#### <a name="3-recreate-the-connection"></a>3. skapa anslutningen igen.

1. Navigera till Virtual Network Gateway för ditt VNet. (Inte den lokala Nätverksgatewayen.)
2. Klicka på **anslutningar**i avsnittet **Inställningar** på Virtual Network Gateway.
3. Klicka på **+ Lägg** till för att öppna bladet **Lägg till anslutning** .
4. Återskapa anslutningen.
5. Klicka på **OK** för att skapa anslutningen.