---
title: "Skapa och använda en intern belastningsutjämnare med en Azure Apptjänst-miljö"
description: "Information om hur du skapar och använder en isolerad internet Azure Apptjänst-miljö"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: cc7bdd7860506c20187dc913b72111824d1737ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Skapa och använda en intern belastningsutjämnare med en Apptjänst-miljö #

 Azure Apptjänst-miljön är en distribution av Azure App Service till ett undernät i Azure-nätverk (VNet). Det finns två sätt att distribuera en Apptjänst-miljö (ASE): 

- Med en VIP på en extern IP-adress, som kallas ofta för en extern ASE.
- Med en VIP på en intern IP-adress kallas ofta för en ILB ASE eftersom den interna slutpunkten är en intern belastningsutjämnare (ILB). 

Den här artikeln visar hur du skapar en ILB ASE. En översikt över ASE finns [introduktion till apptjänstmiljöer][Intro]. Information om hur du skapar en extern ASE finns [skapa en extern ASE][MakeExternalASE].

## <a name="overview"></a>Översikt ##

Du kan distribuera en ASE med en internet-tillgänglig slutpunkt eller en IP-adress på ditt VNet. Om du vill ange IP-adressen till en adress för virtuella nätverk måste ASE distribueras med en ILB. När du distribuerar din ASE med en ILB, måste du ange:

-   En egen domän som du använder när du skapar dina appar.
-   Certifikatet som används för HTTPS.
-   DNS-hantering för din domän.

Du kan i RETUR göra saker som:

-   Värden intranätsprogram på ett säkert sätt i molnet, som du kommer åt via ett plats-till-plats eller Azure ExpressRoute VPN.
-   Värden appar i molnet som inte listas i offentliga DNS-servrar.
-   Skapa isolerad internet backend-appar som din frontend appar på ett säkert sätt kan integreras med.

### <a name="disabled-functionality"></a>Inaktiverade funktioner ###

Det finns vissa saker som du kan göra när du använder en ILB ASE:

-   Använd IP-baserade SSL.
-   Tilldela IP-adresser till specifika appar.
-   Köpa och använda ett certifikat med en app på Azure-portalen. Du kan skaffa certifikat från en certifikatutfärdare och använda dem med dina appar. Du kan inte hämta dem via Azure-portalen.

## <a name="create-an-ilb-ase"></a>Skapa en ILB ASE ##

Skapa en ILB ASE:

1. Välj i Azure-portalen **ny** > **webb + mobilt** > **Apptjänstmiljö**.

2. Välj din prenumeration.

3. Välj eller skapa en Resursgrupp.

4. Välj eller skapa ett VNet.

5. Om du väljer ett existerande VNet, måste du skapa ett undernät för att rymma ASE. Se till att ange en storlek för undernät som är tillräckligt stor för att hantera eventuell tillväxt i din ASE. Vi rekommenderar en storlek på `/25`, som har 128-adresser och kan hantera en ASE maximala storlek. Den minsta storleken som du kan välja en `/28`. När infrastrukturen måste kan den här storleken skalas till högst 11 instanser.

    * Utöver standard högst 100 instanser i App Service-planer.

    * Skala nära 100 men med snabbare frontend skalning.

6. Välj **virtuell nätverksplats** > **konfiguration av virtuellt nätverk**. Ange den **VIP typen** till **interna**.

7. Ange ett domännamn. Den här domänen är den som används för appar som har skapats i den här ASE. Det finns vissa begränsningar. Det går inte att vara:

    * NET   

    * azurewebsites.NET

    * p.azurewebsites.NET

    * &lt;asename&gt;. p.azurewebsites.net

   Det anpassade domännamnet för appar och domännamn som används av din ASE får inte överlappa varandra. För en ILB ASE med domännamn _contoso.com_, du kan inte använda anpassade domännamn för dina appar som:

    * www.contoso.com

    * ABCD.def.contoso.com

    * ABCD.contoso.com

   Om du vet de egna domännamn för dina appar, kan du välja en domän för ILB ASE som inte står i konflikt med de anpassade domännamn. I det här exemplet kan du använda något som liknar *contoso internal.com* på din ASE domän eftersom som inte står i konflikt med anpassade domännamn som slutar i *. contoso.com*.

8. Välj **OK**, och välj sedan **skapa**.

    ![ASE-generering][1]

På den **virtuellt nätverk** bladet finns en **virtuella nätverkskonfigurationen** alternativet. Du kan använda den för att välja en extern VIP eller ett internt VIP. Standardvärdet är **externa**. Om du väljer **externa**, din ASE använder en internet-tillgängliga VIP. Om du väljer **internt**, din ASE har konfigurerats med en ILB på en IP-adress inom ditt VNet.

När du har valt **internt**, möjligheten att lägga till flera IP-adresser till din ASE tas bort. I stället måste du ange domänen för ASE. I en ASE med en extern VIP används namnet på ASE i domänen för appar som har skapats i den ASE.

