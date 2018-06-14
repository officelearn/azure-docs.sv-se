---
title: Så här skapar du en ILB ASE med hjälp av Azure Resource Manager-mallar | Microsoft Docs
description: Lär dig hur du skapar en intern belastningsutjämnare ASE med hjälp av Azure Resource Manager-mallar.
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
ms.openlocfilehash: ea9407208f1bf555cf1a6d166825896dec89ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836856"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Skapa en ILB ASE med hjälp av Azure Resource Manager-mallar

> [!NOTE] 
> Den här artikeln handlar om Apptjänstmiljö v1. Det finns en nyare version av Apptjänst-miljön som är enklare att använda och körs på mer kraftfulla infrastruktur. Mer information om den nya versionen start med den [introduktion till Apptjänst-miljön](intro.md).
>

## <a name="overview"></a>Översikt
Apptjänstmiljöer kan skapas med ett internt virtuellt nätverk-adress i stället för en offentlig VIP.  Den här interna adressen tillhandahålls av en Azure komponent som kallas intern belastningsutjämnare (ILB).  En ILB ASE kan skapas med hjälp av Azure portal.  Det kan även skapas med hjälp av automation via Azure Resource Manager-mallar.  Den här artikeln innehåller stegvisa anvisningar och syntax som behövs för att skapa en ILB ASE med Azure Resource Manager-mallar.

Det finns tre steg ingår i automatisera skapandet av en ILB ASE:

1. Grundläggande ASE skapas först i ett virtuellt nätverk med ett internt belastningsutjämnaradress i stället för en offentlig VIP.  Som en del av det här steget måste tilldelas ett namn för skogsrotsdomänen ILB ASE.
2. När ILB ASE skapas har ett SSL-certifikat överförts.  
3. Överförda SSL-certifikatet tilldelas uttryckligen ILB ASE som dess ”default” SSL-certifikat.  SSL-certifikatet som ska användas för SSL-trafik för appar på ILB ASE när apparna adresseras med vanliga rotdomänen som tilldelats ASE (t.ex. https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Skapa Base ILB ASE
Ett exempel Azure Resource Manager-mall och dess associerade parametrar-fil finns på GitHub [här][quickstartilbasecreate].

De flesta av parametrarna i den *azuredeploy.parameters.json* fil som är gemensamma för att skapa både ILB ASEs, samt ASEs bunden till en offentlig VIP.  I listan nedan anrop out-parametrar för särskilda anteckning eller som är unika, när du skapar en ILB ASE:

* *interalLoadBalancingMode*: I de flesta fall ange detta till 3, vilket innebär att både HTTP/HTTPS-trafik på port 80/443 och kontrolldata channel-portar har lyssnat på FTP-tjänsten på ASE, kommer att bindas till en ILB allokerade virtuella nätverk som interna adress.  Om den här egenskapen anges i stället för 2, relaterade FTP-tjänsten för portar (både kontroll- och kanaler) kommer att bindas till en ILB-adress, medan HTTP/HTTPS-trafiken ska finnas kvar på den offentliga VIP.
* *dnsSuffix*: den här parametern anger standard-rotdomän som ska tilldelas ASE.  I offentliga variationen i Azure App Service rotdomänen standard för alla webbappar är *azurewebsites.net*.  Eftersom en ILB ASE är interna för kundens virtuella nätverk, men inte det vara klokt att använda tjänsten offentliga standard rotdomänen.  I stället ska en ILB ASE ha en standard-rotdomän som passar för användning i ett företags interna virtuella nätverk.  En hypotetiska Contoso Corporation kan till exempel använda en standard rotdomän *intern contoso.com* för appar som är avsedda att endast matchas och tillgänglig Contosos virtuellt nätverk. 
* *ipSslAddressCount*: den här parametern är som standard automatiskt till ett värde av 0 i den *azuredeploy.json* filen eftersom ILB ASEs bara ha en enda ILB-adress.  Inga explicit SSL-IP-adresser för en ILB ASE och därför SSL-IP-adresspool för ett ILB ASE måste anges till noll, annars uppstår ett etablering fel. 

En gång i *azuredeploy.parameters.json* filen har fyllts i för en ILB ASE, ILB ASE kan skapas med hjälp av följande kodavsnitt i Powershell.  Ändra filen sökvägar till matchar där mallfilerna Azure Resource Manager finns på din dator.  Kom ihåg att ange egna värden för Azure Resource Manager distributionsnamnet och resursgruppens namn.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

När Azure Resource Manager mallen har skickats in tar några timmar för ILB ASE ska skapas.  När du är klar att skapa ILB ASE kommer att visas i portalen UX i listan över Apptjänstmiljöer för prenumerationen som utlöste distributionen.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Överför och konfigurera SSL-certifikatet för ”Default”
När ILB ASE skapas ska ett SSL-certifikat associeras med ASE som SSL-certifikatet för ”default” Använd för att upprätta en SSL-anslutningar till appar.  Fortsätter med det hypotetiska Contoso Corporation-exemplet, om ASE standard DNS-suffix är *intern contoso.com*, sedan en anslutning till *https://some-random-app.internal-contoso.com* kräver ett SSL-certifikat som gäller för **.internal contoso.com*. 

Det finns flera olika sätt att skaffa ett giltigt SSL-certifikat inklusive interna CA: er, köpa ett certifikat från en extern utfärdare och använda ett självsignerat certifikat.  Följande certifikat-attribut måste konfigureras korrekt oavsett källan för SSL-certifikat:

* *Ämne*: det här attributet måste anges till **.your-rot-domain-here.com*
* *Alternativt ämnesnamn*: det här attributet måste innehålla både **.your-rot-domain-here.com*, och **.scm.your-rot-domain-here.com*.  Orsaken till den andra posten är att SSL-anslutningar till webbplatsen SCM/Kudu som är associerade med varje app kommer att göras med en adress i formatet *your-app-name.scm.your-root-domain-here.com*.

Med ett giltigt SSL-certifikat i hand krävs två ytterligare förberedande åtgärder.  SSL-certifikatet måste vara konverteras/sparas som en .pfx-fil.  Kom ihåg att .pfx-filen måste innehålla alla mellanliggande och root certifikat och måste också skyddas med ett lösenord.

Den resulterande .pfx-fil måste konverteras till en base64-sträng eftersom SSL-certifikatet kommer att överföras med en Azure Resource Manager-mall.  Eftersom Azure Resource Manager-mallar är textfiler, måste den .pfx-fil som ska konverteras till en base64-sträng, så den kan inkluderas som en parameter för mallen.

Powershell kodfragmentet nedan visar ett exempel på Generera ett självsignerat certifikat, exportera certifikatet som en .pfx-fil, konvertera .pfx-filen till en base64-kodad sträng och sedan spara base64-kodad sträng till en separat fil.  Powershell-koden för base64-kodning bygger på den [Powershell-skript blogg][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

När SSL-certifikatet har har genererats och konverteras till en base64-kodad sträng, exempel Azure Resource Manager-mallen på GitHub för [Konfigurera SSL-standardcertifikat] [ configuringDefaultSSLCertificate] kan användas.

Parametrarna i den *azuredeploy.parameters.json* filen visas nedan:

* *appServiceEnvironmentName*: namnet på den ILB ASE som konfigureras.
* *existingAseLocation*: textsträng som innehåller Azure-regionen där ILB ASE har distribuerats.  Till exempel: ”södra centrala USA”.
* *pfxBlobString*: based64 kodad sträng som innehåller PFX-filen.  Med kodfragmentet som visades tidigare kan du kopiera den sträng som finns i ”exportedcert.pfx.b64” och klistra in den i som värde för den *pfxBlobString* attribut.
* *lösenordet*: lösenordet som används för att skydda den .pfx-fil.
* *certificateThumbprint*: certifikatets tumavtryck.  Om du hämtar värdet från Powershell (t.ex. *$certificate. Tumavtryck för* från kodutdrag tidigare), kan du använda värdet som-är.  Men om du kopierar värdet i dialogrutan Windows-certifikat, Kom ihåg att ta bort extra blanksteg.  Den *certificateThumbprint* ska se ut ungefär: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: ett eget strängidentifierare väljer själv används för att identitet certifikatet.  Namnet används som en del av den unika identifieraren för Azure Resource Manager för den *Microsoft.Web/certificates* entitet som representerar SSL-certifikatet.  Namnet **måste** avslutas med följande suffix: \_yourASENameHere_InternalLoadBalancingASE.  Det här suffixet används av portalen som en indikator att certifikatet används för att skydda en ASE ILB-aktiverade.

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

En gång i *azuredeploy.parameters.json* filen har fyllts i, standard-SSL-certifikat kan konfigureras med följande kodavsnitt i Powershell.  Ändra filen sökvägar till matchar där mallfilerna Azure Resource Manager finns på din dator.  Kom ihåg att ange egna värden för Azure Resource Manager distributionsnamnet och resursgruppens namn.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

När Azure Resource Manager-mall har skickats tar ungefär 40 minuter minuter per ASE frontend att tillämpa ändringen.  Till exempel med en standard storlek ASE som använder två framför ends tar mallen ungefär en timme och 20 minuter för att slutföra.  När mallen körs ASE inte skalas.  

När mallen är klar appar på ILB ASE kan nås över HTTPS och anslutningar ska skyddas med SSL-certifikat som standard.  Standard-SSL-certifikat används när apparna på ILB ASE behandlas med en kombination av programnamnet plus värdnamnet som standard.  Till exempel *https://mycustomapp.internal-contoso.com* använder standard-SSL-certifikat för **.internal contoso.com*.

Dock precis som appar körs på tjänsten offentliga flera innehavare, kan utvecklare också konfigurera anpassade värdnamn för enskilda appar och sedan konfigurera unika SNI SSL-certifikatbindningar för enskilda appar.  

## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänstmiljöer finns [introduktion till Apptjänst-miljö](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

