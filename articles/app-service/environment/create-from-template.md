---
title: "Skapa en Azure Apptjänst-miljö med hjälp av en Resource Manager-mall"
description: "Beskriver hur du skapar en extern eller ILB Azure Apptjänst-miljö med hjälp av en Resource Manager-mall"
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
ms.openlocfilehash: 015bf031aea6b79fcca0a416253e9aa47bb245b6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Skapa en ASE med hjälp av en Azure Resource Manager-mall

## <a name="overview"></a>Översikt
Azure Apptjänst-miljöer (ASEs) kan skapas med en internet-tillgänglig slutpunkt eller en slutpunkt på en intern adress i Azure-nätverk (VNet). När de skapas med en intern slutpunkt tillhandahålls denna slutpunkt av en Azure komponent som kallas en intern belastningsutjämnare (ILB). ASE på en intern IP-adress kallas en ILB ASE. ASE med en offentlig slutpunkt kallas för en extern ASE. 

En ASE kan skapas med hjälp av Azure-portalen eller en Azure Resource Manager-mall. Den här artikeln innehåller stegvisa anvisningar och syntax som du behöver skapa en extern ASE eller ILB ASE med Resource Manager-mallar. Information om hur du skapar en ASE i Azure portal finns [göra en extern ASE] [ MakeExternalASE] eller [gör en ILB ASE][MakeILBASE].

När du skapar en ASE i Azure-portalen, kan du skapa ditt VNet på samma gång eller välj ett befintligt VNet att distribuera till. När du skapar en ASE från en mall måste du starta med: 

* Hanteraren för virtuella nätverk.
* Ett undernät i det virtuella nätverket. Vi rekommenderar en ASE undernät storlek på `/25` med 128 adresser för att underlätta framtida tillväxt. När ASE har skapats kan ändra du inte storlek.
* Resurs-ID från ditt VNet. Du kan hämta den här informationen från Azure portal under Egenskaper för virtuellt nätverk.
* Den prenumeration som du vill distribuera till.
* Den plats som du vill distribuera till.

Att automatisera genereringen av din ASE:

1. Skapa ASE från en mall. Om du skapar en extern ASE är du klar efter det här steget. Om du skapar en ILB ASE, finns det flera saker som du gör.

2. När du har skapat din ILB ASE upp ett SSL-certifikat som matchar din ILB ASE-domän.

3. Överförda SSL-certifikatet har tilldelats ILB ASE som dess ”default” SSL-certifikat.  Det här certifikatet används för SSL-trafik för appar på ILB ASE när de använder vanliga rotdomänen som tilldelas ASE (till exempel https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Skapa ASE
En Resource Manager-mall som skapar en ASE och dess associerade parametrar-fil är tillgänglig [i ett exempel] [ quickstartasev2create] på GitHub.

Använd dessa Resource Manager-mall om du vill göra en ILB ASE [exempel][quickstartilbasecreate]. De tillgodose som använder. De flesta av parametrarna i den *azuredeploy.parameters.json* fil som är gemensamma för skapande av ILB ASEs och externa ASEs. I följande lista visar parametrarna för särskild anmärkning eller som är unika, när du skapar en ILB ASE:

* *internalLoadBalancingMode*: I de flesta fall ange detta till 3, vilket innebär att både HTTP/HTTPS-trafik på port 80/443 och kontrolldata channel-portar har lyssnat på FTP-tjänsten på ASE, kommer att bindas till ett ILB-allokerade virtuella nätverk internt adress. Om den här egenskapen anges till 2 är endast FTP-tjänsten-relaterade portarna (både kontroll- och kanaler) bundna till en ILB-adress. Det finns kvar på det offentliga VIP HTTP/HTTPS-trafik.
* *dnsSuffix*: den här parametern anger standard-rotdomän som har tilldelats ASE. I offentliga variationen i Azure App Service rotdomänen standard för alla webbappar är *azurewebsites.net*. Eftersom en ILB ASE är interna för kundens virtuella nätverk, vara inte det klokt att använda tjänsten offentliga standard rotdomänen. I stället ska en ILB ASE ha en standard-rotdomän som passar för användning i ett företags interna virtuella nätverk. Contoso Corporation kan till exempel använda en standard rotdomän *intern contoso.com* för appar som är avsedda att matchas och tillgänglig endast Contosos virtuellt nätverk. 
* *ipSslAddressCount*: den här parametern automatiskt som standard värdet 0 i den *azuredeploy.json* filen eftersom ILB ASEs bara ha en enda ILB-adress. Det finns inga explicit SSL-IP-adresser för en ILB ASE. Därför måste måste SSL-IP-adresspool för ett ILB ASE anges till noll. Annars uppstår ett etablering. 

Efter den *azuredeploy.parameters.json* filen fylls i, skapar ASE med hjälp av PowerShell kodfragmentet. Ändra sökvägar för att matcha Resource Manager-mallfil platser på din dator. Glöm inte att ange egna värden för Resource Manager distributionsnamnet och resursgruppens namn:

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Det tar ungefär en timme för ASE ska skapas. Sedan visas ASE i portalen i listan över ASEs för prenumerationen som utlöste distributionen.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Överför och konfigurerar du SSL-certifikatet för ”default”
Ett SSL-certifikat måste vara kopplad till ASE som ”default” SSL-certifikatet som används för att upprätta en SSL-anslutningar till appar. Om den ASE standard DNS-suffixet *intern contoso.com*, en anslutning till https://some-random-app.internal-contoso.com kräver ett SSL-certifikat som gäller för **.internal contoso.com*. 

Skaffa ett giltigt SSL-certifikat med hjälp av interna certifikatutfärdare, köpa ett certifikat från en extern utfärdare eller med ett självsignerat certifikat. Oavsett källan för SSL-certifikat, måste följande certifikat-attribut vara korrekt konfigurerade:

* **Ämne**: det här attributet måste anges till **.your-rot-domain-here.com*.
* **Alternativt ämnesnamn**: det här attributet måste innehålla både **.your-rot-domain-here.com* och **.scm.your-rot-domain-here.com*. SSL-anslutningar till webbplatsen SCM/Kudu som är associerade med varje app använder en adress i formatet *your-app-name.scm.your-root-domain-here.com*.

Med ett giltigt SSL-certifikat i hand krävs två ytterligare förberedande åtgärder. Konvertera/spara SSL-certifikatet som en .pfx-fil. Kom ihåg att .pfx-filen måste innehålla alla mellanliggande och root certifikat. Skydda den med ett lösenord.

.Pfx-filen måste konverteras till en base64-sträng eftersom SSL-certifikatet har laddats upp av en Resource Manager-mall. Eftersom Resource Manager-mallar är textfiler, måste .pfx-filen konverteras till en base64-sträng. Det här sättet kan den inkluderas som en parameter för mallen.

Använd följande PowerShell-kodfragment till:

* Generera ett självsignerat certifikat.
* Exportera certifikatet som en .pfx-fil.
* Konvertera PFX-filen till en base64-kodad sträng.
* Spara base64-kodad sträng till en separat fil. 

Den här PowerShell-koden för base64-kodning bygger på den [PowerShell-skript blogg][examplebase64encoding]:

        $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

        $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
        $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

        $fileName = "exportedcert.pfx"
        Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

        $fileContentBytes = get-content -encoding byte $fileName
        $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
        $fileContentEncoded | set-content ($fileName + ".b64")

När SSL-certifikatet är har genererats och konverteras till en base64-kodad sträng använder Resource Manager-exempelmall [Konfigurera SSL-standardcertifikatet] [ quickstartconfiguressl] på GitHub. 

Parametrarna i den *azuredeploy.parameters.json* fil finns här:

* *appServiceEnvironmentName*: namnet på den ILB ASE som konfigureras.
* *existingAseLocation*: textsträng som innehåller Azure-regionen där ILB ASE har distribuerats.  Till exempel: ”södra centrala USA”.
* *pfxBlobString*: based64-kodad sträng-representation av PFX-filen. Använd kodfragmentet som visades tidigare och kopiera strängen finns i ”exportedcert.pfx.b64”. Klistra in den i som värde för den *pfxBlobString* attribut.
* *lösenordet*: lösenordet som används för att skydda den .pfx-fil.
* *certificateThumbprint*: certifikatets tumavtryck. Om du hämtar värdet från PowerShell (till exempel *$certificate. Tumavtryck för* från kodutdrag tidigare), kan du använda värdet som är. Du kopierar värdet från dialogrutan Windows-certifikat, Kom ihåg att ta bort extra blanksteg. Den *certificateThumbprint* ska se ut ungefär AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: ett eget strängidentifierare väljer själv används för att identitet certifikatet. Namnet används som en del av den unika identifieraren för Resource Manager för den *Microsoft.Web/certificates* entitet som representerar SSL-certifikatet. Namnet *måste* avslutas med följande suffix: \_yourASENameHere_InternalLoadBalancingASE. Azure-portalen använder det här suffixet som en indikator som certifikatet används för att skydda en ASE ILB-aktiverade.

Ett förkortat exempel på *azuredeploy.parameters.json* visas här:

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

Efter den *azuredeploy.parameters.json* filen är ifylld, konfigurera standard-SSL-certifikat med hjälp av PowerShell kodfragmentet. Ändra sökvägar för att matcha där mallfilerna Resource Manager finns på din dator. Glöm inte att ange egna värden för Resource Manager distributionsnamnet och resursgruppens namn:

     $templatePath="PATH\azuredeploy.json"
     $parameterPath="PATH\azuredeploy.parameters.json"

     New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Det tar ungefär 40 minuter per ASE klientdelen att tillämpa ändringen. Till exempel, för en standardstorlek ASE som använder två frontwebbservrarna, tar mallen ungefär en timme och 20 minuter för att slutföra. När mallen körs, kan ASE skala.  

När mallen har slutförts kan appar på ILB ASE nås via HTTPS. Anslutningarna är skyddade med hjälp av standard-SSL-certifikat. Standard-SSL-certifikat används när apparna på ILB ASE åtgärdas genom att använda en kombination av programnamnet plus standardnamnet för värden. Till exempel https://mycustomapp.internal-contoso.com använder standard-SSL-certifikat för **.internal contoso.com*.

Utvecklare kan dock precis som appar som körs på offentliga multitenant tjänsten, konfigurera anpassade värdnamn för enskilda appar. De kan också konfigurera unika SNI SSL-certifikatbindningar för enskilda appar.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
App Service Environment finns i två versioner: ASEv1 och ASEv2. Informationen ovan baserades på ASEv2. Det här avsnittet visar skillnaderna mellan ASEv1 och ASEv2.

I ASEv1 hanterar alla resurser manuellt. Det omfattar klientdelar, arbetare och IP-adresser som används för IP-baserad SSL. Innan du kan skala ut din programtjänstplan, måste du skalar upp arbetspool som du vill ha den.

ASEv1 använder en annan prissättningsmodell än ASEv2. I ASEv1 betalar du för varje allokerad vCPU. Som innehåller vCPUs som används för frontwebbservrarna eller personer som inte är värd för alla arbetsbelastningar. I ASEv1 är den högsta skalstorleken för en ASE-miljö totalt 55 värdar. Det omfattar arbetare och klientdelar. En fördel med ASEv1 är att det går att distribuera i ett klassiskt virtuellt nätverk och ett virtuellt Resource Manager-nätverk. Mer information om ASEv1 finns i [App Service Environment v1 introduction][ASEv1Intro] (Introduktion till App Service Environment v1).

Om du vill skapa en ASEv1 med hjälp av en Resource Manager-mall finns [skapar en ILB ASE v1 med Resource Manager-mall][ILBASEv1Template].


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
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
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
