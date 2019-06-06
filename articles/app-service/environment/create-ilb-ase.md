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
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5b05755502ad5836a21080a122d2e1721825f10c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734693"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Skapa och använda en intern Load Balancer App Service Environment 

Azure App Service Environment är en distribution av Azure App Service till ett undernät i ett Azure-nätverk (VNet). Det går att distribuera en App Service-miljö (ASE) på två sätt: 

- Med en VIP på en extern IP-adress som ofta kallas för en extern ASE.
- Med en VIP på en intern IP-adress, som ofta kallas ILB ASE eftersom den interna slutpunkten är en intern lastbalanserare (ILB). 

Den här artikeln visar hur du kan skapa en intern belastningsutjämnare i apptjänstmiljö. En översikt över ASE finns i [Introduktion till App Service-miljöer][Intro]. Om du vill veta hur du skapar en extern ASE läser du [Create an External ASE][MakeExternalASE] (Skapa en extern ASE).

## <a name="overview"></a>Översikt 

Du kan distribuera en ASE med en internet-tillgänglig slutpunkt eller med en IP-adress i ditt VNet. Om du ska konfigurera IP-adressen till en VNet-adress måste ASE vara distribuerad med en ILB. När du distribuerar din ASE med en ILB måste du ange namnet på din ASE. Namnet på din ASE används i domänsuffixet för appar i din ASE.  Domänsuffixet för din ILB ASE är &lt;ASE-namn&gt;. appservicewebsites.net. Appar som görs i en ILB ASE sätts inte i det offentliga DNS. 

Tidigare versioner av ILB ASE måste du ange ett domänsuffix och ett standardcertifikat för HTTPS-anslutningar. Domänsuffix samlas inte längre på ILB ASE-generering och ett standardcertifikat samlas även inte längre. När du skapar en ILB ASE nu standardcertifikatet tillhandahålls av Microsoft och är betrodd av webbläsaren. Du kan fortfarande ange anpassade domännamn för appar i din ASE och Ställ in certifikat på dessa anpassade domännamn. 

Med en ILB ASE, kan du göra saker som:

-   Vara värd för intranätprogram säkert i molnet, som du kommer åt via en plats-till-plats eller ExpressRoute.
-   Skydda appar med en WAF-enhet
-   Vara värd för appar i molnet som inte listas i offentliga DNS-servrar.
-   Skapa internet-isolerade appar för serverdelar, som dina appar för klientdelar säkert kan integrera med.

### <a name="disabled-functionality"></a>Inaktiverad funktion ###

Det finns några saker som du inte kan göra när du använder en ILB ASE:

-   Använd IP-baserad SSL.
-   Tilldela IP-adresser till specifika appar.
-   Köp och använd ett certifikat med en app via Azure-portalen. Du kan hämta certifikat direkt från en certifikatutfärdare och använda dem med dina appar. Du kan inte hämta dem via Azure-portalen.

## <a name="create-an-ilb-ase"></a>Skapa en intern belastningsutjämnare i apptjänstmiljö ##

Så här skapar du en intern belastningsutjämnare i apptjänstmiljö:

1. I Azure-portalen väljer du **Skapa en resurs** > **Webb** > **App Service Environment**.

2. Välj din prenumeration.

3. Välj eller skapa en Resursgrupp.

4. Ange namnet på din App Service Environment.

