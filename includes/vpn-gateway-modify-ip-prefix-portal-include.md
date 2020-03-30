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
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187179"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Ändra IP-adressprefix för nätverksgateway – ingen gatewayanslutning

#### <a name="to-add-additional-address-prefixes"></a>Så här lägger du till ytterligare adressprefix:

1. Klicka på **Konfiguration**i avsnittet Inställningar i avsnittet Inställningar i resursen **Lokal** nätverksgateway .
2. Lägg till IP-adressutrymmet i rutan *Lägg till ytterligare adressintervall.*
3. Spara inställningarna genom att klicka på **Spara.**

#### <a name="to-remove-address-prefixes"></a>Så här tar du bort adressprefix:

1. Klicka på **Konfiguration**i avsnittet Inställningar i avsnittet Inställningar i resursen **Lokal** nätverksgateway .
2. Klicka på **'...'** på raden som innehåller prefixet som du vill ta bort.
3. Klicka på **Ta bort**.
4. Spara inställningarna genom att klicka på **Spara.**

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Ändra IP-adressprefix för nätverksgateway – existerande gatewayanslutning

Om du har en gatewayanslutning och vill lägga till eller ta bort IP-adressprefixet som ingår i din lokala nätverksgateway, behöver du genomföra följande steg i turordning. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressprefix behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.

#### <a name="1-remove-the-connection"></a>1. Ta bort anslutningen.

1. Klicka på Anslutningar i avsnittet **Inställningar** i avsnittet **Inställningar**på resursen Lokal nätverksgateway .
2. Klicka på **...** på raden för varje anslutning och klicka sedan på **Ta bort**.
3. Spara inställningarna genom att klicka på **Spara.**

#### <a name="2-modify-the-address-prefixes"></a>2. Ändra adressprefixen.

Så här lägger du till ytterligare adressprefix:

1. Klicka på **Konfiguration**i avsnittet Inställningar i avsnittet Inställningar i resursen **Lokal** nätverksgateway .
2. Lägg till IP-adressutrymmet.
3. Spara inställningarna genom att klicka på **Spara.**

Så här tar du bort adressprefix:

1. Klicka på **Konfiguration**i avsnittet Inställningar i avsnittet Inställningar i resursen **Lokal** nätverksgateway .
2. Klicka på **...** på raden som innehåller det prefix som du vill ta bort.
3. Klicka på **Ta bort**.
4. Spara inställningarna genom att klicka på **Spara.**

#### <a name="3-recreate-the-connection"></a>3. Återskapa anslutningen.

1. Navigera till virtual network gateway för ditt virtuella nätverk. (Inte den lokala nätverksgatewayen.)
2. Klicka på **Anslutningar**i avsnittet **Inställningar** i den virtuella nätverksgatewayen.
3. Klicka på **bladet + Lägg** till för att öppna **Bladet Lägg till anslutning.**
4. Återskapa anslutningen.
5. Klicka på **OK** för att skapa anslutningen.