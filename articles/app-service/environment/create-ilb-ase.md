---
title: Skapa intern lastbalanserare med App Service-miljön – Azure
description: Information om hur du skapar och använder en Internetisolerad Azure App Service-miljö
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 99c6e7dc589a94880976a9c7abcde12377e4e5d3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101541"
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Skapa och använda en intern lastbalanserare med en App Service-miljö #

 Azure App Service Environment är en distribution av Azure App Service i ett undernät i ett virtuellt nätverk i Azure (VNet). Det går att distribuera en App Service-miljö (ASE) på två sätt: 

- Med en VIP på en extern IP-adress som ofta kallas för en extern ASE.
- Med en VIP på en intern IP-adress, som ofta kallas ILB ASE eftersom den interna slutpunkten är en intern lastbalanserare (ILB). 

Den här artikeln visar hur du kan skapa en intern belastningsutjämnare i apptjänstmiljö. En översikt över ASE finns i [Introduktion till App Service-miljöer][Intro]. Om du vill veta hur du skapar en extern ASE läser du [Create an External ASE][MakeExternalASE] (Skapa en extern ASE).

## <a name="overview"></a>Översikt ##

Du kan distribuera en ASE med en internet-tillgänglig slutpunkt eller med en IP-adress i ditt VNet. Om du ska konfigurera IP-adressen till en VNet-adress måste ASE vara distribuerad med en ILB. När du distribuerar din ASE med en ILB måste du ange:

-   Din egen domän som du använder när du skapar dina appar.
-   Certifikaten som används för HTTPS.
-   DNS-hantering för din domän.

I utbyte kan du göra saker om att:

-   Vara värd för intranätprogram säkert i molnet, som du kommer åt via en VPN för plats till plats eller Azure ExpressRoute VPN.
-   Vara värd för appar i molnet som inte listas i offentliga DNS-servrar.
-   Skapa internet-isolerade appar för serverdelar, som dina appar för klientdelar säkert kan integrera med.

### <a name="disabled-functionality"></a>Inaktiverad funktion ###

Det finns några saker som du inte kan göra när du använder en ILB ASE:

-   Använd IP-baserad SSL.
-   Tilldela IP-adresser till specifika appar.
-   Köp och använd ett certifikat med en app via Azure-portalen. Du kan hämta certifikat direkt från en certifikatutfärdare och använda dem med dina appar. Du kan inte hämta dem via Azure-portalen.

## <a name="create-an-ilb-ase"></a>Skapa en intern belastningsutjämnare i apptjänstmiljö ##

Så här skapar du en intern belastningsutjämnare i apptjänstmiljö:

1. På Azure-portalen väljer du  **Skapa en resurs** > **Webb** > **App Service-miljön**.

2. Välj din prenumeration.

3. Välj eller skapa en Resursgrupp.

4. Välj eller skapa ett virtuellt nätverk (VNet).

5. Om du väljer ett befintligt VNet måste du skapa ett undernät för att rymma ASE. Se till att undernätets storlek är tillräckligt stort för att rymma kommande ASE-tillväxt. Vi rekommenderar en storlek på `/24`, som har 256 adresser och kan hantera en ASE med maximal storlek och olika skalningsbehov. 

6. Välj  **Virtuellt nätverk/plats** > **Konfiguration av virtuellt nätverk**. Ställ in **VIP-typ** på **Intern**.

7. Ange ett domännamn. Den här domänen är den som används för appar som har skapats i denna ASE. Det finns vissa begränsningar. Det kan inte vara:

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;asename&gt;.p.azurewebsites.net

   Du kan [mappa ett befintligt DNS-namn till din app][customdomain]. Det anpassade domännamnet används för appar och domännamnet som används av din ASE får inte överlappa. För en ILB ASE med domännamnet _contoso.com_ kan du inte använda anpassade domännamn för dina appar som:

   * www\.contoso.com

   * abcd.def.contoso.com

   * abcd.contoso.com

   Om du känner till dina appars anpassade domännamn väljer du en domän för den ILB ASE som inte står i konflikt med dessa anpassade domännamn. I det här exemplet kan du använda något som *contoso-internal.com* som ASE-domän eftersom det inte står i konflikt med anpassade domännamn som slutar med *.contoso.com*.

