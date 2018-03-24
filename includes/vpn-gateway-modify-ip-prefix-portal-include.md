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
ms.openlocfilehash: 3b8049515f753cbcf8ca068c1790f716f02d30b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
### <a name="noconnection"></a>Ändra IP-adressprefix för nätverksgateway – ingen gatewayanslutning

#### <a name="to-add-additional-address-prefixes"></a>Så här lägger du till ytterligare adressprefix:

1. På lokala nätverkets Gateway-resursen i den **inställningar** klickar du på **Configuration**.
2. Lägg till IP-adressutrymme i den *Lägg till ytterligare adressintervall* rutan.
3. Klicka på **spara** att spara dina inställningar.

#### <a name="to-remove-address-prefixes"></a>Så här tar du bort adressprefix:

1. På lokala nätverkets Gateway-resursen i den **inställningar** klickar du på **Configuration**.
2. Klicka på den **”...”** på den rad som innehåller det prefix som du vill ta bort.
3. Klicka på **ta bort**.
4. Klicka på **spara** att spara dina inställningar.

### <a name="withconnection"></a>Ändra IP-adressprefix för nätverksgateway – existerande gatewayanslutning

Om du har en gatewayanslutning och vill lägga till eller ta bort IP-adressprefixet som ingår i din lokala nätverksgateway, behöver du genomföra följande steg i turordning. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressprefix behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.

#### <a name="1-remove-the-connection"></a>1. Ta bort anslutningen.

1. På lokala nätverkets Gateway-resursen i den **inställningar** klickar du på **anslutningar**.
2. Klicka på den **...**  på rad för varje anslutning, klicka sedan på **ta bort**.
3. Klicka på **spara** att spara dina inställningar.

#### <a name="2-modify-the-address-prefixes"></a>2. Ändra adressprefixen.

Så här lägger du till ytterligare adressprefix:

1. På lokala nätverkets Gateway-resursen i den **inställningar** klickar du på **Configuration**.
2. Lägg till IP-adressutrymme.
3. Klicka på **spara** att spara dina inställningar.

Så här tar du bort adressprefix:

1. På lokala nätverkets Gateway-resursen i den **inställningar** klickar du på **Configuration**.
2. Klicka på den **...**  på den rad som innehåller det prefix som du vill ta bort.
3. Klicka på **ta bort**.
4. Klicka på **spara** att spara dina inställningar.

#### <a name="3-recreate-the-connection"></a>3. Skapa anslutningen.

1. Navigera till den virtuella Nätverksgatewayen för din VNet. (Inte den lokala Nätverksgatewayen.)
2. På den virtuella Nätverksgatewayen i den **inställningar** klickar du på **anslutningar**.
3. Klicka på den **+ Lägg till** att öppna den **Lägg till anslutning** bladet.
4. Återskapa din anslutning.
5. Klicka på **OK** att skapa anslutningen.