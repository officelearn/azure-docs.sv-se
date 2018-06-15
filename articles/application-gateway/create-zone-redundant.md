---
title: Skapa en gateway för zonen redundant Azure-program
description: Lär dig hur du skapar en zon redundant application gateway som inte kräver en seperarte gateway i varje zon.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937758"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Skapa en zon redundant Azure-Programgateway - privat förhandsgranskning

Programmet gateway zonen redundant plattformen är en ny SKU som erbjuder många förbättringar över det befintliga programmet gateway SKU inklusive:
- **Zonen återhämtning** – en programdistribution för gateway kan nu sträcka sig över flera tillgänglighet zoner, eliminerar behovet av att etablera och PIN-kod separat Programgateway instanser i varje zon med en traffic manager. Du kan välja en zon eller till flera zoner där gateway programinstanser har distribuerats, därför att se till att zonen fel återhämtning. Serverdelspoolen för program kan distribueras på samma sätt över tillgänglighet zoner.
- **Prestandaförbättringar**
- **Statisk VIP** -programgatewayen VIP stöder nu statiska VIP-typen exklusivt. Detta säkerställer att VIP som är associerade med Programgateway inte ändras efter en omstart.
- **Key vault-integrering för kunden SSL-certifikat**
- **Snabbare distribution och uppdatera**

> [!NOTE]
> Zonen redundant programgatewayen är för tillfället i privat förhandsvisning. Den här förhandsgranskningen tillhandahålls utan ett serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad kapacitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Regioner som stöds

Zonen redundant programgatewayer stöds för närvarande i östra USA 2. Flera områden läggs snart.

## <a name="topologies"></a>Topologier

Med den aktuella versionen kan behöver du inte längre skapa zonen Fäst programgatewayer för att få zonal redundans. Samma gateway programdistributionen kan nu sträcka sig över flera zoner.

Minst tre instanser krävs för att säkerställa att de är fördelade på alla tre zoner. Programgateway distribuerar instanser i zoner som läggs till flera instanser.

Tidigare zonen redundant topologier såg ut som i följande diagram:

![Gamla redundant topologi](media/create-zone-redundant/old-redundant.png)

Den nya zon redundant topologin ser ut som i det här diagrammet:

