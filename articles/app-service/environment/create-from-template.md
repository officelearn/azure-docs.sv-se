---
title: Skapa en Azure App Service-miljö med hjälp av Resource Manager-mall
description: Beskriver hur du skapar en extern eller ILB Azure App Service-miljö med hjälp av Resource Manager-mall
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 92422a254bcfd5b31731dda6d1790cc85f467860
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649674"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Skapa en ASE med en Azure Resource Manager-mall

## <a name="overview"></a>Översikt
Azure App Service-miljöer (ase) kan skapas med en internet-tillgänglig slutpunkt eller en slutpunkt på en intern adress i en Azure-nätverk (VNet). När du skapade med en intern slutpunkt, tillhandahålls att slutpunkten av en Azure-komponent som kallas en intern belastningsutjämnare (ILB). Ase: N på en intern IP-adress kallas för en ILB ASE. ASE med en offentlig slutpunkt kallas för en extern ASE. 

En ASE kan skapas med hjälp av Azure-portalen eller en Azure Resource Manager-mall. Den här artikeln beskriver stegen och syntax som du behöver skapa en extern ASE eller ILB ASE med Resource Manager-mallar. Läs hur du skapar en ase-miljö i Azure-portalen i [gör en extern ASE] [ MakeExternalASE] eller [gör en ILB ASE][MakeILBASE].

När du skapar en ase-miljö i Azure-portalen kan du skapa ditt virtuella nätverk på samma gång eller välj ett befintligt virtuellt nätverk att distribuera till. När du skapar en ASE från en mall måste du börja med: 

* Ett virtuellt nätverk i Resource Manager.
* Ett undernät i det virtuella nätverket. Vi rekommenderar en storlek för ASE-undernät på `/24` med 256-adresser för framtida tillväxt och skalningsbehov. När ASE har skapats kan ändra du inte storleken.
* Resurs-ID från ditt VNet. Du kan hämta den här informationen från Azure-portalen under Egenskaper för virtuellt nätverk.
* Den prenumeration som du vill distribuera till.
* Den plats som du vill distribuera till.

Automatisera din ASE-generering:

1. Skapa ASE från en mall. Om du skapar en extern ASE, är du klar efter det här steget. Om du skapar en ILB ASE, finns det några saker att göra.

2. När din ILB ASE har skapats kan överföra ett SSL-certifikat som matchar din ILB ASE-domän.

