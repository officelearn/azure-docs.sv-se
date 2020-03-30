---
title: Skapa en ASE med ARM
description: Lär dig hur du skapar en extern tjänst eller ILB App Service-miljö med hjälp av en Azure Resource Manager-mall.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1212e77db5e0ec83f8dd966a14872a682b3e0202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295541"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Skapa en ASE med hjälp av en Azure Resource Manager-mall

## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service-miljöer (ASE) kan skapas med en internettillgänglig slutpunkt eller en slutpunkt på en intern adress i ett virtuellt Azure-nätverk (VNet). När den skapas med en intern slutpunkt tillhandahålls slutpunkten av en Azure-komponent som kallas en intern belastningsutjämnare (ILB). ASE på en intern IP-adress kallas en ILB ASE. ASE med en offentlig slutpunkt kallas en extern ASE. 

En ASE kan skapas med hjälp av Azure-portalen eller en Azure Resource Manager-mall. Den här artikeln går igenom de steg och den syntax du behöver för att skapa en extern ASE- eller ILB ASE med Resource Manager-mallar. Mer information om hur du skapar en ASE i Azure-portalen finns i [Skapa en extern ASE][MakeExternalASE] eller [Skapa en ILB ASE][MakeILBASE].

När du skapar en ASE i Azure-portalen kan du skapa ditt virtuella nätverk samtidigt eller välja ett befintligt virtuella nätverk att distribuera till. När du skapar en ASE från en mall måste du börja med: 

* Ett resurshanterarens virtuella nätverk.
* Ett undernät i det virtuella nätverket. Vi rekommenderar en ASE-undernätsstorlek `/24` med 256 adresser för att tillgodose framtida tillväxt- och skalningsbehov. När ASE har skapats kan du inte ändra storleken.
* Resurs-ID:t från ditt virtuella nätverk. Du kan hämta den här informationen från Azure-portalen under dina virtuella nätverksegenskaper.
* Prenumerationen som du vill distribuera till.
* Den plats som du vill distribuera till.

Så här automatiserar du din ASE-skapande:

1. Skapa ASE från en mall. Om du skapar en extern ASE är du klar efter det här steget. Om du skapar en ILB ASE, det finns några fler saker att göra.

2. När din ILB ASE har skapats överförs ett SSL-certifikat som matchar din ILB ASE-domän.

3. Det uppladdade SSL-certifikatet tilldelas ILB ASE som sitt "standard" SSL-certifikat.  Det här certifikatet används för SSL-trafik till appar på ILB ASE när de använder den `https://someapp.mycustomrootdomain.com`gemensamma rotdomän som har tilldelats ASE (till exempel ).


## <a name="create-the-ase"></a>Skapa ASE
En Resource Manager-mall som skapar en ASE- och dess associerade parameterfil är tillgänglig [i ett exempel][quickstartasev2create] på GitHub.

Om du vill göra en ILB ASE använder du dessa [exempel på][quickstartilbasecreate]Resource Manager-mall . De tillgodoser det användningsfallet. De flesta parametrarna i *filen azuredeploy.parameters.json* är gemensamma för skapandet av ILB ASEs och externa ASE.Most of the parameters in the azuredeploy.parameters.json file are common to the creation of ILB ASEs and External ASEs. Följande lista ropar ut parametrar för särskild anteckning, eller som är unika, när du skapar en ILB ASE:

