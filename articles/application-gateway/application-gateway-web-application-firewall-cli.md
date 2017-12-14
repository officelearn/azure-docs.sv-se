---
title: "Konfigurera brandväggar för webbaserade program: Azure Programgateway | Microsoft Docs"
description: "Den här artikeln innehåller råd om hur du startar med en brandvägg för webbaserade program på en befintlig eller ny application gateway."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu
ms.openlocfilehash: e60bfc89378569b154f4f973d1dceb683fa58482
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Konfigurera brandväggar för webbaserade program på en ny eller befintlig Programgateway med Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Lär dig hur du skapar en brandvägg för webbaserade program (Brandvägg)-aktiverat Programgateway. Lär dig också att lägga till en Brandvägg i en befintlig gateway för programmet.

Brandvägg i Azure Application Gateway skyddar webbprogram från vanliga webbaserade attacker som SQL injection, webbplatser scripting attacker och sessionen hijacks.

 Programgateway är en lager 7 belastningsutjämnare. Det ger redundans, prestanda-routing HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt. Programgateway innehåller många funktioner i programmet leverans nätverksstyrenheten (ADC):

 * HTTP för belastningsutjämning 
 * Cookie-baserad session tillhörighet 
 * Secure Sockets Layer (SSL)-avlastning 
 * Anpassade hälsoavsökningar 
 * Stöd för multisitefunktionen
 
 En fullständig lista över funktioner som stöds finns i [översikt för Programgateway](application-gateway-introduction.md).

Den här artikeln visar hur du [lägga till en brandvägg för webbaserade program i en befintlig Programgateway](#add-web-application-firewall-to-an-existing-application-gateway). Den visar även hur du [skapa en Programgateway som använder en brandvägg för webbaserade program](#create-an-application-gateway-with-web-application-firewall).

![scenario-bild][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Förutsättning: Installera Azure CLI 2.0

Om du vill utföra stegen i den här artikeln, måste du [installera Azure-kommandoradsgränssnittet (Azure CLI) för Mac, Linux och Windows](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Brandvägg configuration skillnader

Om du har läst [skapa en Programgateway med Azure CLI](application-gateway-create-gateway-cli.md), du förstår SKU-inställningar för att konfigurera när du skapar en Programgateway. Brandvägg ger ytterligare inställningar för att definiera när du konfigurerar SKU: N på en Programgateway. Det finns inga ytterligare ändringar som du gör på Programgateway sig själv.

| **Inställning** | **Detaljer**
|---|---|
|**SKU** |Har stöd för en normal Programgateway utan en Brandvägg **Standard\_små**, **Standard\_medel**, och **Standard\_stor**storlekar. Det finns två ytterligare SKU: er, med introduktionen av en Brandvägg, **Brandvägg\_medel** och **Brandvägg\_stor**. En Brandvägg stöds inte på små programgatewayer.|
|**Läge** | Den här inställningen är läget för Brandvägg. tillåtna värden är **identifiering** och **förebyggande**. När Brandvägg ställs in i **identifiering** läge måste alla hot som lagras i en loggfil. I **förebyggande** läge, händelser loggas fortfarande, men angriparen tar emot ett 403 obehörig svar från programgatewayen.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Lägg till en brandvägg för webbaserade program i en befintlig Programgateway

Följande kommando för att ändra en befintlig standard Programgateway till en Brandvägg-aktiverat Programgateway:

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Det här kommandot uppdaterar programgatewayen med en Brandvägg. Information om hur du visar loggar för din Programgateway finns [Programgateway diagnostik](application-gateway-diagnostics.md). Granska loggarna regelbundet för att förstå säkerhetstillståndet webbprogram på grund av säkerhet hur av en Brandvägg.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Skapa en Programgateway med en brandvägg för webbaserade program

Följande kommando skapar en Programgateway med en Brandvägg:

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Programgatewayer som skapats med den grundläggande konfigurationen av Brandvägg har konfigurerats med CR 3.0 för skydd.

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett program gateway DNS-namn

När du har skapat, är nästa steg att konfigurera klientdelen för kommunikation. När du använder en offentlig IP-adress, kräver en dynamiskt tilldelad DNS-namn som inte är eget programgatewayen. För att säkerställa att användarna kan träffa programgatewayen, använder du en CNAME-post för att peka till offentlig slutpunkt för programgatewayen. Mer information finns i [konfigurera ett anpassat domännamn för en Azure-molntjänst](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Hämta information om programgatewayen och dess tillhörande IP DNS-namn om du vill konfigurera en CNAME-post med hjälp av PublicIPAddress-element som är kopplade till programgatewayen. Använda den Programgateway DNS-namn för att skapa en CNAME-post som pekar på två webbprogram till DNS-namnet. Vi rekommenderar inte med poster, eftersom VIP kan ändras när programgatewayen startas om.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
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

Information om hur du anpassar Brandvägg regler finns [anpassa web application brandväggsregler via Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png
