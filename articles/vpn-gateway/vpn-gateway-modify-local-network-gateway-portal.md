---
title: 'VPN Gateway: ändra gatewayens IP-adress inställningar: Azure Portal'
description: Den här artikeln vägleder dig genom att ändra IP-adressprefix för din lokala nätverksgateway med hjälp av Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983193"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Ändra inställningar för lokal nätverksgateway med Azure-portalen

Ibland ändras inställningarna för din lokala nätverksgateway AddressPrefix eller GatewayIPAddress. Den här artikeln visar hur du ändrar inställningarna för din lokala nätverksgateway. Du kan också ändra dessa inställningar med en annan metod genom att välja ett annat alternativ i listan nedan:

Innan du tar bort anslutningen kanske du vill ladda ned konfigurationen för dina anslutande enheter för att kunna hämta det definierade PSK-nätverket. På så sätt behöver du inte omdefiniera den på den andra sidan.

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Konfiguration av lokal nätverksgateway

Skärm bilden nedan visar **konfigurations** sidan för en lokal nätverks-gateway-resurs med hjälp av den offentliga IP-adress slut punkten:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Konfigurera lokal nätverksgateway – IP-adress":::

Detta är samma konfigurations sida med en FQDN-slutpunkt:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Konfigurera lokal nätverksgateway – FQDN":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Ändra IP-adressen för gatewayen

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen.

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Ändra IP-adressen i rutan **IP-adress** .
3. Spara inställningarna genom att klicka på **Spara**.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>Ändra Gateway-FQDN

Om den VPN-enhet som du vill ansluta till har ändrat FQDN (fullständigt kvalificerat domän namn) måste du ändra den lokala Nätverksgatewayen för att avspegla ändringen.

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. I rutan **FQDN** ändrar du domän namnet.
3. Spara inställningarna genom att klicka på **Spara**.

> ! Lägg Det går inte att ändra en lokal nätverksgateway mellan FQDN-slutpunkten och IP-adressens slut punkt. Du måste ta bort alla anslutningar som är associerade med den här lokala Nätverksgatewayen, skapa en ny med den nya slut punkten (IP-adress eller fullständigt domän namn) och sedan återskapa anslutningarna.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Ändra IP-adressprefix

### <a name="to-add-additional-address-prefixes"></a>Så här lägger du till ytterligare adressprefix:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Lägg till IP-adressutrymmet i rutan *Lägg till ytterligare adress intervall* .
3. Klicka på **Spara** för att spara inställningarna.

### <a name="to-remove-address-prefixes"></a>Så här tar du bort adressprefix:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Klicka på **"..."** på den rad som innehåller prefixet som du vill ta bort.
3. Klicka på **Ta bort**.
4. Klicka på **Spara** för att spara inställningarna.

## <a name="modify-bgp-settings"></a><a name="bgp"></a>Ändra BGP-inställningar

### <a name="to-add-or-update-bgp-settings"></a>Lägga till eller uppdatera BGP-inställningar:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Välj **Konfigurera BGP-inställningar** om du vill visa eller uppdatera BGP-konfigurationerna för den här lokala Nätverksgatewayen
3. Lägg till eller uppdatera det autonoma system numret eller BGP-peer-IP-adressen i motsvarande fält
4. Klicka på **Spara** för att spara inställningarna.

### <a name="to-remove-bgp-settings"></a>Ta bort BGP-inställningar:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway klickar du på **konfiguration**.
2. Avmarkera alternativet för att **Konfigurera BGP-inställningar** för att ta bort den befintliga BGP-ASN-och BGP-peer-IP-adressen
3. Klicka på **Spara** för att spara inställningarna.

## <a name="next-steps"></a>Nästa steg

Du kan verifiera din gateway-anslutning. Se [Verifiera en gateway-anslutning](vpn-gateway-verify-connection-resource-manager.md).