Om du ställer in **VIP typen** till **internt**, namnet på din ASE används inte i domänen för ASE. Du ange uttryckligen domänen. Om din domän är *contoso.corp.net* och du skapar en app i den ASE med namnet *timereporting*, URL: en för appen är timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Skapa en app i en ILB ASE ##

Du kan skapa en app i en ASE ILB på samma sätt som du skapar en app i en ASE normalt.

1. Välj i Azure-portalen **ny** > **webb + mobilt** > **Web** eller **Mobile** eller **API-App**.

2. Ange namnet på appen.

3. Välj prenumerationen.

4. Välj eller skapa en Resursgrupp.

5. Välj eller skapa en apptjänstplan. Välj din ASE som om du vill skapa en ny programtjänstplan. Välj arbetspool där du vill att din App Service-plan som ska skapas. När du skapar App Service-plan, Välj din ASE som platsen och worker-poolen. När du anger namnet på appen har till domänen under appnamnet ersatts av domänen för din ASE.

6. Välj **Skapa**. Om du vill att appen visas på instrumentpanelen, väljer den **fäst på instrumentpanelen** kryssrutan.

    ![Skapa en App Service-plan][2]

    Under **appnamn**, domännamnet uppdateras domänen för din ASE.

## <a name="post-ilb-ase-creation-validation"></a>Post ILB ASE skapa validering ##

En ILB ASE är något annorlunda än den icke - ILB ASE. Du måste hantera egna DNS som redan anges. Du måste ange ditt eget certifikat för HTTPS-anslutningar.

När du har skapat din ASE visar domännamnet den angivna domänen. Ett nytt objekt visas i den **inställningen** menyn kallas **ILB certifikat**. ASE skapas med ett certifikat som inte anger ILB ASE-domän. Om du använder ASE med certifikatet om webbläsaren det är ogiltigt. Det här certifikatet gör det enklare att testa HTTPS, men du måste överföra ditt certifikat som är knutet till ILB ASE domänen. Det här steget är nödvändigt oavsett om certifikatet är självsignerat eller förvärvas från en certifikatutfärdare.

![ILB ASE domännamn][3]

ILB-ASE behöver ett giltigt SSL-certifikat. Använda interna certifikatutfärdare, köpa ett certifikat från en extern utfärdare eller använda ett självsignerat certifikat. Följande certifikat-attribut måste konfigureras korrekt oavsett källan för SSL-certifikat:

* **Ämne**: det här attributet måste anges till *.your-rot-domain-här.
* **Alternativt ämnesnamn**: det här attributet måste innehålla både **.your-rot-domain-här* och **.scm.your-rot-domain-här*. SSL-anslutningar till webbplatsen SCM/Kudu som är associerade med varje app använder en adress i formatet *your-app-name.scm.your-root-domain-here*.

Konvertera/spara SSL-certifikatet som en .pfx-fil. .Pfx-filen måste innehålla alla mellanliggande och root certifikat. Skydda den med ett lösenord.

Om du vill skapa ett självsignerat certifikat kan du använda PowerShell-kommandon här. Se till att använda ett ILB ASE domännamn i stället för *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

Det certifikat som genereras av dessa PowerShell-kommandon flaggas av webbläsare eftersom certifikatet inte har skapats av en certifikatutfärdare i din webbläsare förtroendekedja för. För att få ett certifikat som har förtroende för din webbläsare kan du skaffa den från en kommersiell certifikatutfärdare i din webbläsare förtroendekedja för. 

![Ställ in ILB certificate][4]

Ladda upp dina egna certifikat och testa åtkomst:

1. När du har skapat ASE går du till ASE-Användargränssnittet. Välj **ASE** > **inställningar** > **ILB certifikat**.

2. Välj certifikatets PFX-fil för att ange ILB-certifikat, och ange lösenordet. Det här steget tar tid att bearbeta. Det visas ett meddelande om att en överföringen pågår.

3. Hämta ILB-adressen för din ASE. Välj **ASE** > **egenskaper** > **virtuella IP-adressen**.

4. Skapa en webbapp i din ASE efter ASE skapas.

5. Skapa en virtuell dator om du inte har något i det virtuella nätverket.

    > [!NOTE] 
    > Försök inte att skapa den här virtuella datorn i samma undernät som ASE eftersom den kommer att misslyckas eller orsaka problem.
    >
    >

6. Ange DNS för din ASE-domän. Du kan använda jokertecken med din domän i din DNS. Redigera värdfilen på den virtuella datorn till webbprogramnamnet VIP IP-adress om du vill göra några enkla tester:

    a. Om din ASE har domännamnet _. ilbase.com_ och du skapar webbprogram med namnet _mytestapp_, riktar på _mytestapp.ilbase.com_. Sedan ställer du in _mytestapp.ilbase.com_ att matcha ILB-adress. (På Windows, är värdfilen på _C:\Windows\System32\drivers\etc\_.)

    b. Skapa en post för att testa distributionen webbpublicering eller åtkomst till konsolen avancerade _mytestapp.scm.ilbase.com_.

