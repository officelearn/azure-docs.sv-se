---
title: "Skapa en extern Azure App Service-miljö"
description: "Beskriver hur du skapar en Apptjänst-miljö när du skapar en app eller en fristående"
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
ms.openlocfilehash: 70c43b25aea364d7254137b46af31f851dcf8bc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-external-app-service-environment"></a>Skapa en extern Apptjänst-miljö #

Azure Apptjänst-miljön är en distribution av Azure App Service till ett undernät i Azure-nätverk (VNet). Det finns två sätt att distribuera en Apptjänst-miljö (ASE):

- Med en VIP på en extern IP-adress, som kallas ofta för en extern ASE.
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

Programtjänstplanen är en behållare för appar. När du skapar en app i App Service, Välj eller skapa en apptjänstplan. Behållaren modellen miljöer håller App Service-planer och programtjänstplaner håller appar.

Skapa en ASE när du skapar en apptjänstplan:

1. I den [Azure-portalen](https://portal.azure.com/)väljer **ny** > **webb + mobilt** > **Web App**.

    ![Skapa en webbapp][1]

2. Välj din prenumeration. Appen och ASE skapas i samma prenumerationer.

3. Välj eller skapa en Resursgrupp. Med resursgrupper kan du hantera relaterade Azure-resurser som en enhet. Resursgrupper är också användbara när du har skapat regler för rollbaserad åtkomstkontroll för dina appar. Mer information finns i [översikt över Azure Resource Manager][ARMOverview].

4. Välj App Service-plan och välj sedan **Skapa nytt**.

    ![Ny programtjänstplan][2]

5. I den **plats** listrutan, Välj den region där du vill skapa ASE. Om du väljer en befintlig ASE är inte en ny ASE skapas. Programtjänstplanen har skapats i ASE som du har valt. 

6. Välj **prisnivå**, och välj ett av de **isolerad** priser SKU: er. Om du väljer en **isolerad** SKU-kort och en plats som inte är en ASE, skapas en ny ASE på denna plats. Om du vill starta processen med att skapa en ASE Välj **Välj**. Den **isolerad** SKU är endast tillgänglig i tillsammans med en ASE. Du också kan inte använda andra prisnivå SKU i en ASE än **isolerad**.

    ![Val av prisnivå][3]

7. Ange namn för din ASE. Det här namnet används i dina appar adresserbara namnet. Om namnet på ASE _appsvcenvdemo_, domännamnet är *. appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*, är det adresserbara på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda blanksteg i namnet. Om du använder versaler är domännamnet den totala gemena versionen med det namnet.

    ![Nya programtjänstplanens namn][4]

8. Ange information om din Azure virtuella nätverk. Välj antingen **skapa nya** eller **Välj befintlig**. Välja ett befintligt virtuellt nätverk är bara tillgängligt om du har ett virtuellt nätverk i den valda regionen. Om du väljer **Skapa nytt**, ange ett namn för det virtuella nätverket. Ett nytt Resource Manager-VNet med det namnet skapas. Den använder adressutrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj befintligt**, måste du:

    a. Välj VNet-Adressblock, om du har fler än en.

    b. Ange ett nytt undernätsnamn.

    c. Välj storleken på undernätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att underlätta framtida tillväxt i din ASE.* Vi rekommenderar `/25`, som har 128-adresser och kan hantera en ASE maximala storlek. Vi rekommenderar inte `/28`, till exempel eftersom endast 16 adresser är tillgängliga. Infrastruktur använder minst fem adresser. I en `/28` undernät, som du har lämnat med en maximal skala 11 instanser.

    d. Välj IP-adressintervall för undernätet.

9. Välj **skapa** att skapa ASE. Den här processen skapar även App Service-plan och appen. ASE App Service-plan och appen är alla under samma prenumeration och i samma resursgrupp. Om din ASE måste en separat resursgrupp, eller om du behöver en ILB ASE, Följ stegen för att skapa en ASE ensamt.

## <a name="create-an-ase-by-itself"></a>Skapa en ASE ensamt ##

Om du skapar en ASE fristående har ingenting i den. En tom ASE ådrar fortfarande månadskostnaden för infrastrukturen. Följ dessa steg för att skapa en ASE med en ILB eller skapa en ASE i sin egen resursgruppen. När du har skapat din ASE kan du skapa appar i den med normala processer. Välj din nya ASE som platsen.

1. Sök på Azure Marketplace för **Apptjänstmiljö**, eller välj **ny** > **Web Mobile** > **Apptjänstmiljö**. 

2. Ange namnet på din ASE. Det här namnet används för appar som har skapats i ASE. Om namnet är *mynewdemoase*, underdomännamnet är *. mynewdemoase.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*, är det adresserbara på mytestapp.mynewdemoase.p.azurewebsites.net. Du kan inte använda blanksteg i namnet. Om du använder versaler är domännamnet den totala gemena versionen av namnet. Om du använder en ILB ASE-namnet används inte i din underdomänen men i stället anges uttryckligen när ASE skapades.

    ![Namngivning av ASE][5]

3. Välj din prenumeration. Den här prenumerationen är också ett med alla program i ASE. Du kan inte försätta en ASE i ett virtuellt nätverk som tillhör en annan prenumeration.

4. Välj eller ange en ny resursgrupp. Resursgruppens namn används för din ASE måste vara samma som används för din VNet. Om du väljer ett existerande VNet, uppdateras resursgruppsurvalet för din ASE som ditt VNet. *Du kan skapa en ASE med en resursgrupp som skiljer sig från resursgruppen virtuella nätverk om du använder en Resource Manager-mall.* Om du vill skapa en ASE från en mall finns [skapa en Apptjänst-miljö från en mall][MakeASEfromTemplate].

    ![Val av resursgrupp][6]

5. Välj din VNet och plats. Du kan skapa ett nytt virtuellt nätverk eller välj ett befintligt virtuellt nätverk: 

    * Om du väljer ett nytt virtuellt nätverk kan du ange ett namn och plats. Det nya VNet har adressintervallet 192.168.250.0/23 och ett undernät med namnet standard. Undernätet är definierad som 192.168.250.0/24. Du kan bara välja en Resource Manager-VNet. Den **VIP typen** val avgör om din ASE kan nås direkt från internet (externa) eller om den använder en ILB. Mer information om dessa alternativ finns [skapa och använda en intern belastningsutjämnare med en Apptjänst-miljö][MakeILBASE]. 

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