* *internalLoadBalancingMode*: I de flesta fall ställer du in detta på 3, vilket innebär att både HTTP/HTTPS-trafik på portar 80/443 och de kontroll-/datakanalportar som ftp-tjänsten lyssnar på på ASE, kommer att vara bundna till en intern ILB-allokerad intern adress för virtuellt nätverk. Om den här egenskapen är inställd på 2 är endast FTP-tjänstrelaterade portar (både kontroll- och datakanaler) bundna till en ILB-adress. HTTP/HTTPS-trafiken finns kvar på den offentliga VIP-webbplatsen.
* *dnsSuffix*: Den här parametern definierar standardrotdomänen som har tilldelats ASE. I den offentliga varianten av Azure App Service är standardrotdomänen för alla webbappar *azurewebsites.net*. Eftersom en ILB ASE är intern i en kunds virtuella nätverk är det inte meningsfullt att använda den offentliga tjänstens standardrotdomän. I stället bör en ILB ASE ha en standardrotdomän som är meningsfull för användning inom ett företags interna virtuella nätverk. Contoso Corporation kan till exempel använda en standardrotdomän för *internal-contoso.com* för appar som är avsedda att vara resolvable och endast tillgängliga i Contosos virtuella nätverk. 
* *ipSslAddressCount*: Den här parametern standards automatiskt till ett värde av 0 i *filen azuredeploy.json* eftersom ILB ASEs bara har en enda ILB-adress. Det finns inga explicita IP-SSL-adresser för en ILB ASE. Därför måste IP-SSL-adresspoolen för en ILB ASE anges till noll. Annars uppstår ett etableringsfel. 

När *filen azuredeploy.parameters.json* har fyllts i skapar du ASE med hjälp av PowerShell-kodavsnittet. Ändra filsökvägarna så att de matchar Resource Manager-mall-filplatserna på datorn. Kom ihåg att ange dina egna värden för Resurshanterarens distributionsnamn och resursgruppsnamnet:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Det tar ungefär en timme för ASE att skapas. Sedan visas ASE i portalen i listan över ASE för prenumerationen som utlöste distributionen.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Ladda upp och konfigurera SSL-certifikatet "standard"
Ett SSL-certifikat måste associeras med ASE som det "standard" SSL-certifikat som används för att upprätta SSL-anslutningar till appar. Om ASE:s standard-DNS-suffix *är internal-contoso.com*kräver `https://some-random-app.internal-contoso.com` en anslutning till ett SSL-certifikat som är giltigt för **.internal-contoso.com*. 

Skaffa ett giltigt SSL-certifikat med hjälp av interna certifikatutfärdare, köpa ett certifikat från en extern utfärdare eller använda ett självsignerat certifikat. Oavsett källan till SSL-certifikatet måste följande certifikatattribut konfigureras korrekt:

* **Ämne**: Det här attributet måste anges till **.your-root-domain-here.com*.
* **Ämnesalternativnamn:** Det här attributet måste innehålla både **.your-root-domain-here.com* och **.scm.your-root-domain-here.com*. SSL-anslutningar till SCM/Kudu-webbplatsen som är associerad med varje app använder en adress till formuläret *your-app-name.scm.your-root-domain-here.com*.

Med ett giltigt SSL-certifikat i handen krävs ytterligare två förberedande steg. Konvertera/spara SSL-certifikatet som en .pfx-fil. Kom ihåg att PFX-filen måste innehålla alla mellanliggande certifikat och rotcertifikat. Skydda den med ett lösenord.

PFX-filen måste konverteras till en base64-sträng eftersom SSL-certifikatet överförs med hjälp av en Resource Manager-mall. Eftersom Resource Manager-mallar är textfiler måste PFX-filen konverteras till en base64-sträng. På så sätt kan det inkluderas som en parameter i mallen.

Använd följande PowerShell-kodavsnitt för att:

* Generera ett självsignerat certifikat.
* Exportera certifikatet som en PFX-fil.
* Konvertera PFX-filen till en base64-kodad sträng.
* Spara den base64-kodade strängen i en separat fil. 

Denna PowerShell-kod för base64 kodning har anpassats från [PowerShell skript blogg:][examplebase64encoding]

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

När SSL-certifikatet har genererats och konverterats till en base64-kodad sträng använder du exempelmallen Resource Manager [Konfigurera standard-SSL-certifikatet][quickstartconfiguressl] på GitHub. 

Parametrarna i filen *azuredeploy.parameters.json* visas här:

