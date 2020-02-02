---
title: Utveckla ett säkert Azure AD-webbprogram | Microsoft Docs
description: Den här enkla exempel appen implementerar säkerhets metod tips som förbättrar ditt program och din organisations säkerhets position när du utvecklar på Azure.
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 11bf7c0ae05c2e52d59efb32be47ce6bd96fac4f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937983"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Utveckla en säker app för en Azure AD-App
## <a name="overview"></a>Översikt

Det här exemplet är en enkel Azure Active Directory med en webbapp som länkar till säkerhets resurser för att utveckla appar i Azure. Appen implementerar säkerhets metoder som hjälper dig att förbättra ditt program och din organisations säkerhets position när du utvecklar appar i Azure.

Distributions skripten konfigurerar infrastrukturen. När du har kört distributions skripten måste du göra en manuell konfiguration i Azure Portal för att länka komponenterna och tjänsterna tillsammans. Det här exemplet är riktat mot erfarna utvecklare på Azure som arbetar inom detalj handels branschen och vill bygga en skyddad Azure Active Directory med säker Azure-infrastruktur. 


När du utvecklar och distribuerar den här appen lär du dig att 
- Skapa en Azure Key Vault instans, lagra och hämta hemligheter från den.
- Distribuera Azure-webbappen, som är dedikerad isolerad med klient dels brand Väggs åtkomst. 
- Skapa och konfigurera en Azure Application Gateway-instans med en brand vägg som använder OWASP 10 högsta ruleset. 
- Aktivera kryptering av data under överföring och i vila med hjälp av Azure-tjänster. 
- Konfigurera Azure policy och Security Center för att utvärdera compliancies. 

När du har utvecklat och distribuerat den här appen har du ställt in följande exempel-webbapp tillsammans med de konfigurations-och säkerhets åtgärder som beskrivs.

## <a name="architecture"></a>Arkitektur
Appen är ett typiskt n-Nivåprogram med tre nivåer. Klient delen, Server delen och databas lagret med integrerade komponenter för övervakning och hemlig hantering visas här:

![App-arkitektur](./media/secure-aad-app/architecture.png)

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet distributions arkitektur. 

Arkitekturen består av dessa komponenter

- [Azure Application Gateway](../../application-gateway/index.yml). Tillhandahåller gateway och brand vägg för vår program arkitektur.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Tillhandahåller en utöknings bar APM-tjänst (Application Performance Management) på flera plattformar.
- [Azure Key Vault](../../key-vault/index.yml). Lagrar och krypterar vår Apps hemligheter och hanterar skapandet av åtkomst principer runt dem.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Tillhandahåller molnbaserad identitets-och åtkomst hanterings tjänst, logga in och få åtkomst till resurser.
- [Azure-Domain Name System](../../dns/dns-overview.md). Ange tjänsten som värd för domänen.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Ger möjlighet att skala dina program och skapa hög tillgänglighet för dina tjänster.
- [Azure Web App](../../app-service/overview.md).  Tillhandahåller en HTTP-baserad tjänst för att vara värd för webb program.
- [Azure Security Center](../../security-center/index.yml). tillhandahåller Avancerat skydd för dina hybrid arbets belastningar i molnet.
- [Azure policy](../../governance/policy/overview.md). Tillhandahåller utvärdering av dina resurser för inkompatibilitet med tilldelade principer.

## <a name="threat-model"></a>Hot modell
Hot modellering är en process där du kan identifiera potentiella säkerhetshot för ditt företag och program och sedan se till att en lämplig minsknings plan är på plats.

Det här exemplet använde [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) för att implementera hot modellering för appen säker exempel. Genom att skriva diagram över komponenterna och data flödena kan du identifiera problem och hot tidigt i utvecklings processen. Tid och pengar kommer att sparas senare med hjälp av detta.

Här är Hot modellen för exempel appen

![Hot modell](./media/secure-aad-app/threat-model.png)

