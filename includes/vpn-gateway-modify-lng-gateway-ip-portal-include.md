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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419609"
---
### <a name="gwipnoconnection"></a> Att ändra lokala gateway-IP-adress – ingen gatewayanslutning

Använd exemplet för att ändra en lokal nätverksgateway som inte har någon gatewayanslutning. När du ändrar det här värdet kan du också ändra adressprefixen på samma gång.

1. På den lokala nätverksgatewaysresursen i den **inställningar** klickar du på **Configuration**.
2. I den **IP-adress** ändrar IP-adress.
3. Spara inställningarna genom att klicka på **Spara**.

### <a name="gwipwithconnection"></a>Att ändra lokala nätverket gatewayens IP-adress – existerande gatewayanslutning

Om du vill ändra en lokal nätverksgateway som har en anslutning måste du först ta bort anslutningen. När anslutningen har tagits bort kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Du kan också ändra adressprefixen på samma gång. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressen för gatewayen behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.
 
#### <a name="1-remove-the-connection"></a>1. Ta bort anslutningen.

1. På den lokala nätverksgatewaysresursen i den **inställningar** klickar du på **anslutningar**.
2. Klicka på den **...**  på raden för anslutningen, klicka sedan på **ta bort**.
3. Klicka på **spara** att spara dina inställningar.

#### <a name="2-modify-the-ip-address"></a>2. Ändra IP-adressen.

Du kan också ändra adressprefixen på samma gång.

1. I den **IP-adress** ändrar IP-adress.
2. Spara inställningarna genom att klicka på **Spara**.

#### <a name="3-recreate-the-connection"></a>3. Återskapa anslutningen.

1. Navigera till den virtuella Nätverksgatewayen för ditt virtuella nätverk. (Inte den lokala Nätverksgatewayen.)
2. På den virtuella Nätverksgatewayen i den **inställningar** klickar du på **anslutningar**.
3. Klicka på den **+ Lägg till** att öppna den **Lägg till anslutning** bladet.
4. Återskapa din anslutning.
5. Klicka på **OK** att skapa anslutningen.