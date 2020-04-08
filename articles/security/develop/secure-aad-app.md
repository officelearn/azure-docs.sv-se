---
title: Utveckla ett säkert Azure AD-webbprogram | Microsoft-dokument
description: Den här enkla exempelappen implementerar metodtips för säkerhet som förbättrar ditt program och organisationens säkerhetsposition när du utvecklar på Azure.
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
ms.openlocfilehash: 599c4a31840b47294b43c4c4d1f0200b17f04540
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810544"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Utveckla säker app för en Azure AD-app
## <a name="overview"></a>Översikt

Det här exemplet är en enkel Azure Active Directory med webbapp som länkar till säkerhetsresurser för att utveckla appar på Azure. Appen implementerar metodtips för säkerhet som kan förbättra ditt program och organisationens säkerhetsposition när du utvecklar appar på Azure.

Distributionsskripten konfigurerar infrastrukturen. När du har kört distributionsskripten måste du göra en viss manuell konfiguration i Azure-portalen för att länka komponenterna och tjänsterna tillsammans. Det här exemplet är riktat mot erfarna utvecklare på Azure som arbetar inom detaljhandeln och vill skapa en säker Azure Active Directory med säker Azure-infrastruktur. 


När du utvecklar och distribuerar den här appen får du lära dig hur du 
- Skapa en Azure Key Vault-instans, lagra och hämta hemligheter från den.
- Distribuera Azure Web App, som är dedikerad isolerad med frontend-brandväggsåtkomst. 
- Skapa och konfigurera en Azure Application Gateway-instans med en brandvägg som använder OWASP Top 10 Ruleset. 
- Aktivera kryptering av data under överföring och i vila med hjälp av Azure-tjänster. 
- Konfigurera Azure-princip- och säkerhetscenter för att utvärdera kompatibiliteterna. 

När du har utvecklat och distribuerat den här appen har du konfigurerat följande exempelwebbapp tillsammans med de konfigurations- och säkerhetsåtgärder som beskrivs.

## <a name="architecture"></a>Arkitektur
Appen är ett typiskt n-nivåprogram med tre nivåer. Frontend, baksidan och databasskiktet med integrerade övervaknings- och hemliga hanteringskomponenter visas här:

![App-arkitektur](./media/secure-aad-app/architecture.png)

Den här lösningen använder följande Azure-tjänster. Information om distributionsarkitekturen finns i avsnittet Distributionsarkitektur. 

Arkitekturen består av dessa komponenter

- [Azure Application Gateway](../../application-gateway/index.yml). Tillhandahåller gateway och brandvägg för vår programarkitektur.
- [Programinsikter](../../azure-monitor/app/app-insights-overview.md). Tillhandahåller en utökningsbar APM-tjänst (Application Performance Management) på flera plattformar.
- [Azure Key Vault](../../key-vault/index.yml). Lagrar och krypterar appens hemligheter och hanterar skapandet av åtkomstpolicyer runt dem.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Tillhandahåller molnbaserad identitets- och åtkomsthanteringstjänst, inloggning och åtkomstresurser.
- [Azure-domännamnssystem](../../dns/dns-overview.md). Ange den tjänst som värd för domänen.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Ger för att skala dina program och skapa hög tillgänglighet för dina tjänster.
- [Azure Web App](../../app-service/overview.md).  Tillhandahåller en HTTP-baserad tjänst för att vara värd för webbprogram.
- [Azure Security Center](../../security-center/index.yml). ger avancerat skydd mot hot över dina hybridarbetsbelastningar i molnet.
- [Azure-princip](../../governance/policy/overview.md). Ger utvärdera dina resurser för bristande efterlevnad med tilldelade principer.

## <a name="threat-model"></a>Hotmodell
Hotmodellering är processen att identifiera potentiella säkerhetshot mot ditt företag och program och sedan se till att en korrekt begränsningsplan finns på plats.

I det här exemplet användes [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) för att implementera hotmodellering för den säkra exempelappen. Genom att kartlägga komponenterna och dataflödena kan du identifiera problem och hot tidigt i utvecklingsprocessen. Tid och pengar kommer att sparas senare med hjälp av detta.

Här är hotmodellen för exempelappen

![Hotmodell](./media/secure-aad-app/threat-model.png)

Vissa exempelhot och potentiella sårbarheter som verktyget för hotmodellering genererar visas i följande skärmbild. Hotmodellen ger en översikt över den exponerade attackytan och uppmanar utvecklarna att tänka på hur du kan minska problemen.

![Utdata för hotmodell](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Krav
För att få igång programmet måste du installera följande verktyg:

- En kodredigerare för att ändra och visa programkoden. [Visual Studio Code](https://code.visualstudio.com/) är ett alternativ med öppen källkod.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) på din utvecklingsdator.
- [Git](https://git-scm.com/) på ditt system. Git används för att klona källkoden lokalt.
- [jq](https://stedolan.github.io/jq/), ett UNIX-verktyg för att fråga JSON på ett användarvänligt sätt.

Du behöver en Azure-prenumeration för att distribuera exempelappens resurser. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) för att testa exempelappen.

När du har installerat dessa verktyg är du redo att distribuera appen på Azure.

### <a name="implementation-guidance"></a>Vägledning för genomförande
Distributionsskriptet är ett skript som kan delas upp i fyra faser. Varje fas distribuerar och konfigurerar en Azure-resurs som finns i [arkitekturdiagrammet](#architecture).

De fyra faserna är

- Distribuera Azure Key Vault.
- Distribuera Azure Web Apps.
- Distribuera Application Gateway med brandvägg för webbprogram.
- Konfigurera en Azure AD med distribuerad app.

Varje fas bygger på den föregående med hjälp av konfiguration från tidigare distribuerade resurser.

Kontrollera att du har installerat verktygen under [Förutsättningar](#prerequisites)för implementeringen.

#### <a name="deploy-azure-key-vault"></a>Distribuera Azure Key Vault
I det här avsnittet skapar och distribuerar du en Azure Key Vault-instans som används för att lagra hemligheter och certifikat.

När du har slutfört distributionen har du en Azure Key Vault-instans distribuerad på Azure.

Så här distribuerar du Azure Key Vault med Powershell
 
1. Deklarera variablerna för Azure Key Vault.
2. Registrera Azure Key Vault-providern.
3. Skapa resursgruppen för instansen.
4. Skapa Azure Key Vault-instansen i resursgruppen som skapats i steg 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Nedanstående Azure AD-användare har administratörsbehörighet till Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Registrera Az-leverantörerna
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Skapa Azure Key Vault-instansen
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Lägga till administratörsprinciperna i Nyckelvalvet
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Så här skapar du en åtkomstprincip så att en användare kan hämta och lista kryptografiska nycklar, certifikat och hemligheter om du känner till användarens huvudnamn:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Det är en bra idé att använda hanterade identiteter för Azure-resurser i appar som använder Key Vault för att komma åt resurser. Säkerhetspositionen ökar när åtkomstnycklarna till Key Vault inte lagras i kod eller i konfiguration.

Ett rotcertifikat ingår i behållaren. De åtgärder som vidtagits för att erhålla certifikatet

1. Hämta certifikatfilen från [certifikatutfärdaren](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Avkoda certifikatfilen:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Det här skriptet skapar en tilldelad identitet för App Service-instansen som kan användas med MSI för att interagera med Azure Key Vault utan hårda kodningshemligheter i kod eller konfiguration.

Gå till Azure Key Vault-instansen i portalen för att auktorisera den tilldelade identiteten på fliken åtkomstprincip. Välj **Lägg till ny åtkomstprincip**. Under **Välj huvudnamn**söker du efter programnamnet som liknar namnet på apptjänstinstansen som skapats.
Ett huvudnamn för tjänsten som är kopplad till programmet ska vara synligt. Välj den och spara åtkomstprincipsidan, som visas i följande skärmbild.

Eftersom programmet bara behöver hämta nycklar väljer du behörigheten **Hämta** i hemligheterna, vilket ger åtkomst samtidigt som de privilegier som beviljas minskas.

![Åtkomstprincip för nyckelvalv](./media/secure-aad-app/kv-access-policy.png)

*Skapa en princip för nyckelvalv*

Spara åtkomstprincipen och spara sedan den nya ändringen på fliken **Åtkomstprinciper** för att uppdatera principerna.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Distribuera Application Gateway med brandvägg för webbprogram aktiverat
I webbappar rekommenderas inte att du exponerar tjänster direkt för omvärlden på internet.
Belastningsutjämning och brandväggsregler ger mer säkerhet och kontroll över inkommande trafik och hjälper dig att hantera den.

Så här distribuerar du en Application Gateway-instans

1. Skapa resursgruppen för att hysa programgatewayen.
2. Etablera ett virtuellt nätverk som ska kopplas till gatewayen.
3. Skapa ett undernät för gatewayen i det virtuella nätverket.
4. Etablera en offentlig IP-adress.
5. Etablera programgatewayen.
6. Aktivera brandvägg för webbprogram i gatewayen.

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

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Distribuera Azure Web Apps
Med Azure App Service kan du skapa och vara värd för webbappar med hjälp av språk som Python, Ruby, C#och Java. Azure stöder också anpassade behållare, vilket kan tillåta praktiskt taget alla programmeringsspråk att köras på Azure App Service-plattformen.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Skapa en apptjänstplan på den kostnadsfria nivån
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Skapa en webbapp
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Innan du fortsätter går du till konfigurationsgränssnittet för Azure Domain https://aka.ms/appservicecustomdns Name System för din anpassade domän och följer instruktionerna för att konfigurera en CNAME-post för värdnamnet "www" och pekar på webbappens standarddomännamn

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Uppgradera App Service-planen till delad nivå (minimum krävs av anpassade domäner)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Lägga till ett eget domännamn i webbappen
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="network"></a>Nätverk
När du har slutfört distributionen har du en programgateway med brandvägg för webbprogram aktiverat.

Gateway-instansen exponerar port 443 för HTTPS. Den här konfigurationen säkerställer att vår app endast är tillgänglig på port 443 till HTTPS.

Att blockera oanvända portar och begränsa exponeringen på angreppsytan är en säkerhetspraxis.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Lägga till nätverkssäkerhetsgrupper i App Service-instansen

App Service-instanser kan integreras med virtuella nätverk. Med den här integreringen kan de konfigureras med principer för nätverkssäkerhetsgrupper som hanterar appens inkommande och utgående trafik.

1. Om du vill aktivera den här funktionen väljer du **Nätverk**under Inställningar under **Inställningar.** Konfigurera under **VNet-integrering**i den högra rutan .

   ![Ny integrering av virtuella nätverk](./media/secure-web-app/app-vnet-menu.png)

    *Ny integrering av virtuella nätverk för App Service*
1. På nästa sida väljer du **Lägg till VNET (förhandsgranskning)**.

1. På nästa meny väljer du det virtuella nätverk `aad-vnet`som skapas i distributionen som börjar med . Du kan antingen skapa ett nytt undernät eller välja ett befintligt.
   Skapa i så fall ett nytt undernät. Ange **adressintervallet** till **10.0.3.0/24** och ge undernätet **ett appundernät**ett namn.

   ![Konfiguration av virtuellt nätverk för App Service](./media/secure-web-app/app-vnet-config.png)

    *Konfiguration av virtuellt nätverk för App Service*

Nu när du har aktiverat integreringen av virtuella nätverk kan du lägga till nätverkssäkerhetsgrupper i vår app.

1. Använd sökrutan, sök efter **nätverkssäkerhetsgrupper**. Välj **Nätverkssäkerhetsgrupper** i resultatet.

    ![Sök efter nätverkssäkerhetsgrupper](./media/secure-web-app/nsg-search-menu.png)

    *Sök efter nätverkssäkerhetsgrupper*

2. Välj **Lägg till**på nästa meny . Ange **namnet** på NSG och **resursgruppen** där den ska placeras. Denna NSG kommer att tillämpas på programgatewayens undernät.

    ![Skapa en NSG](./media/secure-web-app/nsg-create-new.png)

    *Skapa en NSG*

3. När NSG har skapats markerar du den. Välj Regler för inkommande **säkerhet**under **Inställningar**i bladet . Konfigurera dessa inställningar så att anslutningar som kommer in i programgatewayen över port 443.

   ![Konfigurera NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurera NSG*

4. I de utgående reglerna för gateway NSG lägger du till en regel som tillåter utgående anslutningar till App Service-instansen genom att skapa en regel som är inriktad på servicetag`AppService`

   ![Lägga till utgående regler för NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Lägga till utgående regler för NSG*

    Lägg till ytterligare en utgående regel så att gatewayen kan skicka utgående regler till ett virtuellt nätverk.

   ![Lägga till ytterligare en utgående regel](./media/secure-web-app/nsg-outbound-vnet.png)

    *Lägga till ytterligare en utgående regel*

5. På undernätsbladet i NSG väljer du **Associera**, väljer det virtuella nätverk som skapats i distributionen och väljer gateway-undernätet med namnet **gw-undernät**. NSG tillämpas på undernätet.

6. Skapa en annan NSG som i det tidigare steget, den här gången för App Service-instansen. Ge det ett namn. Lägg till inkommande regeln för port 443 som du gjorde för programgateway NSG.

   Om du har en App Service-instans som distribueras på en App Service Environment-instans, vilket inte är fallet för den här appen, kan du lägga till inkommande regler för att tillåta Azure Service Health-avsökningar genom att öppna portar 454-455 på de inkommande säkerhetsgrupperna för din App Service NSG. Här är konfigurationen:

   ![Lägga till regler för Azure Service Health-avsökningar](./media/secure-web-app/nsg-create-healthprobes.png)

    *Lägga till regler för Azure Service Health-avsökningar (endast App Service Environment)*

Om du vill begränsa angreppsytan ändrar du apptjänstens nätverksinställningar så att endast programgatewayen kan komma åt programmet.
Om du vill använda inställningarna går du till fliken App Service-nätverk, väljer fliken **IP-begränsningar** och skapar en tillåt-regel som endast tillåter att programgatewayens IP direkt kan komma åt tjänsten. Du kan hämta IP-adressen för gatewayen från dess översiktssida. På fliken **IP-adress CIDR** anger du IP-adressen i det här formatet: `<GATEWAY_IP_ADDRESS>/32`.

![Tillåt endast gatewayen](./media/secure-web-app/app-allow-gw-only.png)

*Tillåt endast gateway-IP för åtkomst till App-tjänsten*

### <a name="azure-domain-name-system"></a>Azure-domännamnssystem 
Azure Domain Name System, eller Azure Domain Name System, ansvarar för att översätta (eller lösa) en webbplats eller tjänstnamn till sin IP-adress. Azure Domain Namehttps://docs.microsoft.com/azure/dns/dns-overview) System( är en värdtjänst för domännamnssystemdomäner som tillhandahåller namnmatchning med Azure-infrastruktur. Genom att vara värd för domäner i Azure kan användare hantera domännamnssystemposter med samma autentiseringsuppgifter, API:er, verktyg och fakturering som andra Azure-tjänster. Azure Domain Name System stöder också privata domännamnssystemdomäner.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption utnyttjar BitLocker-funktionen i Windows för att tillhandahålla volymkryptering för datadiskar. Lösningen integreras med Azure Key Vault för att styra och hantera diskkrypteringsnycklarna.

### <a name="identity-management"></a>Identitetshantering
Följande tekniker ger funktioner för att hantera åtkomst till kortinnehavarens data i Azure-miljön
- Azure Active Directory är Microsofts molnbaserade katalog- och identitetshanteringstjänst för flera innehavare. Alla användare för den här lösningen skapas i Azure Active Directory, inklusive användare som använder Azure WebApp.
- Azure-rollbaserad åtkomstkontroll gör det möjligt för administratörer att definiera detaljerade åtkomstbehörigheter för att bevilja endast den mängd åtkomst som användare behöver för att utföra sina jobb. I stället för att ge varje användare obegränsad behörighet för Azure-resurser kan administratörer endast tillåta vissa åtgärder för åtkomst till kortinnehavaredata. Prenumerationsåtkomsten är begränsad till prenumerationsadministratören.
- Azure Active Directory Privilegierad identitetshantering gör det möjligt för kunder att minimera antalet användare som har åtkomst till viss information, till exempel kortinnehavaredata. Administratörer kan använda Azure Active Directory Privileged Identity Management för att identifiera, begränsa och övervaka privilegierade identiteter och deras åtkomst till resurser. Den här funktionen kan också användas för att framtvinga administrativ åtkomst på begäran, just-in-time när det behövs.
- Azure Active Directory Identity Protection identifierar potentiella säkerhetsproblem som påverkar en organisations identiteter, konfigurerar automatiserade svar till identifierade misstänkta åtgärder relaterade till en organisations identiteter och undersöker misstänkta incidenter för att vidta lämpliga åtgärder för att lösa dem.
### <a name="secrets-management"></a>Hantering av hemligheter
Lösningen använder Azure Key Vault för hantering av nycklar och hemligheter. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Följande Azure Key Vault-funktioner hjälper kunder att skydda och komma åt sådana data
   - Principer för avancerad åtkomst konfigureras på behovsbasis.
   - Åtkomstprinciper för Key Vault definieras med minsta möjliga behörighet till nycklar och hemligheter.
   - Alla nycklar och hemligheter i Key Vault har utgångsdatum.
   - Alla nycklar i Key Vault skyddas av specialiserade maskinvarusäkerhetsmoduler. Nyckeltypen är en maskinvarusäkerhetsmodul (HSM) Skyddad 2048-bitars RSA-nyckel.
   - Med Key Vault kan du kryptera nycklar och hemligheter (t.ex. autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar. PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). 
   - Använd Rollbaserad åtkomstkontroll (RBAC) för att tilldela behörigheter till användare, grupper och program i ett visst omfång.     
   - Använd Key Vault för att hantera dina TLS-certifikat med autorenewal. 
   - Diagnostikloggar för Key Vault är aktiverade med en kvarhållningsperiod på minst 365 dagar.
   - Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs.
### <a name="azure-security-center"></a>Azure Security Center
Med Azure Security Center kan kunder centralt tillämpa och hantera säkerhetsprinciper över arbetsbelastningar, begränsa exponeringen för hot och identifiera och svara på attacker. Dessutom 
   - Azure Security Center har åtkomst till befintliga konfigurationer av Azure-tjänster för att tillhandahålla konfigurations- och tjänstrekommendationer för att förbättra säkerhetspositionen och skydda data.
   - Azure Security Center använder en mängd olika identifieringsfunktioner för att varna kunder om potentiella attacker som är inriktade på deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Azure Security Center har en uppsättning fördefinierade säkerhetsaviseringar som utlöses när ett hot eller misstänkt aktivitet äger rum. Anpassade varningsregler i Azure Security Center gör det möjligt för kunder att definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från deras miljö.
   - Azure Security Center tillhandahåller prioriterade säkerhetsaviseringar och incidenter, vilket gör det enklare för kunder att upptäcka och åtgärda potentiella säkerhetsproblem. En rapport om hotinformation genereras för varje upptäckt hot för att hjälpa incidenthanteringsgrupper att utreda och åtgärda hot.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Arkitekturen minskar risken för säkerhetsproblem med hjälp av en Azure Application Gateway med en brandvägg för webbprogram konfigurerad och OWASP-regeluppsättningen aktiverad. Ytterligare funktioner inkluderar
   - på TLS.
   - Inaktivera TLS v1.0 och v1.1.
   - Aktivera TLSv1.2.
   - Brandvägg för webbprogram (förebyggande läge).
   - Förebyggande läge med OWASP 3.0-regeluppsättning.
   - Aktivera diagnostikloggning.
   - Anpassade hälsoavsökningar.
   - Azure Security Center och en Azure Advisor ger ytterligare skydd och meddelanden. Azure Security Center tillhandahåller också ett ryktessystem.
### <a name="logging-and-auditing"></a>Loggning och granskning
Azure-tjänster loggar i stor utsträckning system- och användaraktivitet samt systemhälsa:
   - Aktivitetsloggar: [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) ger insikt i åtgärder som utförs på resurser i en prenumeration. Aktivitetsloggar kan hjälpa till att fastställa en operationsinitiator, förekomsttid och status.
   - Diagnostikloggar: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) innehåller alla loggar som avges av varje resurs. Dessa loggar inkluderar Windows händelsesystemloggar, Azure Storage-loggar, Key Vault granskningsloggar och Application Gateway åtkomst och brandväggsloggar. Alla diagnostikloggar skriv till ett centraliserat och krypterat Azure-lagringskonto för arkivering. Kvarhållningen är användarkonfigurerbar, upp till 730 dagar, för att uppfylla organisationsspecifika lagringskrav.
### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
   Dessa loggar konsolideras i [Azure Monitor-loggar](https://azure.microsoft.com/services/log-analytics/) för bearbetning, lagring och instrumentpanelsrapportering. När data har samlats in är de ordnade i separata tabeller för varje datatyp i Log Analytics-arbetsytor, vilket gör att alla data kan analyseras tillsammans oavsett dess ursprungliga källa. Dessutom integreras Azure Security Center med Azure Monitor-loggar som gör det möjligt för kunder att använda Kusto-frågor för att komma åt sina säkerhetshändelsedata och kombinera dem med data från andra tjänster.

   Följande [Azure-övervakningslösningar](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ingår som en del av den här arkitekturen

   - [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory Health Check-lösningen utvärderar risken och hälsan för servermiljöer med jämna mellanrum och ger en prioriterad lista med rekommendationer som är specifika för den distribuerade serverinfrastrukturen.
   - [Agenthälsa](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent health-lösningen rapporterar hur många agenter som distribueras och deras geografiska distribution, samt hur många agenter som inte svarar och antalet agenter som skickar operativa data.
   - [Aktivitetslogganalys:](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)Lösningen för Aktivitetslogganalys hjälper till med analys av Azure-aktivitetsloggar för alla Azure-prenumerationer för en kund.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)hjälper användare att spåra prestanda, upprätthålla säkerhet och identifiera trender genom att göra det möjligt för organisationer att granska, skapa aviseringar och arkivera data, inklusive spårning av API-anrop i sina Azure-resurser.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar Application Performance Management-tjänst för webbutvecklare på flera plattformar. Application Insights identifierar prestandaavvikelser och kunder kan använda den för att övervaka live-webbprogrammet. Den innehåller kraftfulla analysverktyg som hjälper kunderna att diagnostisera problem och förstå vad användarna faktiskt gör med sin app. Den är utformad för att hjälpa kunderna att kontinuerligt förbättra prestanda och användbarhet.

### <a name="azure-key-vault"></a>Azure Key Vault
   Skapa ett valv för den organisation där nycklar ska lagras och ansvarsskyldighet för operativa uppgifter som nedan

   - Data som lagras i Key Vault inkluderar   
   - Intraktningsnyckel för program
   - Åtkomstnyckel för datalagring
   - Anslutningssträng
   - Namn på datatabell
   - Användarreferenser
   - Principer för avancerad åtkomst konfigureras på behovsbasis
   - Åtkomstprinciper för Key Vault definieras med minsta möjliga behörighet till nycklar och hemligheter
   - Alla nycklar och hemligheter i Key Vault har utgångsdatum
   - Alla nycklar i Key Vault skyddas av maskinvarusäkerhetsmodul (HSM) [Nyckeltyp = maskinvarusäkerhetsmodul (HSM) Skyddad       
     2048-bitars RSA-nyckel]
   - Alla användare/identiteter beviljas lägsta obligatoriska behörigheter med hjälp av rollbaserad åtkomstkontroll (RBAC)
   - Program delar inte ett Key Vault om de inte litar på varandra och de behöver tillgång till samma hemligheter vid körning
   - Diagnostikloggar för Key Vault är aktiverade med en kvarhållningsperiod på minst 365 dagar.
   - Tillåtna kryptografiska åtgärder för nycklar är begränsade till de som krävs

### <a name="vpn-and-expressroute"></a>VPN och ExpressRoute
   En säker VPN-tunnel eller [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) behövde konfigureras genom att säkert upprätta en anslutning till de resurser som distribueras som en del av den här PaaS-referensarkitekturen för webbprogram. Genom att konfigurera en VPN eller ExpressRoute på lämpligt sätt kan kunderna lägga till ett skyddslager för data under överföring.

   Genom att implementera en säker VPN-tunnel med Azure kan en virtuell privat anslutning mellan ett lokalt nätverk och ett Virtuellt Azure-nätverk skapas. Den här anslutningen sker via Internet och gör det möjligt för kunder att säkert "tunnel" information i en krypterad länk mellan kundens nätverk och Azure. Site-to-Site VPN är en säker, mogen teknik som har distribuerats av företag av alla storlekar i årtionden. IPsec-tunnelläget används i det här alternativet som en krypteringsmekanism.

   Eftersom trafiken i VPN-tunneln korsar Internet med en plats-till-plats-VPN, erbjuder Microsoft ett annat, ännu säkrare anslutningsalternativ. Azure ExpressRoute är en dedikerad WAN-länk mellan Azure och en lokal plats eller en Exchange-värdleverantör. Eftersom ExpressRoute-anslutningar inte går via Internet erbjuder dessa anslutningar mer tillförlitlighet, snabbare hastigheter, lägre fördröjningar och högre säkerhet än typiska anslutningar via Internet. Eftersom detta är en direkt anslutning till kundens telekommunikationsleverantör, färdas uppgifterna inte över Internet och är därför inte exponerade för dem.

   Metodtips för att implementera ett säkert hybridnätverk som utökar ett lokalt nätverk till Azure är [tillgängliga](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

#### <a name="implement-azure-active-directory-oidc"></a>Implementera Azure Active Directory OIDC

1. Om du vill klona källkodsdatabasen använder du det här Git-kommandot

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Uppdatera url:erna för omdirigering
1.  Navigera tillbaka till Azure-portalen. I det vänstra navigeringsfönstret väljer du Azure Active Directory-tjänsten och väljer sedan Appregistreringar.
2.  På den resulterande skärmen väljer du WebApp-OpenIDConnect-DotNet-code-v2-programmet.
3.  På fliken Autentisering o I avsnittet Omdirigera URI:er väljer du Webben i kombinationsrutan och lägger till följande omdirigerings-URI:er.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o I avsnittet Avancerade inställningar anger du utloggnings-URL tillhttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  På fliken Branding o Uppdatera webbadressen till adressen till apptjänsten, till exempel https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net.
        o Spara konfigurationen.
5.  Om ditt program anropar ett webb-API, se till att tillämpa nödvändiga ändringar på projektet appsettings.json, så det anropar den publicerade API-URL:en i stället för localhost.
Publicera exemplet
    1.  Ladda ned publiceringsprofilen på fliken Översikt i Apptjänsten genom att klicka på länken Hämta publiceringsprofil och spara den. Andra distributionsmekanismer, till exempel från källkontroll, kan också användas.
    2.  Växla till Visual Studio och gå till WebApp-OpenIDConnect-DotNet-code-v2-projektet. Högerklicka på projektet i Lösningsutforskaren och välj Publicera. Klicka på Importera profil i det nedre fältet och importera publiceringsprofilen som du hämtade tidigare.
    3.  Klicka på Konfigurera och uppdatera mål-URL:en på fliken Anslutning så att den https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netär en https i webbadressen till startsidan, till exempel . Klicka på Nästa.
    4.  Kontrollera att Aktivera organisationsautentisering inte är markerat på fliken Inställningar. Klicka på Spara. Klicka på Publicera på huvudskärmen.
    5.  Visual Studio publicerar projektet och öppnar automatiskt en webbläsare för projektets URL. Om du ser projektets standardwebbsida lyckades publikationen.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementera multifaktorautentisering för Azure Active Directory
   Administratörer måste se till att prenumerationskontona i portalen är skyddade. Prenumerationen är sårbar för attacker eftersom den hanterar de resurser som du har skapat. För att skydda prenumerationen aktiverar du multifaktorautentisering på fliken **Azure Active Directory** i prenumerationen.

   En Azure AD fungerar baserat på principer som tillämpas på en användare eller grupper av användare som uppfyller vissa kriterier.
Azure skapar en standardprincip som anger att administratörer behöver tvåfaktorsautentisering för att logga in på portalen.
När du har aktiverat den här principen kan du uppmanas att logga ut och logga in på Azure-portalen igen.

Så här aktiverar du MFA för administratörs inloggningar

   1. Gå till fliken **Azure Active Directory** i Azure-portalen
   2. Välj villkorlig åtkomst under säkerhetskategorin. Du ser den här skärmen

       ![Villkorlig åtkomst - principer](./media/secure-aad-app/ad-mfa-conditional-add.png)

Om du inte kan skapa en ny princip

   1. Gå till fliken **MFA.**
   2. Välj en azure AD Premium **Free testversion** länk för att prenumerera på den kostnadsfria utvärderingsversionen.

   ![En kostnadsfri utvärderingsversion av Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Gå tillbaka till skärmen för villkorlig åtkomst.

   1. Välj den nya principfliken.
   2. Ange principens namn.
   3. Markera de användare eller grupper som du vill aktivera MFA för.
   4. Under **Access-kontroller**väljer du fliken **Bevilja** och väljer sedan **Kräv multifaktorautentisering** (och andra inställningar om du vill).

   ![Kräv MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   Du kan aktivera principen genom att markera kryssrutan högst upp på skärmen eller göra det på fliken **Villkorlig åtkomst.** När principen är aktiverad behöver användarna MFA för att logga in på portalen.

   Det finns en baslinjeprincip som kräver MFA för alla Azure-administratörer. Du kan aktivera den direkt i portalen. Om du aktiverar den här principen kan den aktuella sessionen bli ogiltig och du måste logga in igen.

   Om originalprincipen inte är aktiverad
   1.   Välj **Kräv MFA för administratörer**.
   2.   Välj **Använd princip omedelbart**.

   ![Välj Använd princip omedelbart](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Använda Azure Sentinel för att övervaka appar och resurser

   När ett program växer blir det svårt att aggregera alla säkerhetssignaler och mått som tas emot från resurser och göra dem användbara på ett åtgärdsorienterat sätt.

   Azure Sentinel är utformat för att samla in data, identifiera möjliga typer av hot och ge insyn i säkerhetsincidenter.
Medan azure Sentinel väntar på manuella åtgärder kan den förlita sig på förskrivna spelböcker för att starta aviseringar och incidenthanteringsprocesser.

   Exempelappen består av flera resurser som Azure Sentinel kan övervaka.
Om du vill konfigurera Azure Sentinel måste du först skapa en Log Analytics-arbetsyta som lagrar alla data som samlas in från de olika resurserna.

Så här skapar du den här arbetsytan

   1. Sök efter **Log Analytics**i sökrutan i Azure-portalen . Välj **Log Analytics-arbetsytor**.

   ![Sök efter log analytics-arbetsytor](./media/secure-aad-app/sentinel-log-analytics.png)

   *Sök efter log analytics-arbetsytor*

   2. På nästa sida väljer du **Lägg till** och anger sedan ett namn, en resursgrupp och en plats för arbetsytan.
   ![Skapa en Log Analytics-arbetsyta](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Skapa en Log Analytics-arbetsyta*

   3. Använd sökrutan för att söka efter **Azure Sentinel**.

   ![Sök efter Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Sök efter Azure Sentinel*

   4. Välj **Lägg till** och välj sedan arbetsytan Log Analytics som du skapade tidigare.

   ![Lägga till en log analytics-arbetsyta](./media/secure-aad-app/sentinel-workspace-add.png)

   *Lägga till en log analytics-arbetsyta*

   5. Välj Datakopplingar under **Konfiguration**på sidan **Azure Sentinel - Data-anslutningsappar** . **Data connectors** Du ser en matris med Azure-tjänster som du kan länka till log Analytics-lagringsinstansen för analys i Azure Sentinel.

   ![Logga Analytics-dataanslutningar](./media/secure-aad-app/sentinel-connectors.png)

      *Lägga till en dataanslutning i Azure Sentinel*

   Om du till exempel vill ansluta programgatewayen gör du så här:

   1. Öppna instansbladet azure application gateway.
   2. Under **Övervakning** väljer du **Diagnostikinställningar**.
   3. Välj **Lägg till diagnostikinställning**.

   ![Lägga till diagnostik för programgateway](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Lägga till diagnostik för programgateway*

   4. På sidan **Diagnostikinställningar** väljer du arbetsytan Log Analytics som du har skapat och väljer sedan alla mått som du vill samla in och skicka till Azure Sentinel. Välj **Spara**.

   ![Azure Sentinel-anslutningsinställningar](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Kostnadsöverväganden
   Om du inte redan har ett Azure-konto kan du skapa ett kostnadsfritt konto. Gå till den [kostnadsfria kontosidan](https://azure.microsoft.com/free/) för att komma igång, se vad du kan göra med ett kostnadsfritt Azure-konto och lär dig vilka produkter som är lediga i 12 månader.

   Om du vill distribuera resurserna i exempelappen med säkerhetsfunktionerna måste du betala för vissa premiumfunktioner. När appen skalas och de kostnadsfria nivåer och utvärderingsversioner som erbjuds av Azure måste uppgraderas för att uppfylla programmets krav kan dina kostnader öka. Använd [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta dina kostnader.

## <a name="next-steps"></a>Nästa steg
   Följande artiklar kan hjälpa dig att utforma, utveckla och distribuera säkra program.

- [Design](secure-design.md)
- [Utveckla](secure-develop.md)
- [Distribuera](secure-deploy.md)
