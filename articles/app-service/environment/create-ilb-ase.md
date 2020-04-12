---
title: Skapa en ILB ASE med ARM
description: Lär dig hur du skapar en App Service-miljö med en intern belastningsutjämnare (ILB ASE) med Hjälp av Azure Resource Manager-mallar. Isolera dina appar helt från internet.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: b7fa447e8564fcbf77702f1d3d474cceb48705c5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114639"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Skapa och använda en apptjänstmiljö för intern belastningsutjämning 

Azure App Service Environment är en distribution av Azure App Service till ett undernät i ett virtuellt Azure-nätverk (VNet). Det går att distribuera en App Service-miljö (ASE) på två sätt: 

- Med en VIP på en extern IP-adress som ofta kallas för en extern ASE.
- Med en VIP på en intern IP-adress, som ofta kallas ILB ASE eftersom den interna slutpunkten är en intern lastbalanserare (ILB). 

Den här artikeln visar hur du kan skapa en intern belastningsutjämnare i apptjänstmiljö. En översikt över ASE finns i [Introduktion till apptjänstmiljöer][Intro]. Om du vill veta hur du skapar en extern ASE läser du [Create an External ASE][MakeExternalASE] (Skapa en extern ASE).

## <a name="overview"></a>Översikt 

Du kan distribuera en ASE med en internet-tillgänglig slutpunkt eller med en IP-adress i ditt VNet. Om du ska konfigurera IP-adressen till en VNet-adress måste ASE vara distribuerad med en ILB. När du distribuerar din ASE med en ILB måste du ange namnet på din ASE. Namnet på din ASE används i domänsuffixet för apparna i DIN ASE.  Domänsuffixet för din ILB &lt;ASE&gt;är ASE-namnet .appserviceenvironment.net. Appar som skapas i en ILB ASE läggs inte i den offentliga DNS:en. 

Tidigare versioner av ILB ASE krävde att du tillhandahåller ett domänsuffix och ett standardcertifikat för HTTPS-anslutningar. Domänsuffixet samlas inte längre in när ILB ASE skapas och ett standardcertifikat samlas inte längre in. När du skapar en ILB ASE nu tillhandahålls standardcertifikatet av Microsoft och är betrodd av webbläsaren. Du kan fortfarande ange anpassade domännamn på appar i ASE och ange certifikat för de anpassade domännamnen. 

Med en ILB ASE kan du göra saker som:

-   Värd för intranätprogram på ett säkert sätt i molnet, som du kommer åt via en plats-till-plats eller ExpressRoute.
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

1. I Azure-portalen väljer du Skapa en > **resurswebbapptjänstmiljö** > **App Service Environment**. **Create a resource**

2. Välj din prenumeration.

3. Välj eller skapa en Resursgrupp.

4. Ange namnet på apptjänstmiljön.

