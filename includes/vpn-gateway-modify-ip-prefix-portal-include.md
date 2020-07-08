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
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187179"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Ändra IP-adressprefix för nätverksgateway – ingen gatewayanslutning

#### <a name="to-add-additional-address-prefixes"></a>Så här lägger du till ytterligare adressprefix:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Lägg till IP-adressutrymmet i rutan *Lägg till ytterligare adress intervall* .
3. Klicka på **Spara** för att spara inställningarna.

#### <a name="to-remove-address-prefixes"></a>Så här tar du bort adressprefix:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Klicka på **"..."** på den rad som innehåller prefixet som du vill ta bort.
3. Klicka på **Ta bort**.
4. Klicka på **Spara** för att spara inställningarna.

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Ändra IP-adressprefix för nätverksgateway – existerande gatewayanslutning

Om du har en gatewayanslutning och vill lägga till eller ta bort IP-adressprefixet som ingår i din lokala nätverksgateway, behöver du genomföra följande steg i turordning. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressprefix behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.

#### <a name="1-remove-the-connection"></a>1. ta bort anslutningen.

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **anslutningar**.
2. Klicka på **...** på raden för varje anslutning och klicka sedan på **ta bort**.
3. Klicka på **Spara** för att spara inställningarna.

#### <a name="2-modify-the-address-prefixes"></a>2. ändra adressprefix.

Så här lägger du till ytterligare adressprefix:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Lägg till IP-adressutrymmet.
3. Klicka på **Spara** för att spara inställningarna.

Så här tar du bort adressprefix:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Klicka på **...** på den rad som innehåller det prefix som du vill ta bort.
3. Klicka på **Ta bort**.
4. Klicka på **Spara** för att spara inställningarna.

#### <a name="3-recreate-the-connection"></a>3. skapa anslutningen igen.

1. Navigera till Virtual Network Gateway för ditt VNet. (Inte den lokala Nätverksgatewayen.)
2. Klicka på **anslutningar**i avsnittet **Inställningar** på Virtual Network Gateway.
3. Klicka på **+ Lägg** till för att öppna bladet **Lägg till anslutning** .
4. Återskapa anslutningen.
5. Klicka på **OK** för att skapa anslutningen.