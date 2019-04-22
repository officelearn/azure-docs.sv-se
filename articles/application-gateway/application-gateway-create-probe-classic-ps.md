---
title: Skapa en anpassad avsökning – Azure Application Gateway – PowerShell – klassisk | Microsoft Docs
description: Lär dig hur du skapar en anpassad avsökning för Programgateway med hjälp av PowerShell i den klassiska distributionsmodellen
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
ms.openlocfilehash: 01c1768f60da98206f0dfd041745428256f545fc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861887"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Skapa en anpassad avsökning för Azure Application Gateway (klassisk) med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln får du till en anpassad avsökning i en befintlig application gateway med PowerShell. Anpassade avsökningar är användbara för program som har en specifik hälsokontrollsida eller för program som inte uppger ett lyckat svar på standardwebbprogrammet.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Skapa en Application Gateway

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
> Standardvärdet för *InstanceCount* är 2, och det högsta värdet är 10. Standardvärdet för *GatewaySize* är Medium. Du kan välja mellan Small, Medium eller Large.
> 
> 

*VirtualIPs* och *DnsName* är tomma eftersom gatewayen inte har startat än. Dessa värden skapas när gatewayen är i körläge.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurera en Programgateway med hjälp av XML

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

I följande exempel visas hur du använder en konfigurationsfil för att ställa in application-gateway för att belastningsutjämna HTTP-trafik på den offentliga porten 80 och skickar nätverkstrafik till backend-port 80 mellan två IP-adresser med hjälp av en anpassad avsökning.

> [!IMPORTANT]
> Protokollobjekten Http och Https är skiftlägeskänsliga.

Ett nytt konfigurationsobjekt \<avsökning\> läggs till i Konfigurera anpassade avsökningar.

Konfigurationsparametrarna är:

|Parameter|Beskrivning|
|---|---|
|**Namn** |Referensnamn för anpassad avsökning. |
| **Protokoll** | Protokoll som används (möjliga värden är HTTP eller HTTPS).|
| **Värden** och **sökväg** | Fullständig URL-sökväg som anropas av application gateway kan fastställa hälsotillståndet för instansen. Exempel: Om du har en webbplats http:\//contoso.com/ och anpassad avsökning kan konfigureras för ”http:\//contoso.com/path/custompath.htm” för avsökningen söker ha ett lyckat HTTP-svar.|
| **Intervall** | Konfigurerar hälsoavsökning för intervall i sekunder.|
| **Timeout** | Definierar avsökningen timeout-värde för en kontroll av HTTP-svar.|
| **UnhealthyThreshold** | Antalet misslyckade HTTP-svar som behövs för att flaggan backend-instans som *feltillstånd*.|

Avsökningsnamnet refereras till i den \<BackendHttpSettings\> konfiguration för att tilldela vilken backend-poolen använder inställningar för anpassad avsökning.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Lägg till en anpassad avsökning i en befintlig Programgateway

Ändra den aktuella konfigurationen av en application gateway kräver tre steg: Hämta den aktuella XML-konfigurationsfilen, ändra om du vill ha en anpassad avsökning och konfigurera programgatewayen med de nya inställningarna för XML.

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

1. Uppdatera application gateway-konfigurationen med den nya XML-filen med hjälp av `Set-AzureApplicationGatewayConfig`. Den här cmdleten uppdaterar din application gateway med den nya konfigurationen.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera Secure Sockets Layer (SSL) avlastning läser [konfigurera en Programgateway för SSL-avlastning](application-gateway-ssl.md).

Om du vill konfigurera en programgateway för användning med en intern lastbalanserare läser du [Skapa en programgateway med en intern lastbalanserare (ILB)](application-gateway-ilb.md).

