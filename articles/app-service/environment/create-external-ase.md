---
title: Skapa en extern ASE
description: Lär dig hur du skapar en App Service-miljö med en app i den eller skapar en fristående (tom) ASE.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6c4838e3226b91cbb5d6f86b83266a986418c120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430519"
---
# <a name="create-an-external-app-service-environment"></a>Skapa en extern apptjänstmiljö

Azure App Service Environment är en distribution av Azure App Service i ett undernät i ett virtuellt nätverk i Azure (VNet).

> [!NOTE]
> Varje App Service Environment har en virtuell IP (VIP), som kan användas för att kontakta App Service Environment.

Det går att distribuera en App Service-miljö (ASE) på två sätt:

- Med en VIP på en extern IP-adress som ofta kallas för en extern ASE.
- Med VIP på en intern IP-adress, ofta kallad ILB ASE eftersom den interna slutpunkten är en intern belastningsutjämnare (ILB).

Den här artikeln visar hur du skapar en extern ASE. En översikt över ASE finns i [En introduktion till apptjänstmiljön][Intro]. Information om hur du skapar en ILB ASE finns i [Skapa och använda en ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Innan du skapar din ASE

När du har skapat ASE kan du inte ändra följande:

- Location
- Prenumeration
- Resursgrupp
- Använt V-nätverk
- Undernät som används
- Storleken på undernätet

> [!NOTE]
> När du väljer ett virtuella nätverk och anger ett undernät kontrollerar du att det är tillräckligt stort för att tillgodose framtida tillväxt- och skalningsbehov. Vi rekommenderar en `/24` storlek på med 256 adresser.
>

## <a name="three-ways-to-create-an-ase"></a>Tre sätt att skapa en ASE

Det finns tre sätt att skapa en ASE:

- **När du skapar en apptjänstplan.** Den här metoden skapar ASE- och App Service-planen i ett steg.
- **Som en fristående åtgärd**. Den här metoden skapar en fristående ASE, som är en ASE med ingenting i den. Den här metoden är en mer avancerad process för att skapa en ASE. Du använder den för att skapa en ASE med en ILB.
- **Från en Azure Resource Manager-mall**. Den här metoden är för avancerade användare. Mer information finns i [Skapa en ASE från en mall][MakeASEfromTemplate].

En extern ASE har en offentlig VIP, vilket innebär att all HTTP/HTTPS-trafik till apparna i ASE träffar en INTERNET-tillgänglig IP-adress. En ASE med en ILB har en IP-adress från undernätet som används av ASE. De appar som finns i en ILB ASE exponeras inte direkt för internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Skapa en ASE- och appserviceplan tillsammans

App serviceplanen är en behållare med appar. När du skapar en app i App Service väljer eller skapar du en apptjänstplan. App Service-miljöer innehåller App Service-abonnemang och App Service-abonnemang innehåller appar.

Så här skapar du en ASE medan du skapar en apptjänstplan:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Skapa en > **resurswebb +** > **Mobilapp**. **Create a resource**

    ![Skapa webbappar][1]

2. Välj din prenumeration. Appen och ASE skapas i samma prenumerationer.

3. Välj eller skapa en Resursgrupp. Med resursgrupper kan du hantera relaterade Azure-resurser som en enhet. Resursgrupper är också användbara när du upprättar rollbaserade åtkomstkontrollregler för dina appar. Mer information finns i [översikten över Azure Resource Manager][ARMOverview].

4. Välj operativsystem (Windows, Linux eller Docker). 

5. Välj apptjänstplanen och välj sedan **Skapa ny**. Linux webbappar och Windows webbappar kan inte finnas i samma App Service Plan, men kan finnas i samma App Service Environment. 

    ![Ny apptjänstplan][2]

6. I listrutan **Plats** väljer du den region där du vill skapa ASE. Om du väljer en befintlig ASE skapas inte ett nytt ASE. App serviceplanen skapas i den ASE som du har valt. 

7. Välj **Prisnivå**och välj en av de **isolerade** prisskondienserna. Om du väljer ett **isolerat** SKU-kort och en plats som inte är en ASE skapas en ny ASE på den platsen. Om du vill starta processen för att skapa en ASE väljer du **Välj**. Den **isolerade** SKU:n är endast tillgänglig tillsammans med en ASE. Du kan inte heller använda någon annan prissättning SKU i en ASE än **isolerad**. 

    ![Val av prisnivå][3]

8. Ange namnet på din ASE. Det här namnet används i det adresserbara namnet för dina appar. Om namnet på ASE är _appsvcenvdemo_är domännamnet *.appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*är den adresserbar på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda tomt utrymme i namnet. Om du använder versaler är domännamnet den totala gemener versionen av det namnet.

    ![Namn på nytt apptjänstabonnemang][4]

9. Ange information om virtuella Azure-nätverk. Välj Antingen **Skapa nytt** eller **Välj befintlig**. Alternativet att välja ett befintligt virtuella nätverk är bara tillgängligt om du har ett virtuella nätverk i den valda regionen. Om du väljer **Skapa ny**anger du ett namn på det virtuella nätverket. Ett nytt Resurshanterarens virtuella nätverk med det namnet skapas. Den använder adressutrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj Befintlig**måste du:

    a. Välj VNet-adressblocket om du har fler än ett.

    b. Ange ett nytt undernätsnamn.

    c. Välj storleken på undernätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att rymma framtida tillväxt av din ASE.* Vi `/24`rekommenderar , som har 128 adresser och kan hantera en maximal storlek ASE. Vi rekommenderar `/28`inte, till exempel, eftersom endast 16 adresser är tillgängliga. Infrastruktur använder minst sju adresser och Azure Networking använder ytterligare 5. I `/28` ett undernät får du en maximal skalning av 4 App Service-planinstanser för en extern ASE och endast 3 App Service-planinstanser för en ILB ASE.

    d. Markera IP-området för undernät.

10. Välj **Skapa** för att skapa ASE. Den här processen skapar också App Service-planen och appen. ASE- och App Service-planen och appen är alla under samma prenumeration och även i samma resursgrupp. Om din ASE behöver en separat resursgrupp eller om du behöver en ILB ASE följer du stegen för att skapa en ASE av sig själv.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Skapa en ASE- och en Linux-webbapp med en anpassad Docker-avbildning tillsammans

1. Skapa **en resurswebbplats** > **+ mobilapp** > för behållare i [Azure-portalen.](https://portal.azure.com/)**Web App for Containers.** 

    ![Skapa webbappar][7]

1. Välj din prenumeration. Appen och ASE skapas i samma prenumerationer.

1. Välj eller skapa en Resursgrupp. Med resursgrupper kan du hantera relaterade Azure-resurser som en enhet. Resursgrupper är också användbara när du upprättar rollbaserade åtkomstkontrollregler för dina appar. Mer information finns i [översikten över Azure Resource Manager][ARMOverview].

1. Välj apptjänstplanen och välj sedan **Skapa ny**. Linux webbappar och Windows webbappar kan inte finnas i samma App Service Plan, men kan finnas i samma App Service Environment. 

    ![Ny apptjänstplan][8]

1. I listrutan **Plats** väljer du den region där du vill skapa ASE. Om du väljer en befintlig ASE skapas inte ett nytt ASE. App serviceplanen skapas i den ASE som du har valt. 

1. Välj **Prisnivå**och välj en av de **isolerade** prisskondienserna. Om du väljer ett **isolerat** SKU-kort och en plats som inte är en ASE skapas en ny ASE på den platsen. Om du vill starta processen för att skapa en ASE väljer du **Välj**. Den **isolerade** SKU:n är endast tillgänglig tillsammans med en ASE. Du kan inte heller använda någon annan prissättning SKU i en ASE än **isolerad**. 

    ![Val av prisnivå][3]

1. Ange namnet på din ASE. Det här namnet används i det adresserbara namnet för dina appar. Om namnet på ASE är _appsvcenvdemo_är domännamnet *.appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*är den adresserbar på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda tomt utrymme i namnet. Om du använder versaler är domännamnet den totala gemener versionen av det namnet.

    ![Namn på nytt apptjänstabonnemang][4]

1. Ange information om virtuella Azure-nätverk. Välj Antingen **Skapa nytt** eller **Välj befintlig**. Alternativet att välja ett befintligt virtuella nätverk är bara tillgängligt om du har ett virtuella nätverk i den valda regionen. Om du väljer **Skapa ny**anger du ett namn på det virtuella nätverket. Ett nytt Resurshanterarens virtuella nätverk med det namnet skapas. Den använder adressutrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj Befintlig**måste du:

    a. Välj VNet-adressblocket om du har fler än ett.

    b. Ange ett nytt undernätsnamn.

    c. Välj storleken på undernätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att rymma framtida tillväxt av din ASE.* Vi `/24`rekommenderar , som har 128 adresser och kan hantera en maximal storlek ASE. Vi rekommenderar `/28`inte, till exempel, eftersom endast 16 adresser är tillgängliga. Infrastruktur använder minst sju adresser och Azure Networking använder ytterligare 5. I `/28` ett undernät får du en maximal skalning av 4 App Service-planinstanser för en extern ASE och endast 3 App Service-planinstanser för en ILB ASE.

    d. Markera IP-området för undernät.

1.  Välj "Konfigurera behållare".
    * Ange ditt anpassade avbildningsnamn (du kan använda Azure Container Registry, Docker Hub och ditt eget privata register). Om du inte vill använda din egen anpassade behållare kan du bara ta med din kod och använda en inbyggd avbildning med App Service på Linux med hjälp av instruktionerna ovan. 

    ![Konfigurera behållare][9]

1. Välj **Skapa** för att skapa ASE. Den här processen skapar också App Service-planen och appen. ASE- och App Service-planen och appen är alla under samma prenumeration och även i samma resursgrupp. Om din ASE behöver en separat resursgrupp eller om du behöver en ILB ASE följer du stegen för att skapa en ASE av sig själv.


## <a name="create-an-ase-by-itself"></a>Skapa en ASE av sig själv

Om du skapar en ASE fristående, har det ingenting i den. En tom ASE ådrar sig fortfarande en månadsavgift för infrastrukturen. Följ dessa steg för att skapa en ASE med en ILB eller för att skapa en ASE i en egen resursgrupp. När du har skapat din ASE kan du skapa appar i den med hjälp av den normala processen. Välj din nya ASE som plats.

1. Sök i Azure Marketplace for **App Service Environment**eller välj Skapa en web**mobile** > **app-tjänstmiljö**för **resursen.** >  

1. Ange namnet på din ASE. Det här namnet används för de appar som skapats i ASE. Om namnet är *mynewdemoase*är underdomännamnet *.mynewdemoase.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp*är den adresserbar på mytestapp.mynewdemoase.p.azurewebsites.net. Du kan inte använda tomt utrymme i namnet. Om du använder versaler är domännamnet den totala gemener versionen av namnet. Om du använder en ILB används inte ditt ASE-namn i underdomänen utan anges i stället uttryckligen när ASE skapas.

    ![ASE namngivning][5]

1. Välj din prenumeration. Den här prenumerationen är också den som alla appar i ASE använder. Du kan inte placera din ASE i ett virtuella nätverk som finns i en annan prenumeration.

1. Markera eller ange en ny resursgrupp. Resursgruppen som används för ASE måste vara samma som används för ditt virtuella nätverk. Om du väljer ett befintligt virtuella nätverk uppdateras resursgruppsvalet för DITT ASE för att återspegla det i ditt virtuella nätverk. *Du kan skapa en ASE med en resursgrupp som skiljer sig från VNet-resursgruppen om du använder en Resource Manager-mall.* Information om hur du skapar en ASE från en mall finns i [Skapa en App Service-miljö från en mall][MakeASEfromTemplate].

    ![Val av resursgrupp][6]

1. Välj ditt virtuella nätverk och din plats. Du kan skapa ett nytt virtuella nätverk eller välja ett befintligt virtuella nätverk: 

    * Om du väljer ett nytt virtuellt nätverk, kan du ange ett namn och en plats. 
    
    * Det nya virtuella nätverket har adressintervallet 192.168.250.0/23 och ett undernät med namnet standard. Undernätet definieras som 192.168.250.0/24. Du kan bara välja ett resurshanterarens virtuella nätverk. **VALET AV VIP-typ** avgör om din ASE kan nås direkt från internet (externt) eller om det använder en ILB. Mer information om dessa alternativ finns i [Skapa och använda en intern belastningsutjämnare med en App Service-miljö][MakeILBASE]. 

      * Om du väljer **Extern** för **VIP-typen**kan du välja hur många externa IP-adresser som systemet skapas med för IP-baserade SSL-ändamål. 
    
      * Om du väljer **Internt** för **VIP-typen**måste du ange vilken domän som din ASE använder. Du kan distribuera en ASE till ett virtuella nätverk som använder offentliga eller privata adressintervall. Om du vill använda ett virtuella nätverk med ett offentligt adressintervall måste du skapa det virtuella nätverket i förväg. 
    
    * Om du väljer ett befintligt virtuella nätverk skapas ett nytt undernät när ASE skapas. *Du kan inte använda ett förskapadt undernät i portalen. Du kan skapa en ASE med ett befintligt undernät om du använder en Resource Manager-mall.* Information om hur du skapar en ASE från en mall finns i [Skapa en apptjänstmiljö från en mall][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Environment v1

Du kan fortfarande skapa instanser av den första versionen av App Service Environment (ASEv1). Starta den processen genom att söka i Marketplace för **apptjänstmiljö v1**. Du skapar ASE på samma sätt som du skapar den fristående ASE. När den är klar har din ASEv1 två främre ändar och två arbetare. Med ASEv1 måste du hantera frontändarna och arbetarna. De läggs inte till automatiskt när du skapar apptjänstplanerna. Klientändarna fungerar som HTTP/HTTPS-slutpunkter och skickar trafik till arbetarna. Arbetarna är de roller som är värd för dina appar. Du kan justera antalet frontändar och arbetare när du har skapat din ASE. 

Mer information om ASEv1 finns i [Introduktion till App Service Environment v1][ASEv1Intro]. Mer information om skalning, hantering och övervakning av ASEv1 finns i [Konfigurera en apptjänstmiljö][ConfigureASEv1].

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
