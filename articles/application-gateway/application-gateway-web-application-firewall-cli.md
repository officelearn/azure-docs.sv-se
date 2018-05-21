---
title: Skapa en Programgateway med en brandvägg för webbaserade program - Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en Programgateway med en brandvägg för webbaserade program med hjälp av Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 1a0691f51071ec4e983727967790367507ef40fd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-cli"></a>Skapa en Programgateway med en brandvägg för webbaserade program med Azure CLI

Du kan använda Azure CLI för att skapa en [Programgateway](application-gateway-introduction.md) med en [Brandvägg för webbaserade program](application-gateway-web-application-firewall-overview.md) (Brandvägg) som använder en [virtuella datorns skaluppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Brandvägg använder [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) regler för att skydda ditt program. Dessa regler innehåller skydd mot attacker, till exempel SQL injection, webbplatser scripting attacker och sessionen hijacks. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en Programgateway med Brandvägg aktiverat
> * Skapa en VM-skalningsuppsättning
> * Skapa ett lagringskonto och konfigurera diagnostik

![Exempel på brandväggen ett webbprogram](./media/application-gateway-web-application-firewall-cli/scenario-waf.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Skapa en Azure-resursgrupp med namnet *myResourceGroupAG* med [az gruppen skapa](/cli/azure/group#az_group_create).

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Virtuella nätverk och undernät används för att ge nätverksanslutning till gateway för programmet och dess associerade resurser. Skapa ett virtuellt nätverk med namnet *myVNet* och undernät med namnet *myAGSubnet* med [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create) och [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Skapa en offentlig IP-adress med namnet *myAGPublicIPAddress* med [az nätverket offentliga IP-skapa](/cli/azure/network/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway-with-a-waf"></a>Skapa en Programgateway med en Brandvägg

Du kan använda [az nätverket Programgateway skapa](/cli/azure/application-gateway#az_application_gateway_create) att skapa Programgateway med namnet *myAppGateway*. När du skapar en Programgateway med hjälp av Azure CLI kan ange du konfigurationsinformation, till exempel kapacitet, sku och HTTP-inställningar. Programgatewayen har tilldelats *myAGSubnet* och *myPublicIPSddress* som du skapade tidigare.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Det kan ta flera minuter för Programgateway ska skapas. När programgatewayen har skapats kan se du de här nya funktionerna i den:

- *appGatewayBackendPool* -en Programgateway måste ha minst en backend-adresspool.
- *appGatewayBackendHttpSettings* -anger att port 80 och en HTTP-protokollet används för kommunikation.
- *appGatewayHttpListener* -standard lyssnaren som är associerade med *appGatewayBackendPool*.
- *appGatewayFrontendIP* -tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* - standard routning regel som är associerad med *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en skaluppsättning för virtuell dator som ger två servrar för serverdelspoolen i programgatewayen. De virtuella datorerna i skaluppsättning som är associerade med den *myBackendSubnet* undernät. Att skapa skala, kan du använda [az vmss skapa](/cli/azure/vmss#az_vmss_create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installera NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Skapa ett lagringskonto och konfigurera diagnostik

I den här kursen använder programgatewayen ett lagringskonto för att lagra data för att upptäcka och förhindra syften. Du kan också använda logganalys eller Händelsehubb för att registrera data. 

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett lagringskonto med namnet *myagstore1* med [az storage-konto skapar](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption blob
```

### <a name="configure-diagnostics"></a>Konfigurera diagnostik

Konfigurera diagnostik för att registrera data i ApplicationGatewayAccessLog och ApplicationGatewayPerformanceLog ApplicationGatewayFirewallLog loggar. Ersätt `<subscriptionId>` med prenumerations-ID och sedan konfigurera diagnostik med [az diagnostik-inställningar för övervakning av skapa](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)
storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)
az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Offentliga IP-adressen för programgatewayen får använda [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testa bas-URL i Programgateway](./media/application-gateway-web-application-firewall-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en Programgateway med Brandvägg aktiverat
> * Skapa en VM-skalningsuppsättning
> * Skapa ett lagringskonto och konfigurera diagnostik

Mer information om programgatewayer och deras associerade resurser fortsätter du att artiklarna.