Några exempel på hot och eventuella sårbarheter som verktyget för Threat Modeling genererar visas på följande skärm bild. Hot modellen ger en översikt över angrepps ytan som exponeras och gör det möjligt för utvecklarna att tänka på hur de kan åtgärda problemen.

![Hot modellens utdata](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Krav
För att komma igång med programmet måste du installera följande verktyg:

- En kod redigerare för att ändra och Visa program koden. [Visual Studio Code](https://code.visualstudio.com/) är ett alternativ med öppen källkod.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) på din utvecklings dator.
- [Git](https://git-scm.com/) i systemet. Git används för att klona käll koden lokalt.
- [JQ](https://stedolan.github.io/jq/), ett UNIX-verktyg för att fråga JSON på ett användarvänligt sätt.

Du behöver en Azure-prenumeration för att distribuera exempel appens resurser. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) för att testa exempel appen.

När du har installerat dessa verktyg är du redo att distribuera appen på Azure.

### <a name="implementation-guidance"></a>Implementeringsanvisningar
Distributions skriptet är ett skript som kan delas upp i fyra faser. Varje fas distribuerar och konfigurerar en Azure-resurs som finns i [arkitektur diagrammet](#architecture).

De fyra faserna är

- Distribuera Azure Key Vault.
- Distribuera Azure Web Apps.
- Distribuera Application Gateway med brand vägg för webbaserade program.
- Konfigurera en Azure AD med distribuerad app.

Varje fas bygger på föregående, med hjälp av konfiguration från de tidigare distribuerade resurserna.

Kontrol lera att du har installerat verktygen som visas under [krav](#prerequisites)för att slutföra implementerings stegen.

#### <a name="deploy-azure-key-vault"></a>Distribuera Azure Key Vault
I det här avsnittet skapar och distribuerar du en Azure Key Vault-instans som används för att lagra hemligheter och certifikat.

När du har slutfört distributionen har du en Azure Key Vault-instans som distribuerats på Azure.

Distribuera Azure Key Vault med hjälp av PowerShell
 
1. Deklarera variablerna för Azure Key Vault.
2. Registrera Azure Key Vault-providern.
3. Skapa resurs gruppen för instansen.
4. Skapa Azure Key Vault-instansen i resurs gruppen som skapades i steg 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Under den här Azure AD-användaren får du administratörs behörighet till Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Registrera AZ-providers
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Skapa Azure Key Vault-instansen
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Lägg till administratörs principerna i Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>För att skapa en åtkomst princip för att tillåta att en användare hämtar och listar kryptografiska nycklar, certifikat och hemligheter om du känner till användarens huvud namn:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Vi rekommenderar att du använder hanterade identiteter för Azure-resurser i appar som använder Key Vault för att få åtkomst till resurser. Din säkerhets position ökar när åtkomst nycklar till Key Vault inte lagras i koden eller i konfigurationen.

Ett rot certifikat ingår i behållaren. De steg som vidtagits för att hämta certifikatet är

1. Ladda ned certifikat filen från [certifikat utfärdaren](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Avkoda certifikat filen:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Det här skriptet skapar en tilldelad identitet för App Service-instansen som kan användas med MSI för att interagera med Azure Key Vault utan hårda kodnings hemligheter i kod eller konfiguration.

Gå till Azure Key Vault-instansen i portalen för att auktorisera den tilldelade identiteten på fliken åtkomst princip. Välj **Lägg till ny åtkomst princip**. Under **Välj huvud**namn söker du efter det program namn som liknar namnet på App Service-instansen som skapats.
Ett tjänst huvud namn som är kopplat till programmet ska vara synligt. Välj den och spara åtkomst princip sidan, som du ser i följande skärm bild.

Eftersom programmet bara behöver hämta nycklar väljer du behörigheten **Hämta** i alternativen för hemligheter, vilket ger åtkomst samtidigt som de behörigheter som beviljats minskas.

![Key Vault åtkomst princip](./media/secure-aad-app/kv-access-policy.png)

*Skapa en princip för Key Vault åtkomst*

Spara åtkomst principen och spara sedan den nya ändringen på fliken **åtkomst principer** för att uppdatera principerna.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Distribuera Application Gateway med brand vägg för webbaserade program aktive rad
I Web Apps rekommenderar vi inte att du exponerar tjänster direkt för världen utanför Internet.
Belastnings utjämning och brand Väggs regler ger bättre säkerhet och kontroll över inkommande trafik och hjälper dig att hantera den.

Så här distribuerar du en Application Gateway instans

1. Skapa resurs gruppen för att House Application Gateway.
2. Etablera ett virtuellt nätverk att ansluta till gatewayen.
3. Skapa ett undernät för gatewayen i det virtuella nätverket.
4. Etablera en offentlig IP-adress.
5. Etablera programgatewayen.
6. Aktivera brand väggen för webbaserade program på gatewayen.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Distribuera Azure-Web Apps
Med Azure App Service kan du bygga och vara värd för webbappar med hjälp av språken som python C#, ruby, och Java. Azure stöder också anpassade behållare, vilket kan göra det möjligt för praktiskt taget alla programmeringsspråk att köras på Azure App Services plattform.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Skapa en App Service plan på den kostnads fria nivån
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Skapa ett webbprogram
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Innan du fortsätter går du till ditt Azure Domain Name System konfigurations gränssnitt för din anpassade domän och följer anvisningarna på https://aka.ms/appservicecustomdns för att konfigurera en CNAME-post för värd namnet "www" och pekar den på webbappens standard domän namn

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Uppgradera App Service plan till delad nivå (minimum krävs av anpassade domäner)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Lägg till ett anpassat domän namn i webbappen
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="network"></a>Nätverk
När du har slutfört distributionen har du en Application Gateway med brand vägg för webbaserade program aktive rad.

Gateway-instansen exponerar port 443 för HTTPS. Den här konfigurationen säkerställer att vår app endast är tillgänglig på port 443 via HTTPS.

Att blockera oanvända portar och begränsa exponerings ytans exponering är en bra säkerhets rutin.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Lägg till nätverks säkerhets grupper i App Service-instansen

App Service instanser kan integreras med virtuella nätverk. Den här integrationen gör att de kan konfigureras med principer för nätverks säkerhets grupper som hanterar appens inkommande och utgående trafik.

1. Om du vill aktivera den här funktionen väljer du **nätverk**på bladet Azure App tjänst instans under **Inställningar**. Konfigurera under **VNet-integrering**i den högra rutan.

   ![Ny integrering av virtuella nätverk](./media/secure-web-app/app-vnet-menu.png)

    *Ny virtuell nätverks integrering för App Service*
1. På nästa sida väljer du **Lägg till VNet (för hands version)** .

1. På nästa meny väljer du det virtuella nätverk som skapades i distributionen som börjar med `aad-vnet`. Du kan antingen skapa ett nytt undernät eller välja ett befintligt.
   I det här fallet skapar du ett nytt undernät. Ange **adress intervallet** till **10.0.3.0/24** och ge under **nätet**ett namn.

   ![App Service konfiguration av virtuellt nätverk](./media/secure-web-app/app-vnet-config.png)

    *Konfiguration av virtuellt nätverk för App Service*

Nu när du har aktiverat integrering av virtuella nätverk kan du lägga till nätverks säkerhets grupper i appen.

1. Använd Sök-rutan och Sök efter **nätverks säkerhets grupper**. Välj **nätverks säkerhets grupper** i resultatet.

    ![Sök efter nätverks säkerhets grupper](./media/secure-web-app/nsg-search-menu.png)

    *Sök efter nätverks säkerhets grupper*

2. I nästa meny väljer du **Lägg till**. Ange **namnet** på NSG och **resurs gruppen** där den ska finnas. Den här NSG kommer att användas för application gateways undernät.

    ![Skapa en NSG](./media/secure-web-app/nsg-create-new.png)

    *Skapa en NSG*

3. När NSG har skapats väljer du den. I sitt blad väljer du **inkommande säkerhets regler**under **Inställningar**. Konfigurera de här inställningarna så att anslutningar kommer till Application Gateway via port 443.

   ![Konfigurera NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurera NSG*

4. I utgående regler för gateway-NSG lägger du till en regel som tillåter utgående anslutningar till App Service-instansen genom att skapa en regel som är riktad mot tjänst tag gen `AppService`

   ![Lägg till utgående regler för NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Lägg till utgående regler för NSG*

    Lägg till en annan utgående regel som tillåter att gatewayen skickar utgående regler till ett virtuellt nätverk.

   ![Lägg till en annan utgående regel](./media/secure-web-app/nsg-outbound-vnet.png)

    *Lägg till en annan utgående regel*

5. På bladet undernät i NSG väljer du **associera**, väljer det virtuella nätverk som skapades i distributionen och väljer Gateway-undernätet med namnet **GW-undernät**. NSG tillämpas på under nätet.

6. Skapa en annan NSG som i föregående steg, den här gången för App Service-instansen. Ge den ett namn. Lägg till regeln för inkommande trafik för port 443 som du gjorde för Application Gateway-NSG.

   Om du har en App Service-instans som distribuerats på en App Service-miljön-instans, vilket inte är fallet för den här appen, kan du lägga till regler för inkommande trafik som tillåter Azure Service Health avsökningar genom att öppna portarna 454-455 i App Service NSG. Här är konfigurationen:

   ![Lägg till regler för Azure Service Health avsökningar](./media/secure-web-app/nsg-create-healthprobes.png)

    *Lägg till regler för Azure Service Health avsökningar (endast App Service-miljön)*

Om du vill begränsa angrepps ytan ändrar du App Service nätverks inställningar så att endast programgatewayen får åtkomst till programmet.
Om du vill tillämpa inställningarna går du till fliken App Service nätverk, väljer fliken **IP-begränsningar** och skapar en Tillåt-regel som endast tillåter programgatewayens IP att komma åt tjänsten direkt. Du kan hämta IP-adressen för gatewayen från sidan Översikt. På fliken **CIDR för IP-adress** anger du IP-adressen i följande format: `<GATEWAY_IP_ADDRESS>/32`.

![Tillåt endast gatewayen](./media/secure-web-app/app-allow-gw-only.png)

*Tillåt endast Gateway-IP att komma åt App Service*

### <a name="azure-domain-name-system"></a>Azure-Domain Name System 
Azure Domain Name System eller Azure Domain Name System ansvarar för översättning (eller matchning) av en webbplats eller ett tjänst namn till dess IP-adress. Azure Domain Name System (https://docs.microsoft.com/azure/dns/dns-overview) är en värd tjänst för Domain Name System domäner som tillhandahåller namn matchning med hjälp av Azure-infrastrukturen. Genom att vara värd för domäner i Azure kan användare hantera Domain Name System poster med samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster. Azure Domain Name System stöder också privata Domain Name System domäner.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volym kryptering för data diskar. Lösningen integreras med Azure Key Vault för att hjälpa dig att styra och hantera disk krypterings nycklarna.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker tillhandahåller funktioner för att hantera åtkomst till kort innehavares data i Azure-miljön
- Azure Active Directory är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare som har åtkomst till Azure WebApp.
- Med rollbaserad åtkomst kontroll i Azure kan administratörer definiera detaljerade åtkomst behörigheter för att endast ge åtkomst till den mängd åtkomst som användarna behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer bara tillåta vissa åtgärder för åtkomst till data för kort innehavare. Åtkomst till prenumerationen är begränsad till prenumerations administratören.
- Azure Active Directory Privileged Identity Management gör det möjligt för kunderna att minimera antalet användare som har åtkomst till viss information, till exempel kort korts data. Administratörer kan använda Azure Active Directory Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst just-in-Time vid behov.
- Azure Active Directory Identity Protection identifierar potentiella sårbarheter som påverkar en organisations identiteter, konfigurerar automatiserade svar på identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.
### <a name="secrets-management"></a>Hemligheter, hantering
Lösningen använder Azure Key Vault för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault funktioner hjälper kunder att skydda och komma åt sådana data
   - Avancerade åtkomst principer konfigureras på grund av behov.
   - Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter.
   - Alla nycklar och hemligheter i Key Vault har förfallo datum.
   - Alla nycklar i Key Vault skyddas av specialiserade säkerhetsmoduler för maskin vara. Nyckel typen är en HSM-skyddad (Hardware Security Module) 2048-bitars RSA-nyckel.
   - Med Key Vault kan du kryptera nycklar och hemligheter (till exempel autentiseringsnyckel, lagrings konto nycklar, data krypterings nycklar). PFX-filer och lösen ord) med hjälp av nycklar som skyddas av HSM: er (Hardware Security modules). 
   - Använd rollbaserad Access Control (RBAC) för att tilldela behörigheter till användare, grupper och program i ett visst omfång.     
   - Använd Key Vault för att hantera dina TLS-certifikat med autoförnyelse. 
   - Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
   - Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs.
### <a name="azure-security-center"></a>Azure Security Center
Med Azure Security Center kan kunder centralt tillämpa och hantera säkerhets principer över arbets belastningar, begränsa exponeringen för hot och identifiera och reagera på attacker. Dessutom 
   - Azure Security Center använder befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations-och tjänst rekommendationer för att förbättra säkerheten position och skydda data.
   - Azure Security Center använder flera olika identifierings funktioner för att meddela kunder om potentiella attacker som riktar sig mot sina miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning fördefinierade säkerhets aviseringar som utlöses när ett hot eller misstänkt aktivitet äger rum. Anpassade aviserings regler i Azure Security Center låter kunderna definiera nya säkerhets aviseringar baserat på data som redan har samlats in från deras miljö.
   - Azure Security Center ger prioriterade säkerhets aviseringar och incidenter, vilket gör det enklare för kunderna att upptäcka och åtgärda potentiella säkerhets problem. En hot informations rapport genereras för varje identifierat hot för att hjälpa incident hanterings team att undersöka och åtgärda hot.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Arkitekturen minskar risken för säkerhets problem med en Azure Application Gateway med en brand vägg för webbaserade program konfigurerad, och OWASP-ruleset har Aktiver ATS. Ytterligare funktioner är
   - Slut punkt till slut punkt – SSL.
   - Inaktivera TLS v 1.0 och v 1.1.
   - Aktivera TLSv 1.2.
   - Brand vägg för webbaserade program (skydds läge).
   - Skydds läge med OWASP 3,0 ruleset.
   - Aktivera diagnostikloggning.
   - Anpassade hälso avsökningar.
   - Azure Security Center och en Azure Advisor ger ytterligare skydd och aviseringar. Azure Security Center ger också ett ryktes system.
### <a name="logging-and-auditing"></a>Loggning och granskning
Azure-tjänster loggar system-och användar aktiviteter i stor utsträckning, samt systemets hälso tillstånd:
   - Aktivitets loggar: [aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger inblick i åtgärder som utförs på resurser i en prenumeration. Aktivitets loggar kan hjälpa till att bestämma en åtgärds initierare, tidpunkt för förekomst och status.
   - Diagnostikloggar: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) innehåller alla loggar som har avsänts av varje resurs. Dessa loggar innehåller loggar för Windows Event system, Azure Storage loggar, Key Vault gransknings loggar och Application Gateway åtkomst-och brand Väggs loggar. Alla diagnostiska loggar skriver till ett centraliserat och krypterat Azure Storage-konto för arkivering. Kvarhållning är en användare som kan konfigureras, upp till 730 dagar, för att uppfylla organisationens särskilda krav för kvarhållning.
### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
   Dessa loggar konsol IDE ras i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrument panels rapportering. När data har samlats in ordnas de i separata tabeller för varje datatyp inom Log Analytics arbets ytor, vilket innebär att alla data kan analyseras tillsammans oavsett den ursprungliga källan. Dessutom kan Azure Security Center integreras med Azure Monitor loggar som gör det möjligt för kunder att använda Kusto-frågor för att komma åt sina säkerhets händelse data och kombinera dem med data från andra tjänster.

   Följande lösningar för Azure- [övervakning](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen

   - [Active Directory-utvärdering](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): den Active Directory hälso kontroll lösningen utvärderar hälso-och hälso tillståndet i Server miljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är relaterade till den distribuerade Server infrastrukturen.
   - [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): agenthälsa lösning rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar drift data.
   - [Aktivitetslogganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Aktivitetslogganalys lösningen hjälper till med analys av Azures aktivitets loggar i alla Azure-prenumerationer för en kund.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive att spåra API-anrop i sina Azure-resurser.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utöknings bar hanterings tjänst för program prestanda för webbutvecklare på flera plattformar. Application Insights identifierar prestanda avvikelser och kunder kan använda den för att övervaka Live-webbprogrammet. Den innehåller kraftfulla analys verktyg som hjälper kunder att diagnostisera problem och förstå vad användare faktiskt gör med sina appar. Det är utformat för att hjälpa kunder att kontinuerligt förbättra prestanda och användbarhet.

### <a name="azure-key-vault"></a>Azure Key Vault
   Skapa ett valv för organisationen där du kan lagra nycklar och upprätthålla ansvaret för drift uppgifter som nedan

   - Data som lagras i Key Vault innehåller   
   - Insikts nyckel för program
   - Åtkomst nyckel för data lagring
   - Anslutningssträng
   - Data tabell namn
   - Användarautentiseringsuppgifter
   - Avancerade åtkomst principer konfigureras på grund av behov
   - Key Vault åtkomst principer definieras med lägsta behörighet som krävs för nycklar och hemligheter
   - Alla nycklar och hemligheter i Key Vault har förfallo datum
   - Alla nycklar i Key Vault skyddas av maskin varu säkerhetsmodulen (HSM) [nyckel typ = inbyggd HSM-modul (Hardware Security Module)       
     2048-bitars RSA-nyckel]
   - Alla användare/identiteter beviljas minst de behörigheter som krävs med hjälp av rollbaserad Access Control (RBAC)
   - Program delar inte en Key Vault om de inte litar på varandra och de behöver åtkomst till samma hemligheter vid körning
   - Diagnostikloggar för Key Vault aktive ras med en kvarhållningsperiod på minst 365 dagar.
   - Tillåtna kryptografiska åtgärder för nycklar är begränsade till dem som krävs

### <a name="vpn-and-expressroute"></a>VPN-och ExpressRoute
   En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) som krävs för att konfigureras genom en säker upprättande av en anslutning till resurserna som distribuerats som en del av den här PaaS referens arkitektur för webb program. Genom att konfigurera en VPN-eller ExpressRoute på lämpligt sätt kan kunder lägga till ett skydds lager för data under överföring.

   Genom att implementera en säker VPN-tunnel med Azure kan du skapa en virtuell privat anslutning mellan ett lokalt nätverk och en Azure-Virtual Network. Den här anslutningen sker via Internet och gör det möjligt för kunder att på ett säkert sätt placera "tunnel"-information i en krypterad länk mellan kundens nätverk och Azure. VPN för plats-till-plats är en säker, vuxen teknik som har distribuerats av företag i alla storlekar i årtionden. IPsec-tunnelläge används i det här alternativet som en krypterings metod.

   Eftersom trafiken i VPN-tunneln passerar Internet med en plats-till-plats-VPN, erbjuder Microsoft en annan, ännu mer säker anslutnings möjlighet. Azure ExpressRoute är en särskild WAN-länk mellan Azure och en lokal plats eller en Exchange-värd leverantör. Eftersom ExpressRoute-anslutningar inte går via Internet, ger dessa anslutningar mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. Dessutom, eftersom det är en direkt anslutning till kundens teleoperatörs leverantör, överförs inte data via Internet och exponeras därför inte för den.

   Metod tips för att implementera ett säkert hybrid nätverk som utökar ett lokalt nätverk till Azure är [tillgängligt](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

#### <a name="implement-azure-active-directory-oidc"></a>Implementera Azure Active Directory OIDC

1. Om du vill klona käll kods databasen använder du detta git-kommando

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Uppdatera omdirigerings-URL: erna
1.  Gå tillbaka till Azure Portal. I det vänstra navigerings fönstret väljer du tjänsten Azure Active Directory och väljer sedan Appregistreringar.
2.  I den resulterande skärmen väljer du programmet WebApp-OpenIDConnect-DotNet-Code-v2.
3.  På fliken autentisering o i avsnittet omdirigerings-URI väljer du webb i kombinations rutan och lägger till följande omdirigerings-URI: er.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o i avsnittet Avancerade inställningar ange utloggnings-URL till https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  På fliken anpassning o uppdaterar du Start sidans URL till adressen till din app service, till exempel https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net.
        o Spara konfigurationen.
5.  Om programmet anropar ett webb-API ska du se till att tillämpa de nödvändiga ändringarna på projektet appSettings. JSON, så att den anropar den publicerade API-URL: en i stället för localhost.
Publicera exemplet
    1.  På fliken Översikt i App Service laddar du ned publicerings profilen genom att klicka på länken Hämta publicerings profil och spara den. Andra distributions metoder, till exempel från käll kontroll, kan också användas.
    2.  Växla till Visual Studio och gå till projektet WebApp-OpenIDConnect-DotNet-Code-v2. Högerklicka på projektet i Solution Explorer och välj publicera. Klicka på Importera profil i det nedre fältet och importera den publicerings profil som du laddade ned tidigare.
    3.  Klicka på Konfigurera och på fliken anslutning uppdaterar du mål-URL: en så att den är en https på Start sidans URL, till exempel https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net. Klicka på Next.
    4.  På fliken Inställningar kontrollerar du att aktivera organisations autentisering inte är markerat. Klicka på Spara. Klicka på Publicera på huvud skärmen.
    5.  Visual Studio kommer att publicera projektet och automatiskt öppna en webbläsare till projektets URL. Om du ser projektets standard webb sida slutfördes publikationen.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementera Multi-Factor Authentication för Azure Active Directory
   Administratörer måste se till att prenumerations kontona i portalen är skyddade. Prenumerationen är sårbar för attacker eftersom den hanterar de resurser som du har skapat. Om du vill skydda prenumerationen aktiverar du Multi-Factor Authentication på fliken **Azure Active Directory** i prenumerationen.

   En Azure AD fungerar baserat på principer som tillämpas på en användare eller grupper av användare som uppfyller ett visst kriterium.
Azure skapar en standard princip som anger att administratörer behöver tvåfaktorautentisering för att logga in på portalen.
När du har aktiverat den här principen kan du uppmanas att logga ut och logga in igen på Azure Portal.

Aktivera MFA för admin-inloggningar

   1. Gå till fliken **Azure Active Directory** i Azure Portal
   2. Under kategorin säkerhet väljer du villkorlig åtkomst. Du ser den här skärmen

       ![Villkorlig åtkomst – principer](./media/secure-aad-app/ad-mfa-conditional-add.png)

Om du inte kan skapa en ny princip

   1. Gå till fliken **MFA** .
   2. Välj en Azure AD Premium **kostnads fri utvärderings** länk för att prenumerera på den kostnads fria utvärderings versionen.

   ![En Azure AD Premium kostnads fri utvärderings version](./media/secure-aad-app/ad-trial-premium.png)

Gå tillbaka till skärmen för villkorlig åtkomst.

   1. Välj fliken ny princip.
   2. Ange principens namn.
   3. Välj de användare eller grupper som du vill aktivera MFA för.
   4. Under **åtkomst kontroller**väljer du fliken **beviljande** och väljer sedan **Kräv Multi-Factor Authentication** (och andra inställningar om du vill).

   ![Kräv MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Du kan aktivera principen genom att markera kryss rutan längst upp på skärmen eller göra det på fliken **villkorlig åtkomst** . När principen är aktive rad behöver användare MFA för att logga in på portalen.

   Det finns en bas linje princip som kräver MFA för alla Azure-administratörer. Du kan aktivera det direkt i portalen. Om du aktiverar den här principen kan den aktuella sessionen bli ogiltig och du kan logga in igen.

   Om bas linje principen inte är aktive rad
   1.   Välj **KRÄV MFA för administratörer**.
   2.   Välj **Använd princip omedelbart**.

   ![Välj Använd princip omedelbart](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Övervaka appar och resurser med hjälp av Azure Sentinel

   När ett program växer blir det svårt att sammanställa alla säkerhets signaler och mått som tas emot från resurser och gör dem användbara på ett åtgärds sätt.

   Azure Sentinel har utformats för att samla in data, identifiera de typer av hot som är möjliga och ge insyn i säkerhets incidenter.
Även om det väntar på en manuell åtgärd kan Azure Sentinel förlita sig på fördefinierade spel böcker för att starta aviseringar och incident hanterings processer.

   Exempel appen består av flera resurser som Azure Sentinel kan övervaka.
Om du vill konfigurera Azure Sentinel måste du först skapa en Log Analytics arbets yta som lagrar alla data som samlas in från de olika resurserna.

Skapa den här arbets ytan

   1. Sök efter **Log Analytics**i rutan sök i Azure Portal. Välj **Log Analytics arbets ytor**.

   ![Sök efter Log Analytics arbets ytor](./media/secure-aad-app/sentinel-log-analytics.png)

   *Sök efter Log Analytics arbets ytor*

   2. På nästa sida väljer du **Lägg till** och anger sedan ett namn, en resurs grupp och en plats för arbets ytan.
   ![Skapa en Log Analytics-arbetsyta](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Skapa en Log Analytics-arbetsyta*

   3. Använd sökrutan för att söka efter **Azure Sentinel**.

   ![Sök efter Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Sök efter Azure Sentinel*

   4. Välj **Lägg till** och välj sedan Log Analytics arbets ytan som du skapade tidigare.

   ![Lägg till en Log Analytics-arbetsyta](./media/secure-aad-app/sentinel-workspace-add.png)

   *Lägg till en Log Analytics-arbetsyta*

   5. På sidan **Azure Sentinel-data anslutningar** väljer du **data kopplingar**under **konfiguration**. Du ser en matris med Azure-tjänster som du kan länka till Log Analytics lagrings instans för analys i Azure Sentinel.

   ![Log Analytics data anslutningar](./media/secure-aad-app/sentinel-connectors.png)

      *Lägg till en data anslutning i Azure Sentinel*

   Gör så här för att ansluta Application Gateway:

   1. Öppna bladet Azure Application Gateway-instans.
   2. Under **övervakning**väljer **diagnostikinställningar**.
   3. Välj **Lägg till diagnostisk inställning**.

   ![Lägg till Application Gateway diagnostik](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Lägg till Application Gateway diagnostik*

   4. På sidan **diagnostikinställningar** väljer du Log Analytics arbets ytan som du skapade och väljer sedan alla mått som du vill samla in och skicka till Azure Sentinel. Välj **Spara**.

   ![Inställningar för Azure Sentinel Connector](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Kostnadsöverväganden
   Om du inte redan har ett Azure-konto kan du skapa ett kostnads fritt. Gå till [sidan kostnads fritt konto](https://azure.microsoft.com/free/) för att komma igång, se vad du kan göra med ett kostnads fritt Azure-konto och lär dig vilka produkter som är kostnads fria i 12 månader.

   Om du vill distribuera resurserna i exempel appen med säkerhetsfunktionerna måste du betala för vissa Premium-funktioner. När appen skalas och de kostnads fria nivåerna och utvärderingarna som erbjuds av Azure måste uppgraderas för att uppfylla program kraven kan kostnaderna öka. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för Azure för att beräkna dina kostnader.

## <a name="next-steps"></a>Nästa steg
   Följande artiklar kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Design](secure-design.md)
- [Utveckla](secure-develop.md)
- [Distribuera](secure-deploy.md)
