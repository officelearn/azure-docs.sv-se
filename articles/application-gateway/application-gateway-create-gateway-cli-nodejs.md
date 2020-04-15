---
title: Skapa en Azure Application Gateway - Azure classic CLI
description: Lär dig hur du skapar en Application Gateway med hjälp av Azure classic CLI i Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: ed7bbbf55956d655115b94c8ac46432b3e36c49b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312633"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Skapa en programgateway med hjälp av Azure CLI

Azure Application Gateway är en Layer 7-lastbalanserare. Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt. Programgateway har följande programleveransfunktioner: HTTP-belastningsutjämning, cookie-baserad sessionstillhörighet och TLS (Transport Layer Security), tidigare kallat Secure Sockets Layer (SSL), avlastning, anpassade hälsoavsökningar och stöd för flera plats.

## <a name="prerequisite-install-the-azure-cli"></a>Förutsättning: Installera Azure CLI

Om du vill utföra stegen i den här artikeln måste du [installera Azure CLI](../xplat-cli-install.md) och du måste logga in i [Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Om du inte har ett Azure-konto behöver du ett. Registrera dig för en [kostnadsfri utvärderingsversion här](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Scenario

I det här scenariot lär du dig hur du skapar en programgateway med Azure-portalen.

Detta scenario kommer att:

* Skapa en medelstor programgateway med två instanser.
* Skapa ett virtuellt nätverk med namnet ContosoVNET med ett reserverat CIDR-block på 10.0.0.0/16.
* Skapa ett undernät som heter undernät01 som använder 10.0.0.0/28 som CIDR-block.

> [!NOTE]
> Ytterligare konfiguration av programgatewayen, inklusive anpassade hälsoavsökningar, serverdpooladresser och ytterligare regler konfigureras när programgatewayen har konfigurerats och inte under den första distributionen.

## <a name="before-you-begin"></a>Innan du börjar

Azure Application Gateway kräver ett eget undernät. När du skapar ett virtuellt nätverk ska du se till att du lämnar tillräckligt med adressutrymme för att ha flera undernät. När du har distribuerat en programgateway till ett undernät kan endast ytterligare programgateways läggas till i undernätet.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna **Kommandotolken**i Microsoft Azure och logga in.

```azurecli-interactive
az login
```

När du har angett föregående exempel anges en kod. Navigera https://aka.ms/devicelogin till i en webbläsare för att fortsätta inloggningsprocessen.

![cmd som visar enhetsinloggning][1]

Ange koden du fick i webbläsaren. Du omdirigeras till en inloggningssida.

![webbläsare för att ange kod][2]

När koden har angetts är du inloggad stänger du webbläsaren för att fortsätta med scenariot.

![har loggat in][3]

## <a name="switch-to-resource-manager-mode"></a>Växla till resurshanterarens läge

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Innan programgatewayen skapas skapas en resursgrupp för att innehålla programgatewayen. Nedan visas kommandot.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

När resursgruppen har skapats skapas ett virtuellt nätverk för programgatewayen.  I följande exempel var adressutrymmet som 10.0.0.0/16 enligt definitionen i föregående scenarioanteckningar.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Skapa ett undernät

När det virtuella nätverket har skapats läggs ett undernät till för programgatewayen.  Om du planerar att använda programgateway med en webbapp som finns i samma virtuella nätverk som programgatewayen, se till att lämna tillräckligt med utrymme för ett annat undernät.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

När det virtuella nätverket och undernätet har skapats är förskäliga för programgatewayen slutförda. Dessutom krävs ett tidigare exporterat PFX-certifikat och lösenordet för certifikatet för följande steg: IP-adresserna som används för serverda är IP-adresserna för serverdservern. Dessa värden kan vara antingen privata IP-adresser i det virtuella nätverket, offentliga ips eller fullständigt kvalificerade domännamn för dina serverdelsservrar.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> För en lista över parametrar som kan tillhandahållas under skapandet kör följande kommando: **azure network application-gateway create --help**.

I det här exemplet skapas en grundläggande programgateway med standardinställningar för lyssnaren, backend-poolen, http-inställningar och regler för säkerhetskopiering. Du kan ändra dessa inställningar så att de passar distributionen när etableringen har lyckats.
Om du redan har definierat webbprogrammet med backend-poolen i föregående steg börjar belastningsutjämningen när det har skapats.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar anpassade hälsoavsökningar genom att besöka [Skapa en anpassad hälsoavsökning](application-gateway-create-probe-portal.md)

Lär dig hur du konfigurerar TLS-avlastning och tar bort den kostsamma TLS-dekrypteringen från webbservrarna genom att besöka [Konfigurera TLS-avlastning](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
