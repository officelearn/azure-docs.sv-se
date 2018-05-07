---
title: Skapa en anpassad avsökningsåtgärd - Azure Application Gateway - PowerShell klassisk | Microsoft Docs
description: Lär dig hur du skapar en anpassad avsökningsåtgärd för Programgateway med PowerShell i den klassiska distributionsmodellen
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 338a7be1-835c-48e9-a072-95662dc30f5e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 97d1376dc7908b72d8e8ec15145229cf3cf4acae
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Skapa en anpassad avsökningsåtgärd för Azure-Programgateway (klassiskt) med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln, lägger du till en anpassad avsökning en befintlig Programgateway med PowerShell. Anpassade avsökningar är användbara för program som har ett visst hälsotillstånd Kontrollera sidan eller för program som inte tillhandahåller ett lyckat svar på standardwebbprogrammet.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Så här skapar du en programgateway:

1. Skapa en resurs för en programgateway.
2. Skapa en XML-konfigurationsfil eller ett konfigurationsobjekt.
3. Bekräfta konfigurationen för den nyligen skapade programgatewayresursen.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Skapa ett program gateway med en anpassad avsökningsåtgärd

Du skapar en gateway genom att köra cmdleten `New-AzureApplicationGateway`, där du ersätter värdena med dina egna. Faktureringen för gatewayen startar inte i det här läget. Faktureringen börjar i ett senare skede när gatewayen har startats.

I följande exempel skapar vi en programgateway genom att använda ett virtuellt nätverk med namnet ”testvnet1” och undernätet ”subnet-1”.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Du kontrollerar att gatewayen har skapats genom att köra cmdleten `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Standardvärdet för *InstanceCount* är 2, och det högsta värdet är 10. Standardvärdet för *GatewaySize* är Medium. Du kan välja mellan små, medelstora och stora.
> 
> 

*VirtualIPs* och *DnsName* är tomma eftersom gatewayen inte har startat än. Dessa värden skapas när den är i körläge.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurera en Programgateway genom att använda XML

I följande exempel använder vi en XML-fil för att konfigurera alla inställningar för programgatewayen och för att skicka dem till programgatewayresursen.  

Kopiera följande text till Anteckningar.

```xml
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Redigera värdena mellan parenteserna för konfigurationsobjekten. Spara filen med filnamnstillägget .xml.

I följande exempel visas hur du använder en konfigurationsfil för att ställa in Programgateway belastningsutjämna HTTP-trafik på offentlig port 80 och skicka trafik till backend-port 80 mellan två IP-adresser med hjälp av en anpassad avsökning.

> [!IMPORTANT]
> Protokollobjekten Http och Https är skiftlägeskänsliga.

Ett nytt konfigurationsobjekt \<avsökning\> har lagts till i Konfigurera anpassade avsökningar.

Konfigurationsparametrarna är:

|Parameter|Beskrivning|
|---|---|
|**Namn** |Referensnamn för anpassade avsökningen. |
* **Protokollet** | Protokoll som används (möjliga värden är HTTP eller HTTPS).|
| **Värden** och **sökväg** | Fullständiga URL-sökväg som anropas av Programgateway fastställa hälsotillståndet för instansen. Om du har en webbplats till exempel http://contoso.com/, och sedan den anpassa avsökningen kan konfigureras för ”http://contoso.com/path/custompath.htm” för avsökning kontrollerar att ett lyckat HTTP-svar.|
| **Intervall** | Konfigurerar kontroller för avsökning intervall i sekunder.|
| **Timeout** | Definierar avsökningen timeout-värdet för en kontroll för HTTP-svar.|
| **UnhealthyThreshold** | Antal misslyckade HTTP-svar som behövs för att flaggan backend-instans som *ohälsosamt*.|

Avsökningsnamnet refererar till den \<BackendHttpSettings\> konfigurationen att tilldela vilka backend-adresspoolen använder anpassad avsökningsåtgärd inställningar.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Lägg till en anpassad avsökning i en befintlig Programgateway

Ändra den aktuella konfigurationen av en Programgateway kräver tre steg: hämta den aktuella XML-konfigurationsfilen, ändra om du vill att en anpassad avsökning och konfigurera programgatewayen med de nya inställningarna för XML.

1. Hämta XML-filen genom att använda `Get-AzureApplicationGatewayConfig`. Denna cmdlet exporterar konfigurations-XML som ska ändras för att lägga till någon avsökningsinställning.

  ```powershell
  Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
  ```

1. Öppna XML-filen i en textredigerare. Lägg till en `<probe>` avsnittet efter `<frontendport>`.

  ```xml
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
</Probes>
  ```

  Lägg till avsökningsnamnet i avsnittet backendHttpSettings i XML-koden som visas i följande exempel:

  ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
  ```

  Spara XML-filen.

1. Uppdatera gateway programkonfigurationen med den nya XML-filen genom att använda `Set-AzureApplicationGatewayConfig`. Denna cmdlet uppdaterar din Programgateway med den nya konfigurationen.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera Secure Sockets Layer (SSL)-avlastning, se [konfigurera en Programgateway för SSL-avlastning](application-gateway-ssl.md).

Om du vill konfigurera en programgateway för användning med en intern belastningsutjämnare läser du [Skapa en programgateway med en intern belastningsutjämnare (ILB)](application-gateway-ilb.md).