7. Använda en webbläsare på den virtuella datorn och gå till http://mytestapp.ilbase.com. (Eller gå till det din webbprogrammets namn med domänen.)

8. Använda en webbläsare på den virtuella datorn och gå till https://mytestapp.ilbase.com. Om du använder ett självsignerat certifikat kan acceptera bristen på säkerhet.

    IP-adressen för din ILB anges under **IP-adresser**. Den här listan har även IP-adresser som används av externa VIP och för av inkommande hanteringstrafik.

    ![ILB IP-adress][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Web jobb, funktioner och ILB ASE ##

Web jobb och funktioner stöds i en ASE ILB men för portalen att arbeta med dem. Du måste ha nätverksåtkomst till SCM-platsen.  Detta innebär att din webbläsare måste antingen vara på en värd som är antingen i eller ansluten till det virtuella nätverket.  

När du använder Azure Functions på en ILB ASE kan få du ett felmeddelande som säger ”det inte går att hämta dina funktioner just nu. Försök igen senare ”. Det här felet beror på att Gränssnittet funktioner utnyttjar SCM-plats över HTTPS inte och rotcertifikatet webbläsaren förtroendekedja för. Web jobb har liknande problem. Om du vill undvika det här problemet kan du göra något av följande:

- Lägga till certifikatet i certifikatarkivet Betrodda. Då hämtas kant och Internet Explorer.
- Använda Chrome och gå till webbplatsen SCM först accepterar ej betrodda certifikat och gå sedan till portalen.
- Använda ett kommersiella certifikat som finns i din webbläsare förtroendekedja för.  Detta är det bästa alternativet.  

## <a name="dns-configuration"></a>DNS-konfiguration ##

När du använder en extern VIP hanteras DNS av Azure. Alla appar som skapats i en ASE läggs automatiskt till Azure DNS, vilket är en offentlig DNS. I en ASE ILB, måste du hantera din egen DNS. För en viss domän som _contoso.net_, du behöver skapa DNS A-poster i din DNS pekar på ILB-adress för:

- *. contoso.net
- *. scm.contoso.net

Om din ILB ASE domän används för flera saker utanför den här ASE, kanske du behöver hantera DNS på grundval av per programnamn. Den här metoden en utmaning eftersom du måste lägga till namnen på alla nya app i din DNS när du skapar den. Därför rekommenderar vi att du använder en särskild domän.

## <a name="publish-with-an-ilb-ase"></a>Publicera med en ILB ASE ##

Det finns två slutpunkter för varje app som har skapats. Du har på en ILB ASE  *&lt;appnamn >.&lt; ILB ASE domän >* och  *&lt;appnamn > .scm.&lt; ILB ASE domän >*. 

Platsnamnet SCM går du till konsolen Kudu kallas den **avancerade portal**, i Azure-portalen. Kudu-konsolen kan du visa miljövariabler, Utforska den, använda en konsol och mycket mer. Mer information finns i [Kudu-konsol för Azure App Service][Kudu]. 

I multitenant Apptjänst och i en extern ASE finns enkel inloggning mellan Azure-portalen och Kudu-konsolen. ILB-ASE men måste du använda för dina publishing autentiseringsuppgifter att logga in på Kudu-konsolen.

Internet-baserade CI system, t.ex GitHub och Visual Studio Team Services fungerar inte med en ILB ASE eftersom publishing slutpunkten inte komma åt internet. Du måste i stället använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publishing slutpunkterna för appar i en ASE ILB använder ILB ASE skapades med domänen. Den här domänen visas i appens publiceringsprofilen och i appens portalbladet (**översikt** > **Essentials** och även **egenskaper**). Om du har en ILB ASE med underdomänen *contoso.net* och en app med namnet *MinTest*, använda *mytest.contoso.net* för FTP och *mytest.scm.contoso.net* för web-distribution.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Koppla en ILB ASE med en Brandvägg-enhet ##

Azure Apptjänst innehåller många säkerhetsåtgärder som skyddar systemet. De hjälper också för att avgöra om en app har över. Bästa skydd för ett webbprogram är att koppla en Värdplattformen, till exempel Azure App Service med en brandvägg för webbaserade program (Brandvägg). Eftersom ILB ASE har ett isolerat nätverk programslutpunkten, är den lämplig för sådan användning.

Mer information om hur du konfigurerar din ILB ASE med en Brandvägg enhet finns [konfigurera brandväggar för webbaserade program med App Service-miljö][ASEWAF]. Den här artikeln visar hur du använder en virtuell installation Barracuda med din ASE. Ett annat alternativ är att använda Azure Application Gateway. Programgateway använder OWASP core regler för att skydda alla program som placeras bakom det. Mer information om Application Gateway finns [introduktion till Azure web application brandväggen][AppGW].

## <a name="get-started"></a>Kom igång ##

* Kom igång med ASEs, se [introduktion till apptjänstmiljöer][Intro].
 
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
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