8. Välj **OK** och sedan **Skapa**.

    ![ASE-generering][1]

På bladet **Virtuellt nätverk** finns alternativet **Konfiguration av virtuellt nätverk**. Du kan använda det för att välja en extern eller intern VIP. Standardinställningen är **Extern**. Om du väljer **Extern** använder din ASE en internet-åtkomlig VIP. Om du väljer **Intern** är din ASE konfigurerad med en ILB på en IP-adress inom ditt VNet.

När du har valt **Intern** tas möjligheten att lägga till fler IP-adresser till din ASE bort. Istället måste du ange domänen för ASE. I en ASE med extern VIP används ASE-namnet i domänen för appar som skapats i denna ASE.

Om du ställer in **VIP-typ** på **Intern** används inte ditt ASE-namn i ASE-domänen. Du anger domänen uttryckligen. Om din domän är *contoso.corp.net*  och du skapar en app i den ASE:n med namnet  *timereporting* är appens URL-adress timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Skapa en app i en ILB ASE ##

Du skapar en app i en ILB ASE på samma sätt som du skapar en app i en ASE vanligtvis.

1. På Azure-portalen väljer du  **Skapa en resurs** > **Webb och mobilt** > **Webbapp**.

1. Ange appens namn.

1. Välj prenumerationen.

1. Välj eller skapa en Resursgrupp.

1. Välj ditt operativsystem. 

    * Du kan bara ta din egen container med anvisningarna [här][linuxapp] om du vill skapa en Linux-app med hjälp av en anpassad Docker-container. 

1. Välj eller skapa en App Service plan. Om du vill skapa en ny App Service plan väljer du ASE som plats. Välj den arbetarpool där du vill att din App Service plan ska skapas. När du skapar din App Service plan ska du välja din ASE som plats och arbetarpoolen. När du anger namnet på appen ersätts domänen under ditt appnamn med ASE-domänen.

1. Välj **Skapa**. Om du vill att appen ska visas på instrumentpanelen markerar du kryssrutan  **Fäst på instrumentpanelen** .

    ![Skapa App Service plan][2]

    Under **Appnamn** uppdateras domännamnet för att återspegla din ASE-domän.

## <a name="post-ilb-ase-creation-validation"></a>Validering efter ILB ASE-generering ##

En ILB ASE skiljer sig något från en icke-ILB ASE. Du måste hantera din egen DNS som redan anges. Du måste också tillhandahålla ditt eget certifikat för HTTPS-anslutningar.

När du har skapat din ASE visar domännamnet den domän du angav. Ett nytt objekt visas på menyn **Inställning** med namnet **ILB-certifikat**. ASE skapas med ett certifikat som inte anger ILB ASE-domän. Om du använder en ASE med det certifikatet informerar webbläsaren om att det är ogiltigt. Det här certifikatet gör det lättare att testa HTTPS, men du måste ladda upp ditt eget certifikat som är bundet till din ILB ASE-domän. Det här steget är nödvändigt oavsett om certifikatet är självsignerat eller förvärvas från en certifikatutfärdare.

![ILB ASE-domännamn][3]

Din ILB ASE behöver ett giltigt SSL-certifikat. Använd interna certifikatutfärdare, köp ett certifikat från en extern utfärdare eller använd ett självsignerat certifikat. Följande certifikatattribut måste konfigureras korrekt oavsett källan för SSL-certifikat:

* **Ämne**: Det här attributet måste vara inställt på *.your-root-domain-here.
* **Alternativt namn för certifikatmottagare**: Det här attributet måste innehålla både **.your-root-domain-here* och **.scm.your-root-domain-here*. SSL-anslutningar till SCM-/Kudu-webbplatsen som associeras till varje app använder en adress i formatet *your-app-name.scm.your-root-domain-here*.