5. Välj den virtuella IP-typ av internt.

    ![ASE-generering](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Välj nätverk

7. Välj eller skapa ett virtuellt nätverk. Om du skapar ett nytt VNet, ska det definieras med 192.168.250.0/23 adressintervallet. För att skapa ett virtuellt nätverk med ett annat adressintervall eller i en annan resursgrupp än ASE, använder du Azure Virtual Network skapande-portalen. 

8. Välj eller skapa en tom ett undernät. Om du vill välja ett undernät, måste den vara tom och inte delegerad. Storleken på undernätet kan inte ändras när ASE har skapats. Vi rekommenderar en storlek på `/24`, som har 256 adresser och kan hantera en ASE med maximal storlek och olika skalningsbehov. 

    ![ASE-nätverk][1]

7. Välj **granska och skapa** därefter **skapa**.

## <a name="create-an-app-in-an-ilb-ase"></a>Skapa en app i en ILB ASE ##

Du skapar en app i en ILB ASE på samma sätt som du skapar en app i en ASE vanligtvis.

1. I Azure-portalen väljer du **skapa en resurs** > **Web** > **Webbapp**.

1. Ange appens namn.

1. Välj prenumerationen.

1. Välj eller skapa en Resursgrupp.

1. Välj publicera, Körningsstack och operativsystemet.

1. Välj en plats där platsen är en befintlig ILB ASE.  Du kan också skapa en ny ASE under skapa appar genom att välja en isolerad App Service-plan. Om du vill skapa en ny ASE, väljer du den region som du vill att ASE ska skapas i.

1. Välj eller skapa en App Service plan. 

1. Välj **granska och skapa** därefter **skapa** när du är redo.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Webbjobb, Functions och ILB ASE 

Både Functions och webbjobb går att använda på en ILB ASE, men för att portalen ska fungera med dem måste du ha nätverksåtkomst till SCM-webbplatsen.  Det innebär att din webbläsare antingen måste vara på en värd som är i eller anslutet till det virtuella nätverket. Om din ILB ASE har ett domännamn som inte avslutas med *appserviceenvironment.net*, måste du hämta webbläsaren för att lita på HTTPS-certifikatet som används av scm-webbplatsen.

## <a name="dns-configuration"></a>DNS-konfiguration 

När du använder en extern VIP hanteras DNS av Azure. Appar som skapas i din ASE läggs till automatiskt till Azure DNS, som är en offentlig DNS. I en ILB ASE måste du hantera din egen DNS. Domänsuffix som används med en ILB ASE beror på namnet på ASE. Domänsuffixet är  *&lt;ASE-namn&gt;. appserviceenvironment.net*. IP-adressen för din ILB är i portalen under **IP-adresser**. 

Konfigurera din DNS-server:

- Skapa en zon för  *&lt;ASE-namn&gt;. appserviceenvironment.net*
- Skapa en A-post i zonen som pekar * ILB IP-adress 
- Skapa en zon i  *&lt;ASE-namn&gt;. scm.appserviceenvironment.net* med namnet scm
- Skapa en A-post i scm-zonen som pekar på ILB IP-adress

## <a name="publish-with-an-ilb-ase"></a>Publicera med en ILB ASE

För varje app som skapas finns det två slutpunkter. I en ILB ASE måste du ha *&lt;appnamn&gt;.&lt; ILB ASE-domän&gt;* och  *&lt;appnamn&gt;.scm.&lt; ILB ASE-domän&gt;* . 

SCM-webbplatsens namn tar dig till Kudu-konsolen som heter **Avancerad portal** inom Azure-portalen. Med Kudu-konsolen kan du visa miljövariabler, utforska disken, använda en konsol och mycket mer. Mer information finns i [Kudu console for Azure App Service][Kudu] (Kudu-konsol för Azure App Service). 

Internetbaserade CI-system, t.ex GitHub och Azure DevOps, fungerar fortfarande med en ILB ASE om Build Agent är tillgänglig via Internet och på samma nätverk som ILB ASE. För Azure DevOps gäller att om Build Agent har skapats på samma virtuella nätverk som ILB ASE (olika undernät går bra) kan den hämta koden från Azure DevOps-git och distribuera till ILB ASE. Om du inte vill skapa en egen Build Agent måste du använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Den här domänen visas i appens publiceringsprofil och i appens portalblad (**Översikt** > **Essentials** och även **Egenskaper**). Om du har en ILB ASE med domänsuffixet  *&lt;ASE-namn&gt;. appserviceenvironment.net*, och en app med namnet *mytest*, använda *mytest.&lt; ASE-namn&gt;. appserviceenvironment.net* för FTP och *mytest.scm.contoso.net* för webbdistribution.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurera en ILB ASE med en WAF-enhet ##

Du kan kombinera en web application firewall (WAF)-enhet med din ILB ASE att endast visa de appar som du vill till internet och hålla resten endast nås i det virtuella nätverket. På så sätt kan du bygga säkra flernivåprogram bland annat.

Läs mer om hur du konfigurerar din ILB ASE med en WAF-enhet i [konfigurera en brandvägg för webbaserade program med App Service-miljön][ASEWAF]. Den här artikeln visar hur du använder en virtuell Barracuda-installation med din apptjänstmiljö. Ett annat alternativ är att använda Azure Application Gateway. Application Gateway använder OWASP-kärnregler för att göra programmen säkra. Mer information om Application Gateway finns i [Introduction to the Azure web application firewall][AppGW] (Introduktion till Azures brandvägg för webbaserade program).

## <a name="ilb-ases-made-before-may-2019"></a>ILB ase gjort innan maj 2019

ILB ase-miljöer som har gjorts innan maj 2019 måste du ange domänsuffixet under ASE-generering. Användaren måste också att du kan ladda upp ett standardcertifikat som baserades på det domänsuffixet. Dessutom med en äldre ILB ASE utföra du inte enkel inloggning till Kudu-konsolen med appar i den ILB ASE. När du konfigurerar DNS för en äldre ILB ASE kan behöva du ange jokertecken A-post i en zon som matchar till din domänsuffix. 

## <a name="get-started"></a>Kom igång ##

* Information om hur du kommer igång med ASE finns i [Introduktion till App Service-miljöer][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
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
