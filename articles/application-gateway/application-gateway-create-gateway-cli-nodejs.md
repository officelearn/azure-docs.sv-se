---
title: Skapa en Azure Programgateway - Azure CLI 1.0 | Microsoft Docs
description: "Lär dig hur du skapar en Programgateway med hjälp av Azure CLI 1.0 i Resource Manager"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: fe50fb3a7434702101dc5ae7a9dd176a33423119
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Skapa en Programgateway med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-gateway.md)
> * [Azure Resource Manager-mall](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)
> 
> 

Azure Application Gateway är en Layer 7-belastningsutjämnare. Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt. Programgateway innehåller följande funktioner för leverans av program: HTTP läsa in belastningsutjämning, cookie-baserad session tillhörighet och Secure Sockets Layer (SSL)-avlastning, anpassade hälsoavsökningar och stöd för flera platser.

## <a name="prerequisite-install-the-azure-cli"></a>Förutsättning: Installera Azure CLI

Om du vill utföra stegen i den här artikeln, måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (Azure CLI)](../xplat-cli-install.md) och du behöver [logga in på Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Om du inte har ett Azure-konto behöver du en. Registrera dig för en [kostnadsfri utvärderingsversion här](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scenario

Lär dig hur du skapar en Programgateway med Azure-portalen i det här scenariot.

Det här scenariot kommer:

* Skapa en medelhög Programgateway med två instanser.
* Skapa ett virtuellt nätverk med namnet ContosoVNET med en 10.0.0.0/16 reserverade CIDR-blocket.
* Skapa ett undernät som kallas subnet01 som använder 10.0.0.0/28 som sitt CIDR-block.

> [!NOTE]
> Ytterligare konfiguration för Programgateway, inklusive anpassade hälsa avsökningar, backend-adresser för poolen och ytterligare regler konfigureras när programgatewayen har konfigurerats och inte under första distributionen.

## <a name="before-you-begin"></a>Innan du börjar

Azure Application Gateway kräver sin egen undernät. När du skapar ett virtuellt nätverk, se till att du lämnar tillräckligt med adressutrymme för att du har flera undernät. När du distribuerar en Programgateway till ett undernät kan bara ytterligare programgatewayer som ska läggas till undernätet.

## <a name="log-in-to-azure"></a>Logga in på Azure

Öppna den **Kommandotolken för Microsoft Azure**, och logga in. 

```azurecli-interactive
azure login
```

När du skriver i föregående exempel, har en kod angetts. Navigera till https://aka.ms/devicelogin i en webbläsare för att fortsätta inloggningen.

![cmd visar enhet inloggning][1]

Ange den kod som du fick i webbläsaren. Du omdirigeras till en inloggningssida.

![webbläsare för att ange koden][2]

När du har angett koden du är inloggad, Stäng webbläsaren för att fortsätta scenariot.

![loggat in][3]

## <a name="switch-to-resource-manager-mode"></a>Växla till läget Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Innan du skapar programgatewayen, skapas en resursgrupp för programgatewayen. Nedan visas kommandot.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

När resursgruppen har skapats, skapas ett virtuellt nätverk för programgatewayen.  I följande exempel var adressutrymmet som 10.0.0.0/16 som definierats i föregående scenariot anteckningar.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Skapa ett undernät

När det virtuella nätverket har skapats läggs ett undernät för Programgateway.  Om du planerar att använda Programgateway med en webbapp som finns i samma virtuella nätverk som programgatewayen bör du lämna tillräckligt med utrymme för ett annat undernät.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

När det virtuella nätverk och undernät skapas, är kraven för Programgateway fullständiga. Dessutom ett tidigare exporterade PFX-certifikat och lösenord för certifikatet som krävs för följande steg: IP-adresser som används för backend är IP-adresser för backend-servern. Dessa värden kan vara privata IP-adresser i det virtuella nätverket, offentliga IP-adresser eller fullständigt kvalificerat domännamn för backend-servrar.

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
> En lista över parametrar som kan tillhandahållas vid skapandet kör följande kommando: **azure-nätverk Programgateway skapa--hjälpa**.

Det här exemplet skapar en basic-Programgateway med standardinställningarna för lyssnare, serverdelspool, serverdelens http-inställningar och regler. Du kan ändra dessa inställningar efter distributionen när etableringen har lyckats.
Om du redan har ditt webbprogram som definierats med serverdelspoolen i föregående steg, skapas en gång, börjar belastningsutjämning.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar anpassade hälsoavsökningar genom att besöka [skapa en anpassad hälsoavsökningen](application-gateway-create-probe-portal.md)

Lär dig hur du konfigurerar SSL-avlastning och ta kostsamma SSL-dekryptering av webbservrar genom att besöka [Konfigurera SSL-avlastning](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