3. Överfört SSL-certifikat är tilldelad till ILB ASE som dess ”standard” SSL-certifikat.  Det här certifikatet används för SSL-trafik till appar på ILB ASE när de använder vanliga rotdomänen som är tilldelad till ase: N (till exempel https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Skapa ASE
En Resource Manager-mall som skapar en ASE och dess associerade parameterfilen finns [i ett exempel] [ quickstartasev2create] på GitHub.

Om du vill göra en ILB ASE använder dessa Resource Manager-mall [exempel][quickstartilbasecreate]. De extra som använder. De flesta av parametrarna i den *azuredeploy.parameters.json* fil är gemensamma för att skapa ILB ase-miljöer och extern ase-miljöer. I följande lista anropar out-parametrar Observera särskilt eller som är unika, när du skapar en ILB ASE:

* *internalLoadBalancingMode*: I de flesta fall ange detta till 3, vilket innebär att både HTTP/HTTPS-trafik på portarna 80/443 och kontrolldata/channel-portar har lyssnat på via FTP-tjänsten på ASE, kommer att bindas till en ILB-allokerade virtuella nätverket internt adress. Om den här egenskapen anges till 2 är endast FTP tjänstrelaterade portarna (både kontroll- och kanaler) bundna till ILB-adress. Det finns kvar i det offentliga VIP HTTP/HTTPS-trafik.
* *dnsSuffix*: den här parametern definierar standard-rotdomän som har tilldelats ASE. I den offentliga variationen av Azure App Service standard rotdomänen för alla webbappar är *azurewebsites.net*. Eftersom en ILB ASE är interna för en kundens virtuella nätverk kan meningsfullt det inte att använda tjänsten offentlig standard rotdomänen. I stället bör en ILB ASE ha en standard-rotdomän som passar för användning i ett företags internt virtuellt nätverk. Contoso Corporation kan till exempel använda en standard-rotdomänen för *intern contoso.com* för appar som är avsedda att vara matchningsbara och endast åtkomlig inom Contosos virtuella nätverk. 
* *ipSslAddressCount*: parametern automatiskt som standard värdet 0 i den *azuredeploy.json* filen eftersom ILB ase har endast en ILB-adress. Det finns inga explicita IP SSL-adresser för en ILB ASE. Därför måste IP SSL-adresspool för en ILB ASE anges till noll. I annat fall uppstår ett etablering fel. 

Efter den *azuredeploy.parameters.json* fil är ifyllt, skapa ASE med hjälp av PowerShell-kodfragmentet. Ändra sökvägar för att matcha Resource Manager-mallfilen platser på din dator. Kom ihåg att ange dina egna värden för Resource Manager distributionens namn och resursgruppens namn:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Det tar ungefär en timme för ASE som ska skapas. Sedan ASE som visas i portalen i listan med ase-miljöer för den prenumeration som utlöste distributionen.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Överför och konfigurerar SSL-certifikat ”standard”
Ett SSL-certifikat måste vara associerad med ASE som ”standard” SSL-certifikatet som används för att upprätta en SSL-anslutningar till appar. Om den ASE standard-DNS-suffixet är *intern contoso.com*, en anslutning till https://some-random-app.internal-contoso.com kräver ett SSL-certifikat som är giltig för **.internal contoso.com*. 

Skaffa ett giltigt SSL-certifikat med hjälp av interna certifikatutfärdare, köp ett certifikat från en extern utfärdare eller använder ett självsignerat certifikat. Följande certifikatattribut måste konfigureras korrekt oavsett källan för SSL-certifikat:

* **Ämne**: det här attributet måste anges till **.your-root-domain-here.com*.
* **Alternativt namn för certifikatmottagare**: det här attributet måste innehålla både **.your-root-domain-here.com* och **.SCM.Your-root-domain-here.com*. SSL-anslutningar till SCM/Kudo-sajten för varje app använder en adress i formatet *your-app-name.scm.your-root-domain-here.com*.

Med ett giltigt SSL-certifikat i hand behövs två ytterligare förberedande steg. Konvertera/spara SSL-certifikatet som en .pfx-fil. Kom ihåg att .pfx-filen måste innehålla alla mellanliggande och rotcertifikat. Skydda den med ett lösenord.

PFX-filen måste konverteras till en base64-sträng eftersom SSL-certifikatet har laddats upp av en Resource Manager-mall. Eftersom Resource Manager-mallar är textfiler, måste du konvertera PFX-filen till en base64-sträng. På så sätt kan det vara ingår som en parameter för mallen.

Använd följande PowerShell-kodavsnitt till:

* Generera ett självsignerat certifikat.
* Exportera certifikatet som en .pfx-fil.
* Konvertera PFX-filen till en base64-kodad sträng.
* Spara base64-kodad sträng i en separat fil. 

Den här PowerShell-koden för base64-kodning bygger på den [PowerShell-skript blogg][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

När SSL-certifikatet har genereras och konverteras till en base64-kodad sträng, använda Resource Manager-exempelmall [Konfigurera SSL-standardcertifikatet] [ quickstartconfiguressl] på GitHub. 

Parametrarna i den *azuredeploy.parameters.json* filen visas här:

* *appServiceEnvironmentName*: namnet på den ILB ASE som håller på att konfigureras.
* *existingAseLocation*: textsträng som innehåller Azure-regionen där ILB ASE har distribuerats.  Till exempel: ”USA, södra centrala”.
* *pfxBlobString*: Base 64-kodad sträng-representation av PFX-filen. Använd kodfragmentet ovan och kopiera den sträng som finns i ”exportedcert.pfx.b64”. Klistra in den som värde för den *pfxBlobString* attribut.
* *lösenord*: lösenordet som används för att skydda den .pfx-fil.
* *certificateThumbprint*: certifikatets tumavtryck. Om du hämtar värdet från PowerShell (till exempel *$certificate. Tumavtrycket* från tidigare kodfragmentet), du kan använda värdet som är. Om du kopierar värdet från dialogrutan för Windows-certifikat, kan du komma ihåg att ta bort extra blanksteg. Den *certificateThumbprint* bör se ut ungefär som AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: ett eget strängidentifierare en egen används för att identitet certifikatet. Namnet används som en del av den unika identifieraren för Resource Manager för den *Microsoft.Web/certificates* entitet som representerar SSL-certifikatet. Namnet *måste* sluta med följande suffix: \_yourASENameHere_InternalLoadBalancingASE. Azure-portalen använder suffixet som indikerar att certifikatet används för att skydda en Apptjänstmiljö aktiverad ASE.

Ett förkortat exempel på *azuredeploy.parameters.json* visas här:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Efter den *azuredeploy.parameters.json* fil är ifyllt, konfigurera standard-SSL-certifikatet med hjälp av PowerShell-kodfragmentet. Ändra sökvägar för att matcha där mallfiler för Resource Manager finns på din dator. Kom ihåg att ange dina egna värden för Resource Manager distributionens namn och resursgruppens namn:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Det tar ungefär 40 minuter per ASE klientdel att tillämpa ändringen. För en standardstorlek ASE som använder två klientdelar, tar mallen exempelvis cirka en timme och 20 minuter för att slutföra. När mallen körs skala inte ASE.  

När mallen är klar kan appar på ILB ASE nås via HTTPS. Anslutningarna är skyddade med hjälp av SSL-standardcertifikatet. SSL-standardcertifikatet används när appar på ILB ASE åtgärdas genom att använda en kombination av programnamnet plus standardvärdnamnet. Till exempel https://mycustomapp.internal-contoso.com använder SSL-standardcertifikatet för **.internal contoso.com*.

Utvecklare kan dock precis som appar som körs på den offentliga multitenant-tjänsten, konfigurera anpassade värdnamn för enskilda appar. De kan också konfigurera unika SNI SSL-certifikatbindningar för enskilda appar.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
App Service Environment finns i två versioner: ASEv1 och ASEv2. Informationen ovan baserades på ASEv2. Det här avsnittet visar skillnaderna mellan ASEv1 och ASEv2.

I ASEv1 måste hantera du alla resurser manuellt. Det omfattar klientdelar, arbetare och IP-adresser som används för IP-baserad SSL. Innan du kan skala ut App Service-planen, måste du skala ut arbetarpoolen som du vill ha den.

ASEv1 använder en annan prissättningsmodell än ASEv2. I ASEv1 betalar du för varje allokerad vCPU. Det omfattar vcpu: er som används för klientdelar eller arbetare som inte är värd för alla arbetsbelastningar. I ASEv1 är den högsta skalstorleken för en ASE-miljö totalt 55 värdar. Det omfattar arbetare och klientdelar. En fördel med ASEv1 är att det går att distribuera i ett klassiskt virtuellt nätverk och ett virtuellt Resource Manager-nätverk. Mer information om ASEv1 finns i [App Service Environment v1 introduction][ASEv1Intro] (Introduktion till App Service Environment v1).

För att skapa en ASEv1 med hjälp av Resource Manager-mall, se [skapa en ILB ASE v1 med en Resource Manager-mall][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