5. Välj virtuell IP-typ av internt.

    ![ASE-generering](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> Namnet på apptjänstmiljön får inte vara fler än 37 tecken.

6. Välj Nätverk

7. Markera eller skapa ett virtuellt nätverk. Om du skapar ett nytt virtuella nätverk här definieras det med ett adressintervall på 192.168.250.0/23. Om du vill skapa ett virtuellt nätverk med ett annat adressintervall eller i en annan resursgrupp än ASE använder du portalen för att skapa Azure Virtual Network. 

8. Markera eller skapa ett tomt undernät. Om du vill välja ett undernät måste det vara tomt och inte delegerat. Det går inte att ändra undernätsstorleken när ASE har skapats. Vi rekommenderar en storlek på `/24`, som har 256 adresser och kan hantera en ASE med maximal storlek och olika skalningsbehov. 

    ![ASE-nätverk][1]

7. Välj **Granska och skapa** och välj sedan **Skapa**.


## <a name="create-an-app-in-an-ilb-ase"></a>Skapa en app i en ILB ASE ##

Du skapar en app i en ILB ASE på samma sätt som du skapar en app i en ASE vanligtvis.

1. I Azure-portalen väljer du Skapa en > **resurswebbapp** > **Web App**. **Create a resource**

1. Ange appens namn.

1. Välj prenumerationen.

1. Välj eller skapa en Resursgrupp.

1. Välj publicerings-, körningsstack och operativsystem.

1. Välj en plats där platsen är en befintlig ILB ASE.  Du kan också skapa en ny ASE när du skapar appar genom att välja en isolerad apptjänstplan. Om du vill skapa en ny ASE väljer du den region som du vill att ASE ska skapas i.

1. Välj eller skapa en App Service plan. 

1. Välj **Granska och skapa** och välj sedan **Skapa** när du är redo.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Webbjobb, Functions och ILB ASE 

Både Functions och webbjobb går att använda på en ILB ASE, men för att portalen ska fungera med dem måste du ha nätverksåtkomst till SCM-webbplatsen.  Det innebär att din webbläsare antingen måste vara på en värd som är i eller anslutet till det virtuella nätverket. Om din ILB ASE har ett domännamn som inte slutar *appserviceenvironment.net*måste du få webbläsaren att lita på HTTPS-certifikatet som används av webbplatsen SCM.

## <a name="dns-configuration"></a>DNS-konfiguration 

När du använder en extern VIP hanteras DNS av Azure. Appar som skapas i din ASE läggs till automatiskt till Azure DNS, som är en offentlig DNS. I en ILB ASE måste du hantera din egen DNS. Det domänsuffix som används med en ILB ASE beror på namnet på ASE. Domänsuffixet är * &lt;&gt;ASE-namnet .appserviceenvironment.net*. IP-adressen för din ILB finns i portalen under **IP-adresser**. 

Så här konfigurerar du DNS:

- skapa en zon för * &lt;&gt;ASE-namn .appserviceenvironment.net*
- skapa en A-post i den zonen som pekar * på ILB:s IP-adress
- skapa en A-post i den zonen som pekar @ till ILB IP-adressen
- skapa en zon i * &lt;&gt;ASE-namnet .appserviceenvironment.net* med namnet scm
- skapa en A-post i scm-zonen som pekar * till ILB IP-adressen

## <a name="publish-with-an-ilb-ase"></a>Publicera med en ILB ASE

För varje app som skapas finns det två slutpunkter. I en ILB ASE har * &lt;du&gt;appnamn .&lt; &gt; ILB ASE-domän* och * &lt;appnamn&gt;.scm.&lt; ILB ASE-domän&gt;*. 

SCM-webbplatsens namn tar dig till Kudu-konsolen som heter **Avancerad portal** inom Azure-portalen. Med Kudu-konsolen kan du visa miljövariabler, utforska disken, använda en konsol och mycket mer. Mer information finns i [Kudu console for Azure App Service][Kudu] (Kudu-konsol för Azure App Service). 

Internetbaserade CI-system, t.ex GitHub och Azure DevOps, fungerar fortfarande med en ILB ASE om Build Agent är tillgänglig via Internet och på samma nätverk som ILB ASE. För Azure DevOps gäller att om Build Agent har skapats på samma virtuella nätverk som ILB ASE (olika undernät går bra) kan den hämta koden från Azure DevOps-git och distribuera till ILB ASE. Om du inte vill skapa en egen Build Agent måste du använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Den här domänen visas i appens publiceringsprofil och i appens portalblad (**Overview** > **Essentials** och även **Properties**). Om du har en ILB ASE med domänsuffixet * &lt;ASE-namnet&gt;.appserviceenvironment.net*och en app med namnet *mytest*använder du *mytest.&lt; ASE-namn&gt;.appserviceenvironment.net* för FTP och *mytest.scm.contoso.net* för webbdistribution.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurera en ILB ASE med en WAF-enhet ##

Du kan kombinera en WAF-enhet (Web Application Firewall) med din ILB ASE för att bara exponera de appar som du vill till internet och hålla resten endast tillgängligt från i det virtuella nätverket. På så sätt kan du bland annat skapa säkra flernivåprogram.

Mer information om hur du konfigurerar din ILB ASE med en WAF-enhet finns i [Konfigurera en brandvägg för webbprogram med apptjänstmiljön][ASEWAF]. Den här artikeln visar hur du använder en virtuell Barracuda-installation med din apptjänstmiljö. Ett annat alternativ är att använda Azure Application Gateway. Application Gateway använder OWASP-kärnregler för att göra programmen säkra. Mer information om Application Gateway finns i [Introduction to the Azure web application firewall][AppGW] (Introduktion till Azures brandvägg för webbaserade program).

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASEs gjorda före maj 2019

ILB ASEs som gjordes före maj 2019 krävde att du ställer in domänsuffixet när ASE skapades. De krävde också att du skulle ladda upp ett standardcertifikat som baserades på det domänsuffixet. Dessutom, med en äldre ILB ASE kan du inte utföra enda inloggning till Kudu-konsolen med appar i den ILB ASE. När du konfigurerar DNS för en äldre ILB ASE måste du ange jokertecken A-posten i en zon som matchar till domänsuffixet. 

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
