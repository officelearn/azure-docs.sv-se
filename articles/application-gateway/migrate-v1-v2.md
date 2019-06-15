---
title: Migrera Azure Application Gateway och Brandvägg för webbaserade program från v1 till v2
description: Den här artikeln visar hur du migrerar Azure Application Gateway och brandväggen för webbaserade program från v1 till v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 2387f2546afa9d5af2cb909a1e6a2179548e3b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053324"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrera Azure Application Gateway och Brandvägg för webbaserade program från v1 till v2

[Azure Application Gateway och Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) är nu tillgängligt erbjuder ytterligare funktioner som automatisk skalning och tillgänglighetszon redundans. Dock uppgraderas befintliga v1-gatewayerna inte automatiskt till v2. Om du vill migrera från v1 till v2, följer du stegen i den här artikeln.

Det finns två faser i en migrering:

1. Migrera konfigurationen
2. Migrera klienttrafiken

Den här artikeln beskriver konfigurationsmigreringen. Trafik för klientmigrering varierar beroende på din miljö. Men vissa på hög nivå, allmänna rekommendationer [tillhandahålls](#migrate-client-traffic).

## <a name="migration-overview"></a>Migrering – en översikt

Azure PowerShell-skript som gör följande:

* Skapar en ny Standard_v2 eller WAF_v2 gateway i ett virtuellt nätverksundernät som du anger.
* Sömlöst kopieras som är associerade med v1-Standard eller WAF-gateway till den nyligen skapade Standard_V2 eller WAF_V2 gatewayen-konfigurationen.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Den nya v2-gatewayen har nya offentliga och privata IP-adresser. Det går inte att flytta de IP-adresser som är associerade med den befintliga v1-gatewayen sömlöst till v2. Dock kan du allokera en befintlig (lediga) offentliga eller privata IP-adress till den nya v2-gatewayen.
* Du måste ange ett IP-adressutrymme för ett annat undernät inom ditt virtuella nätverk där din v1-gateway finns. Skriptet kan inte skapa v2-gateway i alla befintliga undernät som redan har en v1-gateway. Det befintliga undernätet har redan angetts en v2-gateway som fungerar fortfarande men det finns tillräckligt med IP-adress.
* Om du vill migrera en SSL-konfigurationen, måste du ange alla SSL-certifikat som används i din v1-gateway.
* Om du har FIPS-läge har aktiverats för din V1-gateway kommer inte att migreras till din nya v2-gateway. FIPS-läge stöds inte i v2.
* v2 stöder inte IPv6, så att IPv6 aktiverat v1 gatewayer inte migreras. Om du kör skriptet kan det kanske inte att slutföra.
* Om v1-gateway har endast en privat IP-adress, skapar skriptet en offentlig IP-adress och en privat IP-adress för den nya v2-gatewayen. v2-gatewayer stöder för närvarande inte bara privata IP-adresser.

## <a name="download-the-script"></a>Ladda ned skriptet

Ladda ned migreringsskriptet från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Använd skriptet

Det finns två alternativ för dig beroende på din lokala installation av PowerShell och inställningar:

* Om du inte har Azure Az-moduler som har installerats eller inte gör något avinstallera Azure Az-moduler, det bästa alternativet är att använda den `Install-Script` möjlighet att köra skriptet.
* Om du behöver Azure Az-moduler är lämpligast att ladda ned skriptet och kör den direkt.

För att avgöra om du har Azure Az-moduler som har installerats, kör `Get-InstalledModule -Name az`. Om du inte ser några installerade Az moduler så kan du använda den `Install-Script` metoden.

### <a name="install-using-the-install-script-method"></a>Installera med hjälp av metoden Install-Script

Om du vill använda det här alternativet om har du inte Azure Az-moduler som har installerats på datorn. Om de installeras, visar följande kommando ett fel. Du kan avinstallera Azure Az-moduler eller Använd ett annat alternativ för att ladda ned skriptet manuellt och kör den.
  
Kör skriptet med följande kommando:

`Install-Script -Name AzureAppGWMigration`

Det här kommandot installerar också Az-moduler som krävs.  

### <a name="install-using-the-script-directly"></a>Installera med hjälp av skript direkt

Om du har några Azure Az-moduler som har installerats och det går inte att avinstallera dem (eller inte vill avinstallera dem) kan du manuellt hämta skriptet med den **manuell hämtning** flik i skriptet länken. Skriptet laddas ned som en rå nupkg-fil. Skriptet från den här filen nupkg Se [manuell paketet hämta](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

Kör skriptet så här:

1. Använd `Connect-AzAccount` att ansluta till Azure.

1. Använd `Import-Module Az` att importera modulerna som Az.

1. Kör `Get-Help AzureAppGWMigration.ps1` att undersöka de obligatoriska parametrarna:

   `AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale`

   Parametrar för skriptet:
   * **resourceId: [String]: Krävs** – det här är Azure-resurs-ID för din befintliga Standard v1- eller WAF v1-gateway. För att hitta den här strängvärde, gå till Azure-portalen din Programgateway eller WAF-resursen och välj klickar du på den **egenskaper** länk för gatewayen. Resurs-ID finns på sidan.

     Du kan också köra följande Azure PowerShell-kommandon för att hämta resurs-ID:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:  Krävs** – det här är IP-adressutrymmet som du har tilldelat (eller om du vill allokera) för ett nytt undernät som innehåller din nya v2-gateway. Detta måste anges i CIDR-notation. Exempel: 10.0.0.0/24. Du behöver inte skapa det här undernätet i förväg. Skriptet skapas den åt dig om den inte finns.
   * **appgwName: [String]: Valfritt**. Det här är en sträng som du anger ska använda som namn på den nya Standard_v2 eller WAF_v2 gatewayen. Om den här parametern inte anges så används namnet på din befintliga v1-gateway med suffixet *_v2* sist.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Valfritt**.  En kommaavgränsad lista över PSApplicationGatewaySslCertificate-objekt som du har skapat för att representera SSL-certifikat från din v1-gateway måste överföras till den nya v2-gatewayen. För var och en av dina SSL-certifikat som har konfigurerats för Standard v1- eller WAF v1 gateway kan du skapa ett nytt PSApplicationGatewaySslCertificate-objekt via den `New-AzApplicationGatewaySslCertificate` kommandot som visas här. Du måste sökvägen till filen SSL-certifikat och lösenord.

       Den här parametern är endast valfritt om du inte har konfigurerats för din v1-gateway eller WAF HTTPS-lyssnare. Om du har minst en HTTPS-lyssnare installationen måste du ange den här parametern.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      Du kan skicka in `$mySslCert1, $mySslCert2` (kommaavgränsad) i det förra exemplet som värden för den här parametern i skriptet.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Valfritt**. En kommaavgränsad lista över PSApplicationGatewayTrustedRootCertificate-objekt som du har skapat för att representera den [betrodda rotcertifikat](ssl-overview.md) för autentisering av din serverdelsinstanser från din v2-gateway.  

      Om du vill skapa en lista över PSApplicationGatewayTrustedRootCertificate objekt [New AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Valfritt**. En specifik privat IP-adress som du vill koppla till din nya v2-gateway.  Detta måste vara från samma virtuella nätverk som du allokera för din nya v2-gateway. Om detta inte anges i skriptet allokerar en privat IP-adress för v2-gateway.
    * **publicIpResourceId: [String]: Valfritt**. Resurs-ID för en offentlig IP-adress (standard-SKU)-resurs i din prenumeration som du vill tilldela den nya v2-gatewayen. Om detta inte anges tilldelar skriptet en ny offentlig IP-adress i samma resursgrupp. Namnet är v2-gatewayens namn med *- IP* sist.
   * **validateMigration: [växla]: Valfritt**. Använd den här parametern om du vill att skriptet ska göra vissa grundkonfiguration jämförelse verifieringar efter v2 gateway skapande och kopiering konfiguration. Som standard görs ingen validering.
   * **enableAutoScale: [växla]: Valfritt**. Använd den här parametern om du vill att skriptet ska aktivera automatisk skalning på den nya v2-gatewayen när den har skapats. Automatisk skalning är inaktiverad som standard. Du kan alltid manuellt aktivera det senare på den nyligen skapade v2-gatewayen.

1. Kör skriptet med lämpliga parametrar. Det kan ta fem till sju minuter att slutföra.

    **Exempel**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceName "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrera klienttrafik

Kontrollera först att skriptet har skapat en ny v2-gateway med den exakta konfigurationen migreras under från v1-gateway. Du kan kontrollera detta från Azure-portalen.

Dessutom skicka en liten mängd trafik via v2-gateway som en manuell metod.
  
Här följer några scenarier där din aktuella application-gateway (Standard) får klienttrafik och våra rekommendationer för var och en:

* **En anpassad DNS-zon (till exempel contoso.com) som pekar på frontend-IP-adress (med en A-post) som är associerade med Standard v1- eller WAF v1 gateway**.

    Du kan uppdatera din DNS-post som pekar på frontend-IP-Adressen eller DNS-etiketten som är associerade med din Standard_v2 application gateway. Beroende på TTL-värdet som konfigurerats på din DNS-post, kan det ta en stund innan alla klienttrafik att migrera till din nya v2-gateway.
* **En anpassad DNS-zon (till exempel contoso.com) som pekar på DNS-etikett (till exempel: *myappgw.eastus.cloudapp.azure.com* med hjälp av en CNAME-post) som är associerade med v1-gateway**.

   Har du två alternativ:

  * Om du använder offentliga IP-adresser på application gateway, kan du göra en kontrollerad detaljerade migrering som använder en Traffic Manager-profil att stegvis dirigera trafik (viktad trafikroutningsmetod) till den nya v2-gatewayen.

    Du kan göra detta genom att lägga till DNS-etiketter för både v1 och v2 programgatewayer till den [Traffic Manager-profil](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method), och CNAMEing din anpassade DNS-post (till exempel www.contoso.com) till Traffic Manager-domän (till exempel Contoso.trafficmanager.NET).
  * Eller så kan du uppdatera din DNS-post för anpassad domän så att den pekar till DNS-etiketten för nya v2 application gateway. Beroende på TTL-värdet som konfigurerats på din DNS-post, kan det ta en stund innan alla klienttrafik att migrera till din nya v2-gateway.
* **Klienterna ansluter till frontend IP-adressen för din Programgateway**.

   Uppdatera dina klienter att använda IP-adresser som är associerade med den nyligen skapade v2 application gatewayen. Vi rekommenderar att du inte använder IP-adresser direkt. Överväg att använda DNS-namnsetikett (till exempel yourgateway.eastus.cloudapp.azure.com) som är associerade med din application gateway som du har CNAME-post till dina egna anpassade DNS-zon (till exempel contoso.com).

## <a name="common-questions"></a>Vanliga frågor

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Finns det några begränsningar med Azure PowerShell-skript för att migrera konfigurationen från v1 till v2?

Ja. Se [varningar/begränsningar](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Gäller den här artikeln och Azure PowerShell-skript för Application Gateway WAF-produkt? 

Ja.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell-skriptet också växla över trafik från min v1-gateway till den nyligen skapade v2-gatewayen?

Nej. Azure PowerShell-skriptet migrerar endast konfigurationen. Faktiska trafik migrering är ditt ansvar och kontroll över.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Är den nya v2-gatewayen som skapats av Azure PowerShell-skriptet lämplig storlek för att hantera all trafik som för närvarande hanteras av min v1-gateway?

Azure PowerShell-skriptet skapar en ny v2-gateway med en lämplig storlek för att hantera trafiken på din befintliga v1-gateway. Automatisk skalning är inaktiverat som standard, men du kan aktivera automatisk skalning när du kör skriptet.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Jag har konfigurerat min v1-gateway för att skicka loggar till Azure storage. Skriptet replikera den här konfigurationen för v2 samt?

Nej. Skriptet replikera inte den här konfigurationen för v2. Du måste lägga till loggningskonfigurationen separat migrerade v2-gateway.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Jag fick vissa problem med det här skriptet. Hur kan jag få hjälp?
  
Du kan skicka ett e-postmeddelande till appgwmigrationsup@microsoft.com, öppna ett supportärende med Azure-supporten eller bådadera.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
