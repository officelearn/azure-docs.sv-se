---
title: Skapa extern App Service environment - Azure
description: Beskriver hur du skapar en App Service-miljö när du skapar en app eller en fristående
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
ms.custom: seodec18
ms.openlocfilehash: eef13c5a4e3757b0eafd77c0915717175c2dbd8c
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545424"
---
# <a name="create-an-external-app-service-environment"></a>Skapa extern App Service environment

Azure App Service Environment är en distribution av Azure App Service i ett undernät i ett virtuellt nätverk i Azure (VNet).

> [!NOTE]
> Varje App Service Environment har en virtuell IP (VIP), som kan användas för att kontakta App Service Environment.

Det går att distribuera en App Service-miljö (ASE) på två sätt:

- Med en VIP på en extern IP-adress som ofta kallas för en extern ASE.
- Med VIP på en intern IP-adress, ofta kallad en ILB ASE eftersom den interna slutpunkten är en intern belastningsutjämnaren (ILB).

Den här artikeln visar hur du skapar en extern ASE. En översikt över ASE finns i [en introduktion till App Service Environment][Intro]. Information om hur du skapar en ILB ASE finns i [skapa och använda en ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Innan du skapar din ASE

När du har skapat din ASE kan ändra du inte följande:

- Plats
- Prenumeration
- Resursgrupp
- Virtuella nätverket som används
- Undernät som används
- Undernätets storlek

> [!NOTE]
> När du väljer ett virtuellt nätverk och ange ett undernät, se till att det är tillräckligt stor för att hantera framtida tillväxt och skalningsbehov. Vi rekommenderar en storlek på `/24` med 256-adresser.
>

## <a name="three-ways-to-create-an-ase"></a>Tre sätt att skapa en Apptjänstmiljö

Det finns tre sätt att skapa en Apptjänstmiljö:

- **När du skapar en App Service plan**. Den här metoden skapar ASE och App Service-planen i ett enda steg.
- **Som en fristående åtgärd**. Den här metoden skapar en fristående ASE, vilket är en ASE med ingenting i den. Den här metoden är en mer avancerad process för att skapa en Apptjänstmiljö. Du kan använda den för att skapa en ASE med en ILB.
- **Från en Azure Resource Manager-mall**. Den här metoden är för avancerade användare. Mer information finns i [skapa ASE från en mall][MakeASEfromTemplate].

En extern ASE har en offentlig VIP, vilket innebär att alla HTTP/HTTPS-trafik till appar i ASE når en internet-tillgänglig IP-adress. En ASE med en ILB har en IP-adress från det undernät som används av ASE. Appar i en ILB ASE visas inte direkt till internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Skapa en ase-miljö och en App Service plan tillsammans

App Service-planen är en behållare för appar. När du skapar en app i App Service kan du välja eller skapa en App Service-plan. App Service-miljöer Håll App Service-planer, App Service-planer och håll och appar.

Skapa en ASE när du skapar en App Service plan:

1. I den [Azure-portalen](https://portal.azure.com/)väljer **skapa en resurs** > **webb + mobilt** > **Webbapp**.

    ![Skapa en webbapp][1]

2. Välj din prenumeration. Appen och ASE skapas i samma abonnemang.

3. Välj eller skapa en Resursgrupp. Du kan hantera relaterade Azure-resurser med resursgrupper, som en enhet. Resursgrupper är också användbara när du etablerar regler för rollbaserad åtkomstkontroll för dina appar. Mer information finns i den [översikt över Azure Resource Manager][ARMOverview].

4. Välj ditt operativsystem (Windows, Linux eller Docker). 

5. Välj App Service-planen och välj sedan **Skapa ny**. Webbappar i Linux och Windows web apps får inte finnas i samma App Service-planen, men kan finnas i samma App Service Environment. 

    ![Ny App Service-plan][2]

6. I den **plats** listrutan, Välj den region där du vill skapa ASE. Om du väljer en befintlig ASE är inte en ny ASE skapas. App Service-planen har skapats i ASE som du har valt. 

7. Välj **prisnivå**, och välj ett av de **isolerad** priser för SKU: er. Om du väljer en **isolerad** SKU-kort och en plats som inte är en ASE, en ny ASE har skapats på den platsen. För att starta processen för att skapa en Apptjänstmiljö markerar **Välj**. Den **isolerad** SKU är endast tillgängliga i tillsammans med en ase-miljö. Du också kan inte använda andra priser SKU i en ASE än **isolerad**. 

    ![Val av prisnivå][3]

8. Ange namn för din ASE. Det här namnet används i adresserbara namnet för dina appar. Om namnet på ASE är _appsvcenvdemo_, domännamnet är *. appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*, är det adresserbara på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda blanksteg i namnet. Om du använder versaler är domännamnet den totala gemenversionen med det namnet.

    ![Ny App Service-planens namn][4]

9. Ange din Azure information för virtuella nätverk. Välj antingen **skapa en ny** eller **Välj befintlig**. Möjlighet att välja ett befintligt VNet är endast tillgängligt om du har ett virtuellt nätverk i den valda regionen. Om du väljer **Skapa ny**, ange ett namn för det virtuella nätverket. Ett nytt Resource Manager-VNet med samma namn skapas. Den använder adressutrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj befintligt**, måste du:

    a. Välj Adressblock för virtuellt nätverk om du har fler än en.

    b. Ange ett nytt undernätsnamn.

    c. Välj storleken på undernätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att hantera tillväxt i din ASE.* Vi rekommenderar att `/25`, som har 128 adresser och kan hantera en ASE med maximal storlek. Vi rekommenderar inte `/28`, till exempel eftersom endast 16 adresserna är tillgängliga. Använder minst sju adresser för infrastrukturen och Azure-nätverk använder en annan 5. I en `/28` undernät, som du har lämnat med en maximal skalning av 4 App Service-planinstanser för en extern ASE och endast 3 App Service-plan-instanser för en ILB ASE.

    d. Välj IP-adressintervall för undernätet.

10. Välj **skapa** att skapa ASE. Den här processen skapar även App Service-planen och appen. ASE, App Service-plan och app är allt under samma prenumeration och även i samma resursgrupp. Om din ASE behöver en separat resursgrupp, eller om du behöver en ILB ASE, följer du stegen för att skapa en Apptjänstmiljö ensamt.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Skapa en ase-miljö och en Linux-webbapp med en anpassad dockeravbildning tillsammans

1. I den [Azure-portalen](https://portal.azure.com/), **skapa en resurs** > **webb + mobilt** > **Web App for Containers.** 

    ![Skapa en webbapp][7]

1. Välj din prenumeration. Appen och ASE skapas i samma abonnemang.

1. Välj eller skapa en Resursgrupp. Du kan hantera relaterade Azure-resurser med resursgrupper, som en enhet. Resursgrupper är också användbara när du etablerar regler för rollbaserad åtkomstkontroll för dina appar. Mer information finns i den [översikt över Azure Resource Manager][ARMOverview].

1. Välj App Service-planen och välj sedan **Skapa ny**. Webbappar i Linux och Windows web apps får inte finnas i samma App Service-planen, men kan finnas i samma App Service Environment. 

    ![Ny App Service-plan][8]

1. I den **plats** listrutan, Välj den region där du vill skapa ASE. Om du väljer en befintlig ASE är inte en ny ASE skapas. App Service-planen har skapats i ASE som du har valt. 

1. Välj **prisnivå**, och välj ett av de **isolerad** priser för SKU: er. Om du väljer en **isolerad** SKU-kort och en plats som inte är en ASE, en ny ASE har skapats på den platsen. För att starta processen för att skapa en Apptjänstmiljö markerar **Välj**. Den **isolerad** SKU är endast tillgängliga i tillsammans med en ase-miljö. Du också kan inte använda andra priser SKU i en ASE än **isolerad**. 

    ![Val av prisnivå][3]

1. Ange namn för din ASE. Det här namnet används i adresserbara namnet för dina appar. Om namnet på ASE är _appsvcenvdemo_, domännamnet är *. appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*, är det adresserbara på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda blanksteg i namnet. Om du använder versaler är domännamnet den totala gemenversionen med det namnet.

    ![Ny App Service-planens namn][4]

1. Ange din Azure information för virtuella nätverk. Välj antingen **skapa en ny** eller **Välj befintlig**. Möjlighet att välja ett befintligt VNet är endast tillgängligt om du har ett virtuellt nätverk i den valda regionen. Om du väljer **Skapa ny**, ange ett namn för det virtuella nätverket. Ett nytt Resource Manager-VNet med samma namn skapas. Den använder adressutrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj befintligt**, måste du:

    a. Välj Adressblock för virtuellt nätverk om du har fler än en.

    b. Ange ett nytt undernätsnamn.

    c. Välj storleken på undernätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att hantera tillväxt i din ASE.* Vi rekommenderar att `/25`, som har 128 adresser och kan hantera en ASE med maximal storlek. Vi rekommenderar inte `/28`, till exempel eftersom endast 16 adresserna är tillgängliga. Använder minst sju adresser för infrastrukturen och Azure-nätverk använder en annan 5. I en `/28` undernät, som du har lämnat med en maximal skalning av 4 App Service-planinstanser för en extern ASE och endast 3 App Service-plan-instanser för en ILB ASE.

    d. Välj IP-adressintervall för undernätet.

1.  Välj ”Konfigurera behållaren”.
    * Ange ditt namn på anpassad avbildning (du kan använda Azure Container Registry, Docker Hub och ditt eget privata register). Om du inte vill använda en egen anpassad behållare kan du bara ta med din kod och använda en inbyggd avbildning med App Service på Linux, med hjälp av anvisningarna ovan. 

    ![Konfigurera behållaren][9]

1. Välj **skapa** att skapa ASE. Den här processen skapar även App Service-planen och appen. ASE, App Service-plan och app är allt under samma prenumeration och även i samma resursgrupp. Om din ASE behöver en separat resursgrupp, eller om du behöver en ILB ASE, följer du stegen för att skapa en Apptjänstmiljö ensamt.


## <a name="create-an-ase-by-itself"></a>Skapa en Apptjänstmiljö ensamt

Om du skapar en ASE-fristående har ingenting i den. En tom ASE fortfarande medför en månatlig avgift för infrastrukturen. Följ dessa steg för att skapa en ASE med en ILB eller skapa en ase-miljö i en egen resursgrupp. När du har skapat din ASE kan skapa du appar i den med hjälp av den normala processen. Välj din nya ASE som plats.

1. Sök på Azure Marketplace för **App Service Environment**, eller välj **skapa en resurs** > **Web Mobile** > **App Tjänsten miljö**. 

1. Ange namnet på din ASE. Det här namnet används för appar som skapats i ASE. Om namnet är *mynewdemoase*, underdomän heter *. mynewdemoase.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*, är det adresserbara på mytestapp.mynewdemoase.p.azurewebsites.net. Du kan inte använda blanksteg i namnet. Om du använder versaler är domännamnet den totala gemenversionen av namnet. Om du använder en ILB ASE-namn används inte i din underdomän men i stället anges under ASE-generering.

    ![Namngivning av ASE][5]

1. Välj din prenumeration. Den här prenumerationen är också det som alla appar i ASE använder. Du kan inte lägga din ASE i ett virtuellt nätverk som tillhör en annan prenumeration.

1. Välj eller ange en ny resursgrupp. Resursgruppen som används för din ASE måste vara samma konto som används för ditt virtuella nätverk. Om du väljer ett befintligt VNet har på val av resursgrupp för din ASE uppdaterats för att avspegla för ditt VNet. *Du kan skapa en ASE med en resursgrupp som skiljer sig från resursgruppen virtuellt nätverk om du använder Resource Manager-mall.* För att skapa en ASE från en mall, se [skapa en App Service-miljö från en mall][MakeASEfromTemplate].

    ![Val av resursgrupp][6]

1. Välj virtuellt nätverk och plats. Du kan skapa ett nytt VNet eller välja ett befintligt VNet: 

    * Om du väljer ett nytt virtuellt nätverk, kan du ange ett namn och en plats. 
    
    * Det nya VNet har adressintervallet 192.168.250.0/23 och ett undernät med namnet standard. Undernätet har definierats som 192.168.250.0/24. Du kan bara välja ett Resource Manager-VNet. Den **VIP-typ** val styr om din ASE kan nås direkt från internet (extern) eller om den använder en ILB. Mer information om alternativen finns [skapa och använda en intern belastningsutjämnare med en App Service environment][MakeILBASE]. 

      * Om du väljer **externa** för den **VIP-typ**, du kan välja hur många externa IP-adresser som systemet skapas med för IP-baserad SSL. 
    
      * Om du väljer **internt** för den **VIP-typ**, måste du ange den domän som används av din ASE. Du kan distribuera en ASE i ett virtuellt nätverk som använder offentlig eller privat adressintervall. Om du vill använda ett virtuellt nätverk med ett offentligt adressintervall, måste du skapa det virtuella nätverket i tid. 
    
    * Om du väljer ett befintligt virtuellt nätverk skapas ett nytt undernät när ASE har skapats. *Du kan inte använda ett undernät som skapats i förväg i portalen. Du kan skapa en ASE med ett befintligt undernät om du använder Resource Manager-mall.* För att skapa en ASE från en mall, se [skapa en App Service Environment från en mall][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Environment v1

Du kan fortfarande skapa instanser av den första versionen av App Service Environment (ASEv1). Den här processen Sök på Marketplace efter **App Service Environment v1**. Du kan skapa ASE på samma sätt som du skapar fristående ASE. När det är klart, har din ASEv1 två klientdelar och två arbetsroller. Med ASEv1, måste du hantera klientdelar och arbetare. De läggs inte automatiskt när du skapar din App Service-planer. Frontend-datorer fungerar som HTTP/HTTPS-slutpunkter och skicka trafik till anställda. Arbetare är roller som värd för dina appar. Du kan justera antalet klientdelar och arbetare när du har skapat din ASE. 

Mer information om ASEv1 finns [introduktion till App Service Environment v1][ASEv1Intro]. Mer information om skalning, hantera och övervaka ASEv1 finns i [så här konfigurerar du en App Service Environment][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
