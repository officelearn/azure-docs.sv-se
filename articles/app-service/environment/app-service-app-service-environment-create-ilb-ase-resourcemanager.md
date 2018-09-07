---
title: Så här skapar du en ILB ASE med hjälp av Azure Resource Manager-mallar | Microsoft Docs
description: Lär dig mer om att skapa en intern belastningsutjämnare i Apptjänstmiljö med hjälp av Azure Resource Manager-mallar.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.openlocfilehash: 20531cb301cad23fbadb617bdf33e710a4481be4
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050042"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Skapa en ILB ASE med hjälp av Azure Resource Manager-mallar

> [!NOTE] 
> Den här artikeln handlar om App Service Environment v1. Det finns en nyare version av App Service Environment som är enklare att använda och körs på kraftfullare infrastruktur. Mer information om den nya versionen början med den [introduktion till App Service Environment](intro.md).
>

## <a name="overview"></a>Översikt
App Service-miljöer kan skapas med en intern adress för virtuellt nätverk i stället för en offentlig VIP.  Den här interna adressen tillhandahålls av en Azure komponent som kallas den interna belastningsutjämnaren (ILB).  En ILB ASE kan skapas med hjälp av Azure portal.  Det kan även skapas med hjälp av automation via Azure Resource Manager-mallar.  Den här artikeln beskriver stegen och syntax som krävs för att skapa en ILB ASE med Azure Resource Manager-mallar.

Det finns tre steg som ingår i automatisera skapandet av en ILB ASE:

1. Först skapas grundläggande ASE i ett virtuellt nätverk med en intern adress för belastningsutjämnaren i stället för en offentlig VIP.  Som en del av det här steget kan tilldelas ett rotdomännamn ILB ASE.
2. När ILB ASE har skapats ett SSL-certifikat har laddats upp.  
3. Överfört SSL-certifikat är uttryckligen tilldelad till ILB ASE som dess ”standard” SSL-certifikat.  SSL-certifikatet används för SSL-trafik till appar på ILB ASE när apparna åtgärdas med hjälp av vanliga rotdomänen tilldelats ase: N (t.ex.) https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Skapa Base ILB ASE
En exempelmall för Azure Resource Manager och dess associerade parametrar-fil finns på GitHub [här][quickstartilbasecreate].

De flesta av parametrarna i den *azuredeploy.parameters.json* fil är gemensamma för att skapa både ILB ase, samt ase som är bunden till en offentlig VIP.  Listan nedan out-parametrar Observera särskilt, eller som är unika, när du skapar en ILB ASE:

* *interalLoadBalancingMode*: I de flesta fall ange detta till 3, vilket innebär att både HTTP/HTTPS-trafik på portarna 80/443 och kontrolldata/channel-portar har lyssnat på via FTP-tjänsten på ASE, kommer att bindas till en ILB allokerade virtuella nätverk som är interna adress.  Om den här egenskapen anges i stället för 2, relaterade FTP-tjänsten för portar (både kontroll- och kanaler) kommer att bindas till ILB-adress, medan HTTP/HTTPS-trafik är kvar på den offentliga VIP.
* *dnsSuffix*: den här parametern definierar standard-rotdomän som ska tilldelas till ASE.  I den offentliga variationen av Azure App Service standard rotdomänen för alla webbappar är *azurewebsites.net*.  Men eftersom en ILB ASE är interna för en kundens virtuella nätverk är det inte meningsfullt att använda tjänsten offentlig standard rotdomänen.  I stället bör en ILB ASE ha en standard-rotdomän som passar för användning i ett företags internt virtuellt nätverk.  Ett hypotetiskt Contoso Corporation kan till exempel använda en standard-rotdomänen för *intern contoso.com* för appar som är avsedda att vara bara matchas och tillgänglig i Contosos virtuellt nätverk. 
* *ipSslAddressCount*: den här parametern är som standard automatiskt till ett värde av 0 i den *azuredeploy.json* filen eftersom ILB ase har endast en ILB-adress.  Det finns inga explicita IP SSL-adresser för en ILB ASE, och därför IP SSL-adresspool för en ILB ASE måste anges till noll, annars uppstår ett fel som etablering. 

När den *azuredeploy.parameters.json* fil har fyllts i för en ILB ASE, ILB ASE kan skapas med hjälp av följande Powershell-kodavsnitt.  Ändra filen sökvägar till matchar där mallfilerna för Azure Resource Manager-finns på din dator.  Kom ihåg att ange dina egna värden för Azure Resource Manager distributionsnamn och resursgruppens namn.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

När du har Azure Resource Manager mallen skickas det tar några timmar innan ILB ASE som ska skapas.  När den har skapats, visas ILB ASE i UX-portalen i listan över App Service-miljöer för den prenumeration som utlöste distributionen.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Ladda upp och konfigurera SSL-certifikat ”standard”
När ILB ASE har skapats ska ett SSL-certifikat associeras med ASE som ”standard” SSL-certifikat använder för att upprätta en SSL-anslutningar till appar.  Fortsättning på hypotetiska Contoso Corporation exemplet, om ASE standard DNS-suffix är *intern contoso.com*, sedan en anslutning till *https://some-random-app.internal-contoso.com* kräver ett SSL-certifikat som är giltigt för **.internal contoso.com*. 

