---
title: Konfigurera SSL-avlastning - Azure Application Gateway - Azure CLI 2.0 | Microsoft Docs
description: "Den här artikeln innehåller instruktioner för att skapa en Programgateway med SSL-avlastning med hjälp av Azure CLI 2.0"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 4bdca33dae2ce52fdeccdae9a67abb6667593f9d
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-cli-20"></a>Konfigurera en Programgateway för SSL-avlastning med hjälp av Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure klassiska PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway kan konfigureras att avsluta SSL-sessionen (Secure Sockets Layer) på gatewayen så att du undviker kostsamma SSL-dekrypteringsaktiviteter i webbservergruppen. SSL-avlastning förenklar också certifikathantering på frontend-servern.

## <a name="prerequisite-install-the-azure-cli-20"></a>Förutsättning: Installera Azure CLI 2.0

Om du vill utföra stegen i den här artikeln, måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="required-components"></a>Nödvändiga komponenter

* **Backend-serverpoolen**: listan över IP-adresser för backend-servrar. IP-adresser i listan ska tillhöra undernät för virtuellt nätverk eller ska vara en offentlig IP-adress eller en virtuell IP-adress (VIP).
* **Backend-server poolinställningarna**: varje pool har inställningar som port och protokoll cookie-baserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Frontend-port**: den här porten är den offentliga som öppnas på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare**: lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa inställningar är skiftlägeskänsligt), och namnet på SSL (om hur du konfigurerar SSL-avlastning).
* **Regeln**: regeln Binder lyssnaren och backend-serverpoolen och definierar vilka backend-server-pool för att dirigera trafik till när den når en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

**Information om ytterligare konfiguration**

För konfiguration av SSL-certifikat bör protokollet i **HttpListener** ändras till *Https* (skiftlägeskänsligt). Lägg till den **SslCertificate** elementet så att **HttpListener** med variabelns värde som konfigurerats för SSL-certifikatet. Frontend-port som ska uppdateras till **443**.

**Så här aktiverar du cookie-baserad tillhörighet**: du kan konfigurera en Programgateway för att säkerställa att en begäran från en klientsession alltid dirigeras till samma virtuella dator i en webbservergrupp. Infoga en sessions-cookie som gör att en gateway att dirigera trafik på lämpligt sätt för att åstadkomma detta. Du kan aktivera cookiebaserad tillhörighet genom att ange **CookieBasedAffinity** till *Enabled* i elementet **BackendHttpSettings**.

## <a name="configure-ssl-offload-on-an-existing-application-gateway"></a>Konfigurera SSL-avlastning på en befintlig Programgateway

Ange följande kommandon för att konfigurera SSL-avlastning till en befintlig Programgateway:

```azurecli-interactive
#!/bin/bash

# Create a new front end port to be used for SSL
az network application-gateway frontend-port create \
  --name sslport \
  --port 443 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Upload the .pfx certificate for SSL offload
az network application-gateway ssl-cert create \
  --name "newcert" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new listener referencing the port and certificate created earlier
az network application-gateway http-listener create \
  --frontend-ip "appGatewayFrontendIP" \
  --frontend-port sslport  \
  --name sslListener \
  --ssl-cert newcert \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new back-end pool to be used
az network application-gateway address-pool create \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG" \
  --name "appGatewayBackendPool2" \
  --servers 10.0.0.7 10.0.0.8

# Create a new back-end HTTP settings using the new probe
az network application-gateway http-settings create \
  --name "settings2" \
  --port 80 \
  --cookie-based-affinity Enabled \
  --protocol "Http" \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

# Create a new rule linking the listener to the back-end pool
az network application-gateway rule create \
  --name "rule2" \
  --rule-type Basic \
  --http-settings settings2 \
  --http-listener ssllistener \
  --address-pool temp1 \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"

```

## <a name="create-an-application-gateway-with-ssl-offload"></a>Skapa en Programgateway med SSL-avlastning

I följande exempel skapar en Programgateway med SSL-avlastning. Certifikat och lösenord för certifikatet måste uppdateras till en giltig privat nyckel.

```azurecli-interactive
#!/bin/bash

# Creates an application gateway with SSL offload
az network application-gateway create \
  --name "AdatumAppGateway3" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG2" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --cert-file /home/azureuser/self-signed/AdatumAppGatewayCert.pfx \
  --cert-password P@ssw0rd \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet" \
  --subnet-address-prefix "10.0.0.0/28" \
  --frontend-port 443 \
  --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 \
  --sku "Standard_Small" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip" \
  --public-ip-address-allocation "dynamic"
```

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett program gateway DNS-namn

När du har skapat, är nästa steg att konfigurera klientdelen för kommunikation.  Programgateway kräver en dynamiskt tilldelad DNS-namn när du använder en offentlig IP-adress som inte är eget. För att säkerställa att användarna kan trycka programgatewayen, kan du använda en CNAME-post så att den pekar till offentlig slutpunkt för programgatewayen. Mer information finns i [konfigurera ett anpassat domännamn för i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Om du vill konfigurera ett alias, hämta information om programgatewayen och dess associerade IP/DNS-namn med hjälp av den **PublicIPAddress** element som är kopplade till programgatewayen. Använda den Programgateway DNS-namn för att skapa en CNAME-post som pekar på två webbprogram till DNS-namnet. Vi rekommenderar inte användning av A-poster eftersom VIP kan ändra på omstart av programgatewayen.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera en Programgateway du använder med en intern belastningsutjämnare finns [skapa en Programgateway med en intern belastningsutjämnare](application-gateway-ilb.md).

Mer information om alternativen för belastningsutjämning i allmänhet finns:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