* *appServiceEnvironmentName*: Namnet på den ILB ASE som konfigureras.
* *existingAseLocation*: Textsträng som innehåller Azure-regionen där ILB ASE har distribuerats.  Till exempel: "Södra centrala USA".
* *pfxBlobString*: Den based64-kodade strängrepresentationen av PFX-filen. Använd kodavsnittet som visas tidigare och kopiera strängen i "exportedcert.pfx.b64". Klistra in den som värdet på *attributet pfxBlobString.*
* *lösenord*: Lösenordet som används för att skydda PFX-filen.
* *certifikatDumbprint*: Certifikatets tumavtryck. Om du hämtar det här värdet från PowerShell (till exempel *$certificate. Tumavtryck* från det tidigare kodavsnittet) kan du använda värdet som det är. Om du kopierar värdet från dialogrutan Windows-certifikat bör du komma ihåg att ta bort de främmande utrymmena. *CertifikatetThumbprint* ska se ut ungefär som AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: En egen strängidentifierare som du själv väljer används för att identifiera certifikatet. Namnet används som en del av den unika Resurshanteraren-identifieraren för den entity för *Microsoft.Web/certificates* som representerar SSL-certifikatet. Namnet *måste* sluta med följande suffix: \_yourASENameHere_InternalLoadBalancingASE. Azure-portalen använder det här suffixet som en indikator på att certifikatet används för att skydda en ILB-aktiverad ASE.

Ett förkortat exempel *på azuredeploy.parameters.json* visas här:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
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

När *filen azuredeploy.parameters.json* har fyllts i konfigurerar du standard-SSL-certifikatet med hjälp av PowerShell-kodavsnittet. Ändra filsökvägarna så att de matchar var Resource Manager-mallfilerna finns på datorn. Kom ihåg att ange dina egna värden för Resurshanterarens distributionsnamn och resursgruppsnamnet:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Det tar ungefär 40 minuter per ASE-front för att tillämpa ändringen. För en ASE-standardstorlek som använder två främre ändar tar mallen till exempel cirka en timme och 20 minuter att slutföra. När mallen körs kan ASE inte skalas.  

När mallen är klar kan appar på ILB ASE nås via HTTPS. Anslutningarna skyddas med hjälp av standard-SSL-certifikatet. Standard-SSL-certifikatet används när appar på ILB ASE adresseras med hjälp av en kombination av programnamnet plus standardvärdens namn. Till exempel `https://mycustomapp.internal-contoso.com` använder standard SSL-certifikat för **.internal-contoso.com*.

Men precis som appar som körs på den offentliga tjänsten med flera innehavare kan utvecklare konfigurera anpassade värdnamn för enskilda appar. De kan också konfigurera unika SNI SSL-certifikatbindningar för enskilda appar.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
App Service Environment finns i två versioner: ASEv1 och ASEv2. Informationen ovan baserades på ASEv2. Det här avsnittet visar skillnaderna mellan ASEv1 och ASEv2.

I ASEv1 hanterar du alla resurser manuellt. Det omfattar klientdelar, arbetare och IP-adresser som används för IP-baserad SSL. Innan du kan skala ut apptjänstplanen måste du skala ut den arbetarpool som du vill vara värd för den.

ASEv1 använder en annan prissättningsmodell än ASEv2. I ASEv1 betalar du för varje allokerad vCPU. Det inkluderar virtuella processorer som används för klientdelar eller arbetare som inte är värdar för några arbetsbelastningar. I ASEv1 är den högsta skalstorleken för en ASE-miljö totalt 55 värdar. Det omfattar arbetare och klientdelar. En fördel med ASEv1 är att det går att distribuera i ett klassiskt virtuellt nätverk och ett virtuellt Resource Manager-nätverk. Mer information om ASEv1 finns i [App Service Environment v1 introduction][ASEv1Intro] (Introduktion till App Service Environment v1).

Information om hur du skapar en ASEv1 med hjälp av en Resource Manager-mall finns i [Skapa en ILB ASE v1 med en Resource Manager-mall][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
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
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
