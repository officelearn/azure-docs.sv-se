---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187178"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Så här ändrar du IP-adressen för den lokala nätverksgatewayen – ingen gatewayanslutning

Använd exemplet för att ändra en lokal nätverksgateway som inte har någon gatewayanslutning. När du ändrar det här värdet kan du också ändra adressprefixen på samma gång.

1. Klicka på **Konfiguration**i avsnittet Inställningar i avsnittet Inställningar i resursen **Lokal** nätverksgateway .
2. Ändra IP-adressen i rutan **IP-adress.**
3. Spara inställningarna genom att klicka på **Spara**.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Så här ändrar du IP-adressen för den lokala nätverksgatewayen – befintlig gatewayanslutning

Om du vill ändra en lokal nätverksgateway som har en anslutning måste du först ta bort anslutningen. När anslutningen har tagits bort kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Du kan också ändra adressprefixen på samma gång. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressen för gatewayen behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.
 
#### <a name="1-remove-the-connection"></a>1. Ta bort anslutningen.

1. Klicka på Anslutningar i avsnittet **Inställningar** i avsnittet **Inställningar**på resursen Lokal nätverksgateway .
2. Klicka på **...** på raden för anslutningen och klicka sedan på **Ta bort**.
3. Spara inställningarna genom att klicka på **Spara.**

#### <a name="2-modify-the-ip-address"></a>2. Ändra IP-adressen.

Du kan också ändra adressprefixen på samma gång.

1. Ändra IP-adressen i rutan **IP-adress.**
2. Spara inställningarna genom att klicka på **Spara**.

#### <a name="3-recreate-the-connection"></a>3. Återskapa anslutningen.

1. Navigera till virtual network gateway för ditt virtuella nätverk. (Inte den lokala nätverksgatewayen.)
2. Klicka på **Anslutningar**i avsnittet **Inställningar** i den virtuella nätverksgatewayen.
3. Klicka på **bladet + Lägg** till för att öppna **Bladet Lägg till anslutning.**
4. Återskapa anslutningen.
5. Klicka på **OK** för att skapa anslutningen.