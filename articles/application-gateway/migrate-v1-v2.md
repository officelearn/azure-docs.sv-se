---
title: Migrera från v1 till v2 - Azure Application Gateway
description: Den här artikeln visar hur du migrerar Azure Application Gateway och Web Application Firewall från v1 till v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 96f3825288846e86771ef3907eb4da4e58630df3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475173"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrera Azure Application Gateway och brandvägg för webbprogram från v1 till v2

[Azure Application Gateway och Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) är nu tillgänglig, som erbjuder ytterligare funktioner som automatisk skalning och tillgänglighetszonredundans. Befintliga v1-gatewayer uppgraderas inte automatiskt till v2. Om du vill migrera från v1 till v2 följer du stegen i den här artikeln.

Det finns två steg i en migrering:

1. Migrera konfigurationen
2. Migrera klienttrafiken

Den här artikeln beskriver konfigurationsmigrering. Klienttrafikmigrering varierar beroende på din specifika miljö. Vissa allmänna rekommendationer på hög nivå [ges](#migrate-client-traffic)dock.

## <a name="migration-overview"></a>Översikt över migrering

Ett Azure PowerShell-skript är tillgängligt som gör följande:

* Skapar en ny Standard_v2 eller WAF_v2 gateway i ett virtuellt nätverksundernät som du anger.
* Kopierar sömlöst konfigurationen som är associerad med v1 Standard- eller WAF-gatewayen till den nyskapade Standard_V2 eller WAF_V2 gatewayen.

### <a name="caveatslimitations"></a>Varningar\Begränsningar

* Den nya v2-gatewayen har nya offentliga och privata IP-adresser. Det går inte att flytta IP-adresser som är associerade med den befintliga v1-gatewayen sömlöst till v2. Du kan dock allokera en befintlig (oallokerad) offentlig eller privat IP-adress till den nya v2-gatewayen.
* Du måste ange ett IP-adressutrymme för ett annat undernät i det virtuella nätverket där v1-gatewayen finns. Skriptet kan inte skapa v2-gatewayen i befintliga undernät som redan har en v1-gateway. Men om det befintliga undernätet redan har en v2-gateway, som fortfarande kan fungera förutsatt att det finns tillräckligt med IP-adressutrymme.
* Om du vill migrera en SSL-konfiguration måste du ange alla SSL-certifikat som används i v1-gatewayen.
* Om du har FIPS-läge aktiverat för din V1-gateway migreras den inte till din nya v2-gateway. FIPS-läge stöds inte i v2.
* v2 stöder inte IPv6, så IPv6-aktiverade v1-gateways migreras inte. Om du kör skriptet kanske det inte slutförs.
* Om v1-gatewayen bara har en privat IP-adress skapar skriptet en offentlig IP-adress och en privat IP-adress för den nya v2-gatewayen. v2-gateways stöder för närvarande inte bara privata IP-adresser.
* Rubriker med namn som innehåller något annat än bokstäver, siffror, bindestreck och understreck skickas inte till ditt program. Detta gäller endast rubriknamn, inte rubrikvärden. Detta är en bryta förändring från V1.

## <a name="download-the-script"></a>Ladda ner skriptet

Hämta migreringsskriptet från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Använda skriptet

Det finns två alternativ för dig beroende på din lokala PowerShell-miljö inställning och inställningar:

* Om du inte har Azure Az-modulerna installerade, eller inte har något emot att avinstallera Azure `Install-Script` Az-modulerna, är det bästa alternativet att använda alternativet för att köra skriptet.
* Om du behöver behålla Azure Az-modulerna är det bäst att ladda ned skriptet och köra det direkt.

Ta reda på om du har Azure `Get-InstalledModule -Name az`Az-modulerna installerade kör du . Om du inte ser några installerade Az-moduler kan `Install-Script` du använda metoden.

### <a name="install-using-the-install-script-method"></a>Installera med metoden Install-Script

Om du vill använda det här alternativet får du inte ha Azure Az-modulerna installerade på datorn. Om de är installerade visas ett fel i följande kommando. Du kan antingen avinstallera Azure Az-modulerna eller använda det andra alternativet för att hämta skriptet manuellt och köra det.
  
Kör skriptet med följande kommando:

`Install-Script -Name AzureAppGWMigration`

Det här kommandot installerar också de Az-moduler som krävs.  

### <a name="install-using-the-script-directly"></a>Installera med skriptet direkt

Om du har några Azure Az-moduler installerade och inte kan avinstallera dem (eller inte vill avinstallera dem) kan du hämta skriptet manuellt med hjälp av fliken **Manuell hämtning** i länken för hämtning av skript. Skriptet hämtas som en rå nupkg-fil. Information om hur du installerar skriptet från den här nupkg-filen finns i [Hämta manuellt paket](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Kör skriptet så här:

1. Används `Connect-AzAccount` för att ansluta till Azure.

1. Används `Import-Module Az` för att importera Az-modulerna.

1. Kör `Get-Help AzureAppGWMigration.ps1` för att undersöka de parametrar som krävs:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parametrar för skriptet:
   * **resourceId: [String]: Obligatoriskt** - Det här är Azure Resource ID för din befintliga Standard v1- eller WAF v1-gateway. Om du vill hitta det här strängvärdet navigerar du till Azure-portalen, väljer programgateway eller WAF-resurs och klickar på länken **Egenskaper** för gatewayen. Resurs-ID finns på den sidan.

     Du kan också köra följande Azure PowerShell-kommandon för att hämta resurs-ID:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **undernätAdressRange: [String]: Obligatoriskt** - Det här är det IP-adressutrymme som du har allokerat (eller vill allokera) för ett nytt undernät som innehåller din nya v2-gateway. Detta måste anges i CIDR-notationen. Till exempel: 10.0.0.0/24. Du behöver inte skapa det här undernätet i förväg. Skriptet skapar det åt dig om det inte finns.
   * **appgwName: [String]: Valfritt**. Det här är en sträng som du anger att använda som namn för den nya Standard_v2 eller WAF_v2 gatewayen. Om den här parametern inte anges används namnet på den befintliga v1-gatewayen med suffixet *_v2* bifogat.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Valfritt**.  En kommaavgränsad lista över PSApplicationGatewaySslCertificate-objekt som du skapar för att representera SSL-cert från din v1-gateway måste överföras till den nya v2-gatewayen. För var och en av dina SSL-certs som konfigurerats för din Standard v1- eller WAF v1-gateway kan du skapa ett nytt PSApplicationGatewaySslCertificate-objekt via `New-AzApplicationGatewaySslCertificate` kommandot som visas här. Du behöver sökvägen till ssl-filen och lösenordet.

     Den här parametern är bara valfri om du inte har HTTPS-lyssnare konfigurerade för din v1-gateway eller WAF. Om du har minst en INSTÄLLNING FÖR HTTPS-lyssnare måste du ange den här parametern.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Du kan `$mySslCert1, $mySslCert2` skicka in (kommaavgränsad) i föregående exempel som värden för den här parametern i skriptet.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Valfritt**. En kommaavgränsad lista över PSApplicationGatewayTrustedRootCertificate-objekt som du skapar för att representera [betrodda rotcertifikat](ssl-overview.md) för autentisering av serverdelsinstanser från din v2-gateway.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Mer om du vill skapa en lista över PSApplicationGatewayTrustedRootCertificate-objekt finns i [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Valfritt**. En specifik privat IP-adress som du vill associera till din nya v2-gateway.  Detta måste vara från samma virtuella nätverk som du allokerar för din nya v2-gateway. Om detta inte anges allokerar skriptet en privat IP-adress för din v2-gateway.
   * **publicIpResourceId: [String]: Valfritt**. ResursEn för befintlig offentlig IP-adressresurs (standard-SKU) i prenumerationen som du vill allokera till den nya v2-gatewayen. Om detta inte anges allokerar skriptet en ny offentlig IP-adress i samma resursgrupp. Namnet är v2-gatewayens namn med *-IP* bifogat.
   * **validateMigration: [switch]: Valfritt**. Använd den här parametern om du vill att skriptet ska göra några grundläggande valideringar för konfigurationsjämlikationer när v2-gatewayen har skapats och konfigurationskopian. Som standard görs ingen validering.
   * **enableAutoScale: [switch]: Valfritt**. Använd den här parametern om du vill att skriptet ska aktivera automatisk skalning på den nya v2-gatewayen när den har skapats. Som standard är automatisk skalning inaktiverad. Du kan alltid manuellt aktivera den senare på den nyligen skapade v2-gatewayen.

1. Kör skriptet med lämpliga parametrar. Det kan ta fem till sju minuter att avsluta.

    **Exempel**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrera klienttrafik

Kontrollera först att skriptet har skapat en ny v2-gateway med den exakta konfigurationen som har migrerats över från din v1-gateway. Du kan verifiera detta från Azure-portalen.

Skicka också en liten mängd trafik via v2-gatewayen som ett manuellt test.
  
Här är några scenarier där din nuvarande programgateway (Standard) kan ta emot klienttrafik och våra rekommendationer för var och en:

* **En anpassad DNS-zon (till exempel contoso.com) som pekar på ip-adressen för klientdelen (med en A-post) som är associerad med din Standard v1- eller WAF v1-gateway**.

    Du kan uppdatera DNS-posten så att den pekar på den klient-IP- eller DNS-etikett som är kopplad till Standard_v2 programgateway. Beroende på den TTL som konfigurerats på DNS-posten kan det ta ett tag innan all klienttrafik migrerar till den nya v2-gatewayen.
* **En anpassad DNS-zon (till exempel contoso.com) som pekar på DNS-etiketten (till exempel: *myappgw.eastus.cloudapp.azure.com* med en CNAME-post) som är associerad med din v1-gateway**.

   Du har två alternativ:

  * Om du använder offentliga IP-adresser på programgatewayen kan du göra en kontrollerad, detaljerad migrering med hjälp av en Traffic Manager-profil för att stegvis dirigera trafik (viktad trafikroutningsmetod) till den nya v2-gatewayen.

    Du kan göra detta genom att lägga till DNS-etiketterna för både v1- och v2-programgateways `www.contoso.com`i [Traffic Manager-profilen](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)och CNAMEing din anpassade DNS-post (till exempel) i Traffic Manager-domänen (till exempel contoso.trafficmanager.net).
  * Du kan också uppdatera den anpassade DNS-posten för domänen så att den pekar på DNS-etiketten för den nya v2-programgatewayen. Beroende på den TTL som konfigurerats på DNS-posten kan det ta ett tag innan all klienttrafik migrerar till den nya v2-gatewayen.
* **Dina klienter ansluter till klientsidans IP-adress för programgatewayen**.

   Uppdatera dina klienter för att använda IP-adressen(er) som är associerad med den nyligen skapade v2-programgatewayen. Vi rekommenderar att du inte använder IP-adresser direkt. Överväg att använda DNS-namnetiketten (till exempel yourgateway.eastus.cloudapp.azure.com) som är associerad med programgatewayen och som du kan CNAME till din egen anpassade DNS-zon (till exempel contoso.com).

## <a name="common-questions"></a>Vanliga frågor

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Finns det några begränsningar med Azure PowerShell-skriptet för att migrera konfigurationen från v1 till v2?

Ja. Se [varningar/begränsningar](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Är den här artikeln och Azure PowerShell-skriptet tillämpliga för Application Gateway WAF-produkt också? 

Ja.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Växlar Azure PowerShell-skriptet också över trafiken från min v1-gateway till den nyligen skapade v2-gatewayen?

Nej. Azure PowerShell-skriptet migrerar bara konfigurationen. Faktisk trafikmigrering är ditt ansvar och i din kontroll.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Är den nya v2-gatewayen som skapats av Azure PowerShell-skriptet dimensionerad på rätt sätt för att hantera all trafik som för närvarande betjänas av min v1-gateway?

Azure PowerShell-skriptet skapar en ny v2-gateway med lämplig storlek för att hantera trafiken på din befintliga v1-gateway. Automatisk skalning är inaktiverat som standard, men du kan aktivera automatisk skalning när du kör skriptet.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Jag konfigurerade min v1-gateway för att skicka loggar till Azure-lagring. Replikerar skriptet även den här konfigurationen för v2?

Nej. Skriptet replikerar inte den här konfigurationen för v2. Du måste lägga till loggkonfigurationen separat i den migrerade v2-gatewayen.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Stöder skriptet certifikat som överförts till Azure KeyVault ?

Nej. För närvarande stöder skriptet inte certifikat i KeyVault. Detta övervägs dock för en framtida version.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Jag stötte på några problem med att använda detta skript. Hur kan jag få hjälp?
  
Du kan skicka appgwmigrationsup@microsoft.comett e-postmeddelande till , öppna ett supportärende med Azure Support eller göra båda.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