Konvertera/spara SSL-certifikatet som en .pfx-fil. .pfx-filen måste innehålla alla mellanliggande rotcertifikat. Skydda den med ett lösenord.

Om du vill skapa ett självsignerat certifikat kan du använda PowerShell-kommandona här. Se till att använda din ILB ASE-domännamn istället för *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

Certifikatet som dessa PowerShell-kommandon genererar flaggas av webbläsare eftersom certifikatet inte skapades av en certifikatutfärdare i webbläsarens certifikatkedja. För att få ett certifikat som webbläsaren har förtroende för kan du skaffa det från en kommersiell certifikatutfärdare i webbläsares certifikatkedja. 

![Ställ in ILB-certifikat][4]

Så här laddar du upp egna certifikat och testar åtkomst:

1. När ASE har skapats går du till ASE UI. Välj **ASE** > **Inställningar** > **ILB-certifikat**.

1. Du anger ILB-certifikat genom att välja filen certificate .pfx och ange lösenordet. Det här steget tar tid att bearbeta. Det visas ett meddelande om att en överföring pågår.

1. Hämta ILB-adressen för din ASE. Välj **ASE** > **Egenskaper** > **Virtuell IP-adress**.

2. Skapa en app i din ASE när ASE har skapats.

3. Skapa en virtuell dator om du inte redan har en i detta VNet.

    > [!NOTE] 
    > Försök inte att skapa den här virtuella datorn i samma undernät som ASE eftersom den kommer att misslyckas eller orsaka problem.
    >

4. Ange DNS för din ASE-domän. Du kan använda jokertecken med din domän i din DNS. Gör några enkla test genom att redigera värdfilerna på din virtuella dator för att ställa in namnet på appen till VIP IP-adressen:

    a. Om din ASE har domännamnet _.ilbase.com_ och du skapar en app med namnet _mytestapp_ så hanteras den på _mytestapp.ilbase.com_. Sedan ställer du in _mytestapp.ilbase.com_ för att lösa ILB-adressen. (I Windows finns värdfilen på _C:\Windows\System32\drivers\etc\_.)

    b. Om du vill test webbdistributionens publicering eller åtkomst till den avancerade konsolen skapar du en post för _mytestapp.scm.ilbase.com_.

5. Använd en webbläsare på den virtuella datorn och gå till https://mytestapp.ilbase.com. (Eller gå till ditt din apps namn med din domän.)