![Ny zon redundant topologi](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Distribution

### <a name="prerequisites"></a>Förutsättningar

Zonen redundant funktionen är för närvarande bara tillgänglig i privat förhandsvisning. Du måste skicka e-post appgwxzone@microsoft.com vara godkända. När du har fått en bekräftelse kan du fortsätta till nästa steg. Inkludera följande information i din e-post för vitlistning:

- Prenumerations-ID:t
- Regionnamn
- Ungefärligt antal programgatewayer krävs

### <a name="resource-manager-template-deployment"></a>Resource Manager för malldistribution

1. Kontrollera att den prenumeration som du använder är godkända, som tidigare nämnts.
2. När ett bekräftelsemeddelande logga in på Azure-konto och väljer lämpliga prenumerationen om det finns mer än en prenumeration. Kontrollera att du väljer den prenumeration som är godkända.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Skapa en ny resursgrupp

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. Hämta mallar från [GitHub](https://github.com/amitsriva/CrossZonePreview) och Kom ihåg vilken mapp där du sparar.
5. Skapa en ny distribution i resursgruppen du skapade. Ändra filen mall och parametrar korrekt innan du distribuerar. 

   I följande diagram visas där du kan hämta klient-ID på Azure portal:

   ![Klient-ID från portalen](media/create-zone-redundant/tenant-id.png)

Mallen skapas i följande resurser:

- **Tilldelade användaridentitet** -används för att aktivera programmet gateway-instanser att komma åt nyckelvalvet och hämta certifikat som lagras av användaren.
- **KeyVault** – Nyckelvalv där användarens certifikat lagras. Detta kan vara en befintlig Nyckelvalvet samt.
- **Hemligt** – den privata nyckeln som lagras i Nyckelvalvet.
- **Princip för** – en åtkomstprincip som tillämpas på Key Vault som ger behörighet med tilldelade användaridentitet så att programmet gateway distributioner kan hämta användarcertifikat.
- **Offentliga IP-adressen** – en reserverad IP-adress som används för att komma åt programgatewayen. Den här IP-adressen ändras aldrig för livscykeln för programgatewayen.
- **Nätverkssäkerhetsgrupper** – en NSG som skapats automatiskt på gateway-undernätet. programmet som öppnar Porttrafik på konfigurerade lyssnaren. Detta uttryckligen skapas och hanteras i den nya SKU jämfört med föregående SKU: N där den här NSG har implicit.
- **Virtuellt nätverk** – vnet där Programgateway och program har distribuerats.
- **Programgateway** – skapar en Programgateway med instanserna i zoner som krävs. Som standard markeras alla zoner (1,2,3). SKU-namnet ändras till *Standard_v2*. Den här SKU namn kan ändras. Autoskala konfigurationen har för närvarande, min och max inställd på antalet instanser som krävs. När autoskalning har aktiverats, kan detta justeras.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>PowerShell-distribution

1. Se till att prenumerationen används som tidigare nämnts i förutsättningarna godkända.
2. Hämta och installera PowerShell privata MSI från [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. Hämta privata PowerShell zip-filen från den plats som anges i preview registrering bekräftelse e-post. Packa upp filen till enheten och Anteckna platsen.
4. Läsa in förhandsgranskningen moduler först innan logga in på ditt konto när förhandsgranskningen är aktiverat:

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Logga in på Azure-konto och välj den önskade prenumerationen om det finns mer än en prenumeration. Se till att du väljer rätt prenumerationen som är godkända.
5. Kör följande kommandon för att fastställa vanliga konstanter som innehåller namnen för de flesta av de enheter som håller på att skapas. 

   Ändra poster som krävs för dina inställningar.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Skapa resursgruppen:

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Skapa tilldelade användaridentitet används för att ge åtkomst till programgatewayen för att hämta certifikat från Nyckelvalvet.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Skapa Nyckelvalvet som används för att lagra dina certifikat:

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Överför certifikatet till Key Vault som en hemlighet:

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Tilldela åtkomstprincipen till Nyckelvalvet med tilldelade användaridentitet. På så sätt kan programmet gateway-instanser åtkomst till Nyckelvalvet hemliga:

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Skapa den Nätverkssäkerhetsgrupp (NSG) för att tillåta åtkomst till programmet gateway-undernät på portar där ny lyssnare har skapats.

    Till exempel skulle portar NSG: N för HTTP/HTTPS standard Tillåt inkommande åtkomst till 80, 443 och 65200-65535 för hanteringsåtgärder.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Skapa VNet och undernät:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Skapa en reserverad typ/statiska offentliga IP-adress:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Skapa programgatewayen:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. Hämta den skapade Programgateway offentlig IP-adress:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

-  Jag debiteras för Programgateway i förhandsgranskningen?

   Det finns utan kostnad under förhandsgranskningen. Du kommer att debiteras för resurser än Programgateway, till exempel Key Vault virtuella datorer osv.
- Vilka regioner är förhandsgranskningen?

   Förhandsgranskningen är tillgänglig i östra USA 2. Flera områden läggs snart.
- Stöds på portalen i förhandsgranskningen?

   Nej, support är begränsade till en privat PowerShell-modulen och Resource Manager-mall under privata förhandsgranskningen.

- Stöds produktion arbetsbelastningen under privata förhandsgranskningen?

   Nej, det finns ingen SLA eller support under privata förhandsgranskningen. Det rekommenderas inte att placera produktionsarbetsbelastningar under förhandsvisning. Stöd är begränsad till direkt interaktion med produktgruppen med e postalias för förhandsgranskning.

- Hur rapporterar problem?

   Privat förhandsversion kan innehålla buggar och kan ha ofta kod distributioner. Använda aliaset stöd appgwxzone@microsoft.com för att rapportera problem och få hjälp.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar


|Problem  |Information  |
|---------|---------|---------|
|Fakturering     |Ingen fakturering för närvarande|
|Diagnostik-loggar (inte mått)     |Prestanda och loggar begäran och svar visas inte för närvarande|
|SDK-Portal/CLI     |Inget stöd för portalen, CLI eller SDK. Portalen får inte användas för att utfärda uppdateringar om du vill förhandsgranska gateways.|
|Uppdatering via mallen misslyckas ibland     |Detta beror på konkurrenstillstånd med principen för KeyVault-åtkomst|När du har skapat Nyckelvalvet och tilldelade användaridentitet kan tas bort från mallen och endast referenser till hemlighet och identitet krävs i mallen.|
|Automatisk skalning     |Inget stöd för autoskalning för närvarande|
|WAF     |Brandvägg stöds för närvarande inte|
|Användaren har angett certifikat och dynamiska VIP: er     |Dessa stöds inte i den nya modellen. Använd Key Vault för lagring av certifikat och statiska virtuella IP-adresser.|
|Samma undernät för gamla och förhandsversionen av Programgateway     |Ett undernät med en befintlig Programgateway (gamla model) kan inte användas för den privata förhandsversionen.|
|HTTP-2, FIPS-läge WebSocket, Azure Web Apps som backend     |För närvarande stöds inte |


## <a name="support-and-feedback"></a>Support och feedback

Kontakta för support och feedback till appgwxzone@microsoft.com. Produktgruppen programmet gateway är gärna höra dina synpunkter för förbättringar och ge vägledning om så krävs.

## <a name="next-steps"></a>Nästa steg

Mer information om andra program gateway-funktioner:

- [Vad är Azure Application Gateway?](overview.md)