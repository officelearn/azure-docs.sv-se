---
title: Skapa en extern Azure App Service-miljö
description: Beskriver hur du skapar en Apptjänst-miljö när du skapar en app eller en fristående
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 34248d75c190aa4636c39f087d399d946b589d58
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-external-app-service-environment"></a>Skapa en extern Apptjänst-miljö #

Azure App Service Environment är en distribution av Azure App Service i ett undernät i ett virtuellt nätverk i Azure (VNet). Det går att distribuera en App Service-miljö på två sätt (ASE):

- Med en VIP på en extern IP-adress som ofta kallas för en extern ASE.
- Med VIP-Adressen på en intern IP-adress, kallas ofta för en ILB ASE eftersom den interna slutpunkten är en intern belastningsutjämnare (ILB).

Den här artikeln visar hur du skapar en extern ASE. En översikt över ASE finns [en introduktion till Apptjänst-miljön][Intro]. Information om hur du skapar en ILB ASE finns [skapa och använda en ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Innan du skapar din ASE ##

När du har skapat din ASE kan du ändra följande:

- Plats
- Prenumeration
- Resursgrupp
- Virtuella nätverk som används
- Undernät som används
- Undernätets storlek

> [!NOTE]
> När du väljer ett virtuellt nätverk och ange ett undernät, se till att den är tillräckligt stor för att underlätta framtida tillväxt. Vi rekommenderar en storlek på `/25` med 128-adresser.
>

## <a name="three-ways-to-create-an-ase"></a>Tre sätt att skapa en ASE ##

Det finns tre sätt att skapa en ASE:

- **När du skapar en apptjänstplan**. Den här metoden skapar ASE och programtjänstplanen i ett steg.
- **Som en fristående åtgärd**. Den här metoden skapar en fristående ASE, vilket är en ASE utan något i den. Den här metoden är en mer avancerad processen för att skapa en ASE. Du kan använda den för att skapa en ASE med en ILB.
- **En Azure Resource Manager-mall**. Den här metoden är avsedd för avancerade användare. Mer information finns i [skapa en ASE från en mall][MakeASEfromTemplate].

En extern ASE har en offentlig VIP, vilket innebär att alla HTTP/HTTPS-trafik för appar i ASE träffar en internet-tillgänglig IP-adress. En ASE med en ILB har en IP-adress från det undernät som används av ASE. Appar som finns i en ASE ILB visas inte direkt till internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Skapa en ASE och en apptjänstplan tillsammans ##

Programtjänstplanen är en behållare för appar. När du skapar en app i App Service, Välj eller skapa en apptjänstplan. Apptjänstmiljöer håller App Service-planer och programtjänstplaner håller appar.

Skapa en ASE när du skapar en apptjänstplan:

1. I den [Azure-portalen](https://portal.azure.com/)väljer **skapar du en resurs** > **webb + mobilt** > **Web App**.

    ![Skapa en webbapp][1]

2. Välj din prenumeration. Appen och ASE skapas i samma prenumerationer.

3. Välj eller skapa en Resursgrupp. Med resursgrupper kan du hantera relaterade Azure-resurser som en enhet. Resursgrupper är också användbara när du har skapat regler för rollbaserad åtkomstkontroll för dina appar. Mer information finns i [översikt över Azure Resource Manager][ARMOverview].

4. Välj ditt operativsystem. 

    * Värd för en Linux-app i en ASE är en ny funktion i förhandsversionen, så vi rekommenderar att du inte lägger till Linux-appar i en ASE som körs för tillfället produktionsarbetsbelastningar. 
    * Att lägga till en Linux-app i en ASE innebär att ASE också i förhandsgranskningsläge. 

5. Välj App Service-plan och välj sedan **Skapa nytt**. Linux-webbprogram och Windows web apps får inte finnas i den samma App Service-Plan, men kan vara i samma Apptjänst-miljön. 

    ![Ny App Service-plan][2]

6. I den **plats** listrutan, Välj den region där du vill skapa ASE. Om du väljer en befintlig ASE är inte en ny ASE skapas. Programtjänstplanen har skapats i ASE som du har valt. 

    > [!NOTE]
    > Linux på ASE är endast aktiverad i 6 områden för tillfället: **västra USA, östra USA, västra Europa, Norra Europa, östra, Sydostasien.** Eftersom Linux på ASE är en förhandsversion av funktionen, Välj inte en ASE som du hade skapat innan den här förhandsgranskningen.
    >

7. Välj **prisnivå**, och välj ett av de **isolerad** priser SKU: er. Om du väljer en **isolerad** SKU-kort och en plats som inte är en ASE, skapas en ny ASE på denna plats. Om du vill starta processen med att skapa en ASE Välj **Välj**. Den **isolerad** SKU är endast tillgänglig i tillsammans med en ASE. Du också kan inte använda andra prisnivå SKU i en ASE än **isolerad**. 

    * För Linux på ASE preview tillämpas en rabatt på 50% på isolerade SKU: N (är ingen rabatt på 2D-avgiften för ASE sig själv).

    ![Val av prisnivå][3]

8. Ange namn för din ASE. Det här namnet används i dina appar adresserbara namnet. Om namnet på ASE _appsvcenvdemo_, domännamnet är *. appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*, är det adresserbara på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda blanksteg i namnet. Om du använder versaler är domännamnet den totala gemena versionen med det namnet.

    ![Nya programtjänstplanens namn][4]

9. Ange information om din Azure virtuella nätverk. Välj antingen **skapa nya** eller **Välj befintlig**. Välja ett befintligt virtuellt nätverk är bara tillgängligt om du har ett virtuellt nätverk i den valda regionen. Om du väljer **Skapa nytt**, ange ett namn för det virtuella nätverket. Ett nytt Resource Manager-VNet med det namnet skapas. Den använder adressutrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj befintligt**, måste du:

    a. Välj VNet-Adressblock, om du har fler än en.

    b. Ange ett nytt undernätsnamn.

    c. Välj storleken på undernätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att underlätta framtida tillväxt i din ASE.* Vi rekommenderar `/25`, som har 128-adresser och kan hantera en ASE maximala storlek. Vi rekommenderar inte `/28`, till exempel eftersom endast 16 adresser är tillgängliga. Infrastruktur använder minst sju adresser och Azure-nätverk använder en annan 5. I en `/28` undernät, som du har lämnat med maximalt skalning av 4 App Service-plan instanser för en extern ASE och bara 3 programtjänstplanen instanser för en ILB ASE.

    d. Välj IP-adressintervall för undernätet.

10. Välj **skapa** att skapa ASE. Den här processen skapar även App Service-plan och appen. ASE App Service-plan och appen är alla under samma prenumeration och i samma resursgrupp. Om din ASE måste en separat resursgrupp, eller om du behöver en ILB ASE, Följ stegen för att skapa en ASE ensamt.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Skapa en ASE och en Linux-webbapp tillsammans med en anpassad Docker-bild

1. I den [Azure-portalen](https://portal.azure.com/), **skapar du en resurs** > **webb + mobilt** > **Web App för behållare.** 

    ![Skapa en webbapp][7]

2. Välj din prenumeration. Appen och ASE skapas i samma prenumerationer.

3. Välj eller skapa en Resursgrupp. Med resursgrupper kan du hantera relaterade Azure-resurser som en enhet. Resursgrupper är också användbara när du har skapat regler för rollbaserad åtkomstkontroll för dina appar. Mer information finns i [översikt över Azure Resource Manager][ARMOverview].

4. Välj App Service-plan och välj sedan **Skapa nytt**. Linux-webbprogram och Windows web apps får inte finnas i den samma App Service-Plan, men kan vara i samma Apptjänst-miljön. 

    ![Ny App Service-plan][8]

5. I den **plats** listrutan, Välj den region där du vill skapa ASE. Om du väljer en befintlig ASE är inte en ny ASE skapas. Programtjänstplanen har skapats i ASE som du har valt. 

    > [!NOTE]
    > Linux på ASE är endast aktiverad i 6 områden för tillfället: **västra USA, östra USA, västra Europa, Norra Europa, östra, Sydostasien.** Eftersom Linux på ASE är en förhandsversion av funktionen, Välj inte en ASE som du hade skapat innan den här förhandsgranskningen.
    >

6. Välj **prisnivå**, och välj ett av de **isolerad** priser SKU: er. Om du väljer en **isolerad** SKU-kort och en plats som inte är en ASE, skapas en ny ASE på denna plats. Om du vill starta processen med att skapa en ASE Välj **Välj**. Den **isolerad** SKU är endast tillgänglig i tillsammans med en ASE. Du också kan inte använda andra prisnivå SKU i en ASE än **isolerad**. 

    * För Linux på ASE preview tillämpas en rabatt på 50% på isolerade SKU: N (är ingen rabatt på 2D-avgiften för ASE sig själv).

    ![Val av prisnivå][3]

7. Ange namn för din ASE. Det här namnet används i dina appar adresserbara namnet. Om namnet på ASE _appsvcenvdemo_, domännamnet är *. appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*, är det adresserbara på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda blanksteg i namnet. Om du använder versaler är domännamnet den totala gemena versionen med det namnet.

    ![Nya programtjänstplanens namn][4]

8. Ange information om din Azure virtuella nätverk. Välj antingen **skapa nya** eller **Välj befintlig**. Välja ett befintligt virtuellt nätverk är bara tillgängligt om du har ett virtuellt nätverk i den valda regionen. Om du väljer **Skapa nytt**, ange ett namn för det virtuella nätverket. Ett nytt Resource Manager-VNet med det namnet skapas. Den använder adressutrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj befintligt**, måste du:

    a. Välj VNet-Adressblock, om du har fler än en.

    b. Ange ett nytt undernätsnamn.

    c. Välj storleken på undernätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att underlätta framtida tillväxt i din ASE.* Vi rekommenderar `/25`, som har 128-adresser och kan hantera en ASE maximala storlek. Vi rekommenderar inte `/28`, till exempel eftersom endast 16 adresser är tillgängliga. Infrastruktur använder minst sju adresser och Azure-nätverk använder en annan 5. I en `/28` undernät, som du har lämnat med maximalt skalning av 4 App Service-plan instanser för en extern ASE och bara 3 programtjänstplanen instanser för en ILB ASE.

    d. Välj IP-adressintervall för undernätet.

9.  Välj ”Konfigurera behållaren”.
    * Ange ditt anpassade bilden (du kan använda Azure Container registret, Docker-hubb och privata registret). Om du inte vill använda din egen anpassade container du bara ta med din kod och använder en inbyggd avbildning med App Service på Linux, med hjälp av anvisningarna ovan. 

    ! [Konfigurera behållaren] [9]

10. Välj **skapa** att skapa ASE. Den här processen skapar även App Service-plan och appen. ASE App Service-plan och appen är alla under samma prenumeration och i samma resursgrupp. Om din ASE måste en separat resursgrupp, eller om du behöver en ILB ASE, Följ stegen för att skapa en ASE ensamt.


## <a name="create-an-ase-by-itself"></a>Skapa en ASE ensamt ##

Om du skapar en ASE fristående har ingenting i den. En tom ASE ådrar fortfarande månadskostnaden för infrastrukturen. Följ dessa steg för att skapa en ASE med en ILB eller skapa en ASE i sin egen resursgruppen. När du har skapat din ASE kan du skapa appar i den med normala processer. Välj din nya ASE som platsen.

1. Sök på Azure Marketplace för **Apptjänstmiljö**, eller välj **skapar du en resurs** > **Web Mobile** > **App Tjänsten miljö**. 

2. Ange namnet på din ASE. Det här namnet används för appar som har skapats i ASE. Om namnet är *mynewdemoase*, underdomännamnet är *. mynewdemoase.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*, är det adresserbara på mytestapp.mynewdemoase.p.azurewebsites.net. Du kan inte använda blanksteg i namnet. Om du använder versaler är domännamnet den totala gemena versionen av namnet. Om du använder en ILB ASE-namnet används inte i din underdomänen men i stället anges uttryckligen när ASE skapades.

    ![Namngivning av ASE][5]

3. Välj din prenumeration. Den här prenumerationen är också ett med alla program i ASE. Du kan inte försätta en ASE i ett virtuellt nätverk som tillhör en annan prenumeration.

4. Välj eller ange en ny resursgrupp. Resursgruppens namn används för din ASE måste vara samma som används för din VNet. Om du väljer ett existerande VNet, uppdateras resursgruppsurvalet för din ASE som ditt VNet. *Du kan skapa en ASE med en resursgrupp som skiljer sig från resursgruppen virtuella nätverk om du använder en Resource Manager-mall.* Om du vill skapa en ASE från en mall finns [skapa en Apptjänst-miljö från en mall][MakeASEfromTemplate].

    ![Val av resursgrupp][6]

5. Välj din VNet och plats. Du kan skapa ett nytt virtuellt nätverk eller välj ett befintligt virtuellt nätverk: 

    * Om du väljer ett nytt virtuellt nätverk, kan du ange ett namn och en plats. För tillfället stöds endast 6 regioner för värdbaserade Linux-appar på ASE: **USA, västra; USA, östra; Europa, västra; Europa, norra; Australien, östra; Asien, sydöstra.** 
    
    * Det nya VNet har adressintervallet 192.168.250.0/23 och ett undernät med namnet standard. Undernätet är definierad som 192.168.250.0/24. Du kan bara välja en Resource Manager-VNet. Den **VIP typen** val avgör om din ASE kan nås direkt från internet (externa) eller om den använder en ILB. Mer information om dessa alternativ finns [skapa och använda en intern belastningsutjämnare med en Apptjänst-miljö][MakeILBASE]. 

      * Om du väljer **externa** för den **VIP typen**, du kan välja hur många externa IP-adresser i systemet skapas med för IP-baserade SSL. 
    
      * Om du väljer **internt** för den **VIP typen**, måste du ange den domän som din ASE används. Du kan distribuera en ASE till ett virtuellt nätverk som använder offentliga eller privata adressintervall. Om du vill använda ett VNet med ett offentligt adressintervall, måste du skapa VNet i förväg. 
    
    * Om du väljer ett befintligt virtuellt nätverk skapas ett nytt undernät när ASE skapas. *Du kan inte använda ett befintligt undernät i portalen. Du kan skapa en ASE med ett befintligt undernät om du använder en Resource Manager-mall.* Om du vill skapa en ASE från en mall finns [skapa en Apptjänst-miljö från en mall][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

Du kan ändå skapa instanser av den första versionen av Apptjänst-miljö (ASEv1). Den här processen Sök Marketplace för **Apptjänstmiljö v1**. Du kan skapa ASE på samma sätt som du skapar fristående ASE. När den är klar har två frontwebbservrarna och två personer i din ASEv1. Du måste hantera frontwebbservrarna och personer med ASEv1. De läggs inte automatiskt när du skapar din App Service-planer. Främre slutar fungera som HTTP/HTTPS-slutpunkter och skicka trafik till anställda. Arbetare är de roller som värd för dina appar. Du kan justera antalet frontwebbservrarna och personer när du har skapat din ASE. 

Läs mer om ASEv1 i [introduktion till Apptjänstmiljö v1][ASEv1Intro]. Mer information om skalning, hantera och övervaka ASEv1, finns i [så här konfigurerar du en Apptjänst-miljö][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