6. Använd en webbläsare på den virtuella datorn och gå till https://mytestapp.ilbase.com. Om du använder ett självsignerat certifikat ska du acceptera bristen på säkerhet.

    IP-adressen för din ILB anges under **IP-adresser**. Den här listan innehåller även IP-adresserna som används av externa virtuella IP-adresser (VIP) och för inkommande hanteringstrafik.

    ![ILB-IP-adress][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Webbjobb, Functions och ILB ASE ##

Både Functions och webbjobb går att använda på en ILB ASE, men för att portalen ska fungera med dem måste du ha nätverksåtkomst till SCM-webbplatsen.  Det innebär att din webbläsare antingen måste vara på en värd som är i eller anslutet till det virtuella nätverket.  

När du använder Azure Functions på en ILB ASE kanske du ser felmeddelandet ”Vi kan inte hämta funktionerna just nu. Försök igen senare.” Felet beror på att gränssnittet i Functions utnyttjar SCM-webbplatsen över HTTPS, och rotcertifikatet finns inte i webbläsarens certifikatkedja. Webbjobb har ett liknande problem. Om du vill undvika det här problemet kan du göra något av följande:

- Lägg till certifikatet i ditt betrodda certifikatarkiv. Detta häver blockeringen för Microsoft Edge och Internet Explorer.
- Använd Chrome och gå till SCM-webbplatsen först, godkänn det icke-betrodda certifikatet och gå sedan till portalen.
- Använd ett kommersiellt certifikat som finns i webbläsarens certifikatkedja.  Detta är det bästa alternativet.  

## <a name="dns-configuration"></a>DNS-konfiguration ##

När du använder en extern VIP hanteras DNS av Azure. Appar som skapas i din ASE läggs till automatiskt till Azure DNS, som är en offentlig DNS. I en ILB ASE måste du hantera din egen DNS. För en viss domän som _contoso.net_ måste du skapa DNS A-poster i din DNS som pekar på din ILB-adress för:

- *.contoso.net
- *.scm.contoso.net

Om din ILB ASE-domän används för flera saker utanför denna ASE kan du behöva hantera DNS per appnamn. Den här metoden är en utmaning eftersom du måste lägga till varje nytt appnamn i din DNS när du skapar den. Av den här anledningen rekommenderar vi att du använder en särskild domän.

## <a name="publish-with-an-ilb-ase"></a>Publicera med en ILB ASE ##

För varje app som skapas finns det två slutpunkter. I en ILB ASE har du *&lt;app name>.&lt;ILB ASE-domän>* och *&lt;app name>.scm.&lt;ILB ASE Domain>*. 

SCM-webbplatsens namn tar dig till Kudu-konsolen som heter **Avancerad portal** inom Azure-portalen. Med Kudu-konsolen kan du visa miljövariabler, utforska disken, använda en konsol och mycket mer. Mer information finns i [Kudu console for Azure App Service][Kudu] (Kudu-konsol för Azure App Service). 

I App Service för flera innehavare och i en extern ASE finns det enkel inloggning mellan Azure-portalen och Kudu-konsolen. För ILB ASE måste du emellertid använda dina pucliceringsautentiseringsuppgifter för att logga in på Kudu-konsolen.

Internetbaserade CI-system, t.ex GitHub och Azure DevOps, fungerar fortfarande med en ILB ASE om Build Agent är tillgänglig via Internet och på samma nätverk som ILB ASE. För Azure DevOps gäller att om Build Agent har skapats på samma virtuella nätverk som ILB ASE (olika undernät går bra) kan den hämta koden från Azure DevOps-git och distribuera till ILB ASE. Om du inte vill skapa en egen Build Agent måste du använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Den här domänen visas i appens publiceringsprofil och i appens portalblad (**Översikt** > **Essentials** och även **Egenskaper**). Om du har en ILB ASE med underdomänen *contoso.net* och en app som heter *mytest* ska du använda *mytest.contoso.net* för FTP och *mytest.scm.contoso.net* för webbdistribution.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Koppla en ILB ASE med en WAF-enhet ##

Azure App Service tillhandahåller många säkerhetsåtgärder som skyddar systemet. De hjälper också till att avgöra om en app har blivit hackad. Det bästa skyddet för en webbapp är att koppla en värdplattform som Azure App Service till en brandvägg för webbaserade program (WAF). Eftersom en intern belastningsutjämnare i apptjänstmiljö har en nätverksisolerad programslutpunkt är den lämplig för sådan användning.

Om du vill veta mer om hur du konfigurerar din interna belastningsutjämnare i apptjänstmiljö med en WAF-enhet går du till [Configure a web application firewall with your App Service environment][ASEWAF] (Konfigurera en brandvägg för webbaserade program med din App Service-miljö). Den här artikeln visar hur du använder en virtuell Barracuda-installation med din apptjänstmiljö. Ett annat alternativ är att använda Azure Application Gateway. Application Gateway använder OWASP-kärnregler för att göra programmen säkra. Mer information om Application Gateway finns i [Introduction to the Azure web application firewall][AppGW] (Introduktion till Azures brandvägg för webbaserade program).

## <a name="get-started"></a>Kom igång ##

* Information om hur du kommer igång med ASE finns i [Introduktion till App Service-miljöer][Intro].
 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
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
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
