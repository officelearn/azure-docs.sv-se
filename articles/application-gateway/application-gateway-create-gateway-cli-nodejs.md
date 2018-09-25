---
title: Skapa ett Azure Application Gateway – klassisk Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en Programgateway med hjälp av den klassiska Azure CLI i Resource Manager
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: victorh
ms.openlocfilehash: e834b1633f17ecec74ae17e962de445ad8d6dccd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974433"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Skapa en Programgateway med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-gateway.md)
> * [Azure Resource Manager-mall](application-gateway-create-gateway-arm-template.md)
> * [Klassisk Azure CLI](application-gateway-create-gateway-cli.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)
> 
> 

Azure Application Gateway är en Layer 7-lastbalanserare. Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt. Application gateway tillhandahåller de följande funktionerna för programleverans: HTTP läsa in belastningsutjämning, cookie-baserad sessionstillhörighet och Secure Sockets Layer (SSL) avlastning, anpassade hälsoavsökningar och stöd för flera platser.

## <a name="prerequisite-install-the-azure-cli"></a>Förutsättning: Installera Azure CLI

Om du vill utföra stegen i den här artikeln, måste du [installera Azure CLI](../xplat-cli-install.md) och du behöver [logga in på Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Om du inte har ett Azure-konto, behöver du en. Registrera dig för en [kostnadsfri utvärderingsversion här](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Scenario

I det här scenariot kan du lära dig hur du skapar en Programgateway med hjälp av Azure portal.

Det här scenariot kommer att:

* Skapa en medelstor Programgateway med två instanser.
* Skapa ett virtuellt nätverk med namnet ContosoVNET med det reserverade CIDR-blocket 10.0.0.0/16.
* Skapa ett undernät med namnet subnet01 som använder 10.0.0.0/28 som dess CIDR-block.

> [!NOTE]
> Ytterligare konfiguration av application gateway, inklusive anpassade hälsotillstånd avsökningar, adresser för backend-poolen och ytterligare regler konfigureras när application gateway har konfigurerats och inte under den inledande distributionen.

## <a name="before-you-begin"></a>Innan du börjar

Azure Application Gateway kräver ett eget undernät. När du skapar ett virtuellt nätverk kan du se till att lämna tillräckligt med adressutrymme för att du har flera undernät. När du distribuerar en application gateway till ett undernät kan kan bara ytterligare programgatewayer läggas till i undernätet.

## <a name="log-in-to-azure"></a>Logga in på Azure

Öppna den **Kommandotolken för Microsoft Azure**, och logga in. 

```azurecli-interactive
azure login
```

När du skriver i föregående exempel finns en kod. Gå till https://aka.ms/devicelogin i en webbläsare för att fortsätta inloggningen.

![cmd som visar inloggning på enhet][1]

Ange koden som du fick i webbläsaren. Du omdirigeras till en inloggningssida.

![webbläsare för att ange koden][2]

När koden har skrivits in du är inloggad, Stäng webbläsaren att fortsätta med scenariot.

![har loggat in][3]

## <a name="switch-to-resource-manager-mode"></a>Växla till Resource Manager-läge

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Innan du skapar programgatewayen skapas en resursgrupp för application gateway. Nedan visas kommandot.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

När resursgruppen skapas, skapas ett virtuellt nätverk för application gateway.  I följande exempel var adressutrymmet 10.0.0.0/16 enligt definitionen i den föregående scenario-informationen.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Skapa ett undernät

När det virtuella nätverket skapas, läggs ett undernät för application gateway.  Om du planerar att använda programgatewayen med en webbapp som finns i samma virtuella nätverk som application gateway, måste du lämna tillräckligt med utrymme för ett annat undernät.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

När det virtuella nätverk och undernät har skapats, är krav för application gateway klar. Dessutom ett tidigare exporterade PFX-certifikat och lösenord för certifikatet måste anges för följande steg: IP-adresser som används för serverdelen är IP-adresser för backend-servern. Dessa värden kan vara antingen privata IP-adresser i det virtuella nätverket, offentliga IP-adresser eller fullständigt kvalificerat domännamn för backend-servrarna.

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
> En lista över parametrar som kan anges när du skapar kör du följande kommando: **azure network application-gateway create--hjälpa**.

Det här exemplet skapar en basic-Programgateway med standardinställningarna för lyssnare, serverdelspoolen, serverdelens http-inställningar och regler. Du kan ändra dessa inställningar så att de passar din distribution när etableringen är klar.
Om du redan har ditt webbprogram som definierats med backend-pool i föregående steg skapade, börjar Utjämning av nätverksbelastning.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar anpassade hälsoavsökningar genom att besöka [skapa en anpassad hälsoavsökning](application-gateway-create-probe-portal.md)

Lär dig hur du konfigurerar SSL-avlastning och ta kostsamma SSL-dekryptering åt dina webbservrar genom att besöka [Konfigurera SSL-avlastning](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