Det finns en mängd olika sätt att skaffa ett giltigt SSL-certifikat, inklusive interna CA: er, köpa ett certifikat från en extern utfärdare och använder ett självsignerat certifikat.  Följande certifikatattribut måste konfigureras korrekt oavsett källan för SSL-certifikat:

* *Ämne*: det här attributet måste anges till **.your-root-domain-here.com*
* *Alternativt namn för certifikatmottagare*: det här attributet måste innehålla både **.your-root-domain-here.com*, och **.SCM.Your-root-domain-here.com*.  Orsaken till den andra posten är att SSL-anslutningar till SCM/Kudo-sajten för varje app görs med en adress i formatet *your-app-name.scm.your-root-domain-here.com*.

Med ett giltigt SSL-certifikat i hand behövs två ytterligare förberedande steg.  SSL-certifikatet måste vara konverteras/sparas som en .pfx-fil.  Kom ihåg att .pfx-filen måste innehålla alla mellanliggande och rotcertifikat och måste även vara skyddad med ett lösenord.

Sedan måste den resulterande .pfx-fil som ska konverteras till en base64-sträng eftersom SSL-certifikatet ska överföras med en Azure Resource Manager-mall.  Eftersom Azure Resource Manager-mallar är textfiler, måste den .pfx-fil som ska konverteras till en base64-sträng så att den kan ingå som en parameter för mallen.

Powershell kodfragmentet nedan visar ett exempel på Generera ett självsignerat certifikat, exportera certifikatet som en .pfx-fil, konvertera PFX-filen till en base64-kodad sträng och sedan spara base64-kodad sträng till en separat fil.  Powershell-kod för base64-kodning bygger på den [Powershell-skript-blogg][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

När SSL-certifikatet har har genererats och konverteras till en base64-kodad sträng, Azure Resource Manager-mallen för exempel på GitHub för [Konfigurera SSL-standardcertifikatet] [ configuringDefaultSSLCertificate] Du kan använda.

Parametrarna i den *azuredeploy.parameters.json* filen finns nedan:

* *appServiceEnvironmentName*: namnet på den ILB ASE som håller på att konfigureras.
* *existingAseLocation*: textsträng som innehåller Azure-regionen där ILB ASE har distribuerats.  Till exempel: ”USA, södra centrala”.
* *pfxBlobString*: den Base 64-kodad sträng som innehåller PFX-filen.  Med kodfragmentet som visades tidigare kan du kopiera den sträng som finns i ”exportedcert.pfx.b64” och klistra in den som värde för den *pfxBlobString* attribut.
* *lösenord*: lösenordet som används för att skydda den .pfx-fil.
* *certificateThumbprint*: certifikatets tumavtryck.  Om du hämtar värdet från Powershell (t.ex. *$certificate. Tumavtrycket* från tidigare kodfragmentet), kan du använda värdet som – är.  Men om du kopierar värdet från dialogrutan för Windows-certifikat, Kom ihåg att ta bort extra blanksteg.  Den *certificateThumbprint* bör likna: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: ett eget strängidentifierare en egen används för att identitet certifikatet.  Namnet används som en del av den unika identifieraren för Azure Resource Manager för den *Microsoft.Web/certificates* entitet som representerar SSL-certifikatet.  Namnet **måste** sluta med följande suffix: \_yourASENameHere_InternalLoadBalancingASE.  Den här suffixet används av portalen som indikator på att certifikatet används för att skydda en Apptjänstmiljö aktiverad ASE.

Ett förkortat exempel på *azuredeploy.parameters.json* visas nedan:

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

När den *azuredeploy.parameters.json* fil har fyllts i, SSL-standardcertifikatet kan konfigureras med följande Powershell-kodavsnitt.  Ändra filen sökvägar till matchar där mallfilerna för Azure Resource Manager-finns på din dator.  Kom ihåg att ange dina egna värden för Azure Resource Manager distributionsnamn och resursgruppens namn.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

När du har Azure Resource Manager mallen skickas det tar ungefär 40 minuter per ASE klientdelen för att tillämpa ändringen.  Till exempel med en standard storlek ASE med hjälp av två klientdelar, tar mallen ungefär en timme och 20 minuter för att slutföra.  När mallen körs inte ASE skalas.  

När mallen är klar appar på ILB ASE kan nås över HTTPS och anslutningar som ska skyddas med SSL-standardcertifikatet.  SSL-standardcertifikatet ska användas när appar på ILB ASE åtgärdas med en kombination av programnamnet plus standardvärdnamnet.  Till exempel *https://mycustomapp.internal-contoso.com* använder SSL-standardcertifikatet för **.internal contoso.com*.

Men precis som appar som körs på den offentliga multiklienttjänst, kan utvecklare även konfigurera anpassade värdnamn för enskilda appar, och sedan konfigurera unika SNI SSL-certifikatbindningar för enskilda appar.  

## <a name="getting-started"></a>Komma igång
Kom igång med App Service-miljöer, se [introduktion till App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

