---
title: Skapa ILB ASE v1
description: Skapa en App Service-miljö med en intern belastningsutjämnare (ILB ASE). Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 1a0ec9465be3b714e90bfca6a15b60423d6065a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295582"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Skapa en ILB ASE med hjälp av Azure Resource Manager-mallar

> [!NOTE] 
> Den här artikeln handlar om App Service Environment v1. Det finns en nyare version av App Service Environment som är enklare att använda och körs på mer kraftfull infrastruktur. Om du vill veta mer om den nya versionen börjar med [introduktionen till App Service Environment](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
App Service-miljöer kan skapas med en intern adress för virtuellt nätverk i stället för en offentlig VIP.  Den här interna adressen tillhandahålls av en Azure-komponent som kallas intern belastningsutjämnare (ILB).  En ILB ASE kan skapas med Hjälp av Azure-portalen.  Det kan också skapas med hjälp av automatisering genom Azure Resource Manager-mallar.  Den här artikeln går igenom de steg och den syntax som behövs för att skapa en ILB ASE med Azure Resource Manager-mallar.

Det finns tre steg inblandade i att automatisera skapandet av en ILB ASE:

1. Först skapas basen ASE i ett virtuellt nätverk med hjälp av en intern belastningsutjämnad adress i stället för en offentlig VIP.  Som en del av det här steget tilldelas ett rotdomännamn till ILB ASE.
2. När ILB ASE har skapats överförs ett SSL-certifikat.  
3. Det uppladdade SSL-certifikatet tilldelas uttryckligen ILB ASE som sitt "standard" SSL-certifikat.  Det här SSL-certifikatet används för SSL-trafik till appar på ILB ASE när apparna adresseras med `https://someapp.mycustomrootcomain.com`den gemensamma rotdomän som tilldelats ASE (t.ex. )

## <a name="creating-the-base-ilb-ase"></a>Skapa bas-ILB ASE
En exempelmall för Azure Resource Manager och dess associerade parameterfil finns på GitHub [här][quickstartilbasecreate].

De flesta parametrarna i *filen azuredeploy.parameters.json* är gemensamma för att skapa både ILB ASEs, samt ASEs bundna till en offentlig VIP.  Listan nedan ropar ut parametrar för särskild anmärkning, eller som är unika, när du skapar en ILB ASE:

* *internalLoadBalancingMode*: I de flesta fall ställer in detta till 3, vilket innebär både HTTP/HTTPS-trafik på portarna 80/443 och de kontroll-/datakanalportar som ftp-tjänsten lyssnar på på ASE, kommer att vara bundna till en intern ILB-allokerad intern adress för virtuellt nätverk.  Om den här egenskapen i stället är inställd på 2, kommer endast FTP-tjänstrelaterade portar (både kontroll- och datakanaler) att vara bundna till en ILB-adress, medan HTTP/HTTPS-trafiken kommer att finnas kvar på den offentliga VIP-åtkomsten.
* *dnsSuffix*: Den här parametern definierar standardrotdomänen som ska tilldelas ASE.  I den offentliga varianten av Azure App Service är standardrotdomänen för alla webbappar *azurewebsites.net*.  Men eftersom en ILB ASE är intern till en kunds virtuella nätverk, är det inte meningsfullt att använda public service standard rotdomän.  I stället bör en ILB ASE ha en standardrotdomän som är meningsfull för användning inom ett företags interna virtuella nätverk.  En hypotetisk Contoso Corporation kan till exempel använda en standardrotdomän för *internal-contoso.com* för appar som endast är avsedda att lösas och vara tillgängliga i Contosos virtuella nätverk. 
* *ipSslAddressCount*: Den här parametern är automatiskt standard till ett värde av 0 i *filen azuredeploy.json* eftersom ILB ASEs bara har en enda ILB-adress.  Det finns inga explicita IP-SSL-adresser för en ILB ASE, och därför måste IP-SSL-adresspoolen för en ILB ASE anges till noll, annars uppstår ett etableringsfel. 

När *filen azuredeploy.parameters.json* har fyllts i för en ILB ASE kan ILB ASE skapas med hjälp av följande Powershell-kodavsnitt.  Ändra filen PATHs så att den matchar var Azure Resource Manager-mallfiler finns på datorn.  Kom också ihåg att ange dina egna värden för Distributionsnamnet för Azure Resource Manager och resursgruppsnamnet.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

När Azure Resource Manager-mallen har skickats tar det några timmar innan ILB ASE skapas.  När skapandet är klart visas ILB ASE i portalens ANVÄNDAR- och användargränssnitt i listan över App Service-miljöer för prenumerationen som utlöste distributionen.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Ladda upp och konfigurera ”standard-SSL-certifikatet”
När ILB ASE har skapats bör ett SSL-certifikat associeras med ASE som "standard" SSL-certifikat för att upprätta SSL-anslutningar till appar.  Fortsätter med det hypotetiska Contoso Corporation-exemplet, om ASE:s standard-DNS-suffix *är internal-contoso.com*, kräver en anslutning till *https://some-random-app.internal-contoso.com* ett SSL-certifikat som är giltigt för **.internal-contoso.com*. 

Det finns en mängd olika sätt att skaffa ett giltigt SSL-certifikat, inklusive interna certifikatutfärdare, köpa ett certifikat från en extern utfärdare och använda ett självsignerat certifikat.  Följande certifikatattribut måste konfigureras korrekt oavsett källan för SSL-certifikat:

* *Ämne*: Det här attributet måste ställas in på **.your-root-domain-here.com*
* *Ämnesalternativnamn:* Det här attributet måste innehålla både **.your-root-domain-here.com*och **.scm.your-root-domain-here.com*.  Anledningen till den andra posten är att SSL-anslutningar till SCM/Kudu-webbplatsen som är associerad med varje app kommer att göras med hjälp av en adress till formuläret *your-app-name.scm.your-root-domain-here.com*.

Med ett giltigt SSL-certifikat i handen krävs ytterligare två förberedande steg.  SSL-certifikatet måste konverteras/sparas som en PFX-fil.  Kom ihåg att PFX-filen måste innehålla alla mellanliggande certifikat och rotcertifikat och måste också skyddas med ett lösenord.

Då måste den resulterande PFX-filen konverteras till en base64-sträng eftersom SSL-certifikatet överförs med hjälp av en Azure Resource Manager-mall.  Eftersom Azure Resource Manager-mallar är textfiler måste PFX-filen konverteras till en base64-sträng så att den kan inkluderas som en parameter i mallen.

Powershell-kodavsnittet nedan visar ett exempel på att generera ett självsignerat certifikat, exportera certifikatet som en PFX-fil, konvertera PFX-filen till en base64-kodad sträng och sedan spara den base64-kodade strängen till en separat fil.  Powershell-koden för base64-kodning har [anpassats från Powershell Scripts Blog][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

När SSL-certifikatet har genererats och konverterats till en base64-kodad sträng kan exempelportan Azure Resource Manager på GitHub för [att konfigurera standard-SSL-certifikatet][configuringDefaultSSLCertificate] användas.

Parametrarna i filen *azuredeploy.parameters.json* visas nedan:

* *appServiceEnvironmentName*: Namnet på den ILB ASE som konfigureras.
* *existingAseLocation*: Textsträng som innehåller Azure-regionen där ILB ASE har distribuerats.  Till exempel: "Södra centrala USA".
* *pfxBlobString*: Den based64 kodade strängrepresentationen av PFX-filen.  Med hjälp av kodavsnittet som visas tidigare kopierar du strängen i "exportedcert.pfx.b64" och klistrar in det som värdet för attributet *pfxBlobString.*
* *lösenord*: Lösenordet som används för att skydda PFX-filen.
* *certifikatDumbprint*: Certifikatets tumavtryck.  Om du hämtar det här värdet från Powershell (t.ex. *$certificate. Tumavtryck* från det tidigare kodavsnittet) kan du använda värdet som det är.  Men om du kopierar värdet från Windows-certifikatdialogrutan, kom ihåg att ta bort de främmande utrymmena.  *CertifikatetThumbprint* ska se ut ungefär som: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: En egen strängidentifierare som du själv väljer används för att identifiera certifikatet.  Namnet används som en del av den unika Azure Resource Manager-identifieraren för entiteten *Microsoft.Web/certificates* som representerar SSL-certifikatet.  Namnet **måste** sluta med följande suffix: \_yourASENameHere_InternalLoadBalancingASE.  Det här suffixet används av portalen som en indikator på att certifikatet används för att skydda en ILB-aktiverad ASE.

Ett förkortat exempel *på azuredeploy.parameters.json* visas nedan:

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

När *filen azuredeploy.parameters.json* har fyllts i kan standard-SSL-certifikatet konfigureras med hjälp av följande Powershell-kodavsnitt.  Ändra filen PATHs så att den matchar var Azure Resource Manager-mallfiler finns på datorn.  Kom också ihåg att ange dina egna värden för Distributionsnamnet för Azure Resource Manager och resursgruppsnamnet.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

När Azure Resource Manager-mallen har skickats tar det ungefär fyrtio minuter per ASE-frontend att tillämpa ändringen.  Till exempel, med en standardstorlek ASE med två front-ends, kommer mallen tar cirka en timme och tjugo minuter att slutföra.  Medan mallen kör ASE kommer inte att kunna skalas.  

När mallen är klar kan appar på ILB ASE nås via HTTPS och anslutningarna skyddas med standard-SSL-certifikatet.  Standard-SSL-certifikatet används när appar på ILB ASE adresseras med en kombination av programnamnet plus standardvärden.  Till *https://mycustomapp.internal-contoso.com* exempel skulle använda standard SSL-certifikat för **.internal-contoso.com*.

Men precis som appar som körs på den offentliga tjänsten med flera innehavare kan utvecklare också konfigurera anpassade värdnamn för enskilda appar och sedan konfigurera unika SNI SSL-certifikatbindningar för enskilda appar.  

## <a name="getting-started"></a>Komma igång
Mer om du vill komma igång med App Service-miljöer finns i [Introduktion till App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

