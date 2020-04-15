---
title: Skapa en anpassad avsökning med PowerShell - Azure Application Gateway
description: Lär dig hur du skapar en anpassad avsökning för Application Gateway med hjälp av PowerShell i den klassiska distributionsmodellen
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 0ba3e9ae7b5075d1f5457cb2960423ad1c737e94
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312561"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Skapa en anpassad avsökning för Azure Application Gateway (klassisk) med powershell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln lägger du till en anpassad avsökning i en befintlig programgateway med PowerShell. Anpassade avsökningar är användbara för program som har en specifik hälsokontrollsida eller för program som inte ger ett lyckat svar i standardwebbprogrammet.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Så här skapar du en programgateway:

1. Skapa en resurs för en programgateway.
2. Skapa en XML-konfigurationsfil eller ett konfigurationsobjekt.
3. Bekräfta konfigurationen för den nyligen skapade programgatewayresursen.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Skapa en programgatewayresurs med en anpassad avsökning

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
> Standardvärdet för *InstanceCount* är 2, och det högsta värdet är 10. Standardvärdet för *GatewaySize* är Medium. Du kan välja mellan Liten, Medel och Stor.
> 
> 

*VirtualIPs* och *DnsName* är tomma eftersom gatewayen inte har startat än. Dessa värden skapas när gatewayen är i körläge.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurera en programgateway med XML

I följande exempel använder vi en XML-fil för att konfigurera alla inställningar för programgatewayen och för att skicka dem till programgatewayresursen.  

Kopiera följande text till Anteckningar.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

I följande exempel visas hur du använder en konfigurationsfil för att ställa in programgatewayen för att läsa in aldo HTTP-trafik på offentlig port 80 och skicka nätverkstrafik till serverdelsport 80 mellan två IP-adresser med hjälp av en anpassad avsökning.

> [!IMPORTANT]
> Protokollobjekten Http och Https är skiftlägeskänsliga.

En ny \<konfigurationsartikelAvsökning\> läggs till för att konfigurera anpassade avsökningar.

Konfigurationsparametrarna är:

|Parameter|Beskrivning|
|---|---|
|**Namn** |Referensnamn för anpassad avsökning. |
| **Protokollet** | Protokoll som används (möjliga värden är HTTP eller HTTPS).|
| **Värd** och **sökväg** | Fullständig URL-sökväg som anropas av programgatewayen för att fastställa instansens hälsotillstånd. Om du till exempel har\/en webbplats http: /contoso.com/ kan den anpassade avsökningen konfigureras för "http:\//contoso.com/path/custompath.htm" för att avsökningskontroller ska ha ett lyckat HTTP-svar.|
| **Intervall** | Konfigurerar avsökningsintervallet kontroller i sekunder.|
| **Timeout** | Definierar timeout för avsökningen för en HTTP-svarskontroll.|
| **OhälsosamtDetrösst** | Antalet misslyckade HTTP-svar som behövs för att flagga backend-instansen som *felaktig*.|

Avsökningsnamnet refereras \<i backendHttpSettings-konfigurationen\> för att tilldela vilken backend-pool som använder anpassade avsökningsinställningar.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Lägga till en anpassad avsökning i en befintlig programgateway

För att ändra den aktuella konfigurationen för en programgateway krävs tre steg: Hämta den aktuella XML-konfigurationsfilen, ändra för att ha en anpassad avsökning och konfigurera programgatewayen med de nya XML-inställningarna.

1. Hämta XML-filen `Get-AzureApplicationGatewayConfig`med hjälp av . Den här cmdleten exporterar konfigurations-XML som ska ändras för att lägga till en avsökningsinställning.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Öppna XML-filen i en textredigerare. Lägg `<probe>` till `<frontendport>`ett avsnitt efter .

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

   Lägg till avsökningsnamnet som visas i följande exempel i avsnittet Serverdelhttpsettings i XML:n:

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

1. Uppdatera konfigurationen för programgateway med `Set-AzureApplicationGatewayConfig`den nya XML-filen med hjälp av . Den här cmdleten uppdaterar programgatewayen med den nya konfigurationen.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera avlastning av Transport Layer Security (TLS), tidigare kallat SSL (Secure Sockets Layer), läser [du Konfigurera en programgateway för TLS-avlastning](application-gateway-ssl.md).

Om du vill konfigurera en programgateway för användning med en intern lastbalanserare läser du [Skapa en programgateway med en intern lastbalanserare (ILB)](application-gateway-ilb.md).

