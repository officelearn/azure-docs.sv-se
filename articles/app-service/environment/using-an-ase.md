---
title: Använda App Service - miljö i Azure
description: Skapa, publicera och skala appar i Azure App Service environment
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a9a6c7c47a6ea81f682f453a85ee6f8e214a09a7
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678096"
---
# <a name="use-an-app-service-environment"></a>Använda en App Service environment #

## <a name="overview"></a>Översikt ##

Azure App Service Environment är en distribution av Azure App Service i ett undernät i kundens Azure-nätverk. Det består av:

- **Klientdelens ends**: Frontend-datorer är där HTTP/HTTPS avslutas i en App Service environment (ASE).
- **Arbetare**: Arbetare är de resurser som är värdar för dina appar.
- **Databasen**: Databasen innehåller information som definierar miljön.
- **Storage**: Lagringen används som värd för apparna som publicerats av kunden.

> [!NOTE]
> Det finns två versioner av App Service Environment: ASEv1 och ASEv2. Du måste hantera resurserna innan du kan använda dem i ASEv1. Information om hur du konfigurerar och hanterar ASEv1 finns [konfigurera en App Service environment v1][ConfigureASEv1]. Resten av den här artikeln fokuserar på ASEv2.
>
>

Du kan distribuera en ase-miljö (ASEv1 och ASEv2) med en extern eller intern VIP för åtkomst till appen. Distribution med extern VIP kallas vanligtvis en extern ASE. Den interna versionen kallas ILB ASE eftersom den använder en intern belastningsutjämnare (ILB). Läs mer om ILB ASE i [skapa och använda en ILB ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Skapa en app i en ASE ##

Om du vill skapa en app i en ase-miljö använda samma process som när du skapar vanligtvis, men med några små skillnader. När du skapar en ny App Service-plan:

- I stället för att välja en geografisk plats där du kan distribuera din app kan välja du en ASE som din plats.
- Alla App Service-planer som skapats i en ASE måste finnas i en isolerad prisnivå.

Om du inte har en ase-miljö kan du skapa en genom att följa anvisningarna i [skapa en App Service environment][MakeExternalASE].

Skapa en app i en ASE:

1. Välj **skapa en resurs** > **webb + mobilt** > **Webbapp**.

2. Ange ett namn för appen. Om du redan har valt en App Service-plan i en ASE visar domännamnet för appen domännamnet för ASE.

    ![Val av nätverksnamn för App][1]

1. Välj en prenumeration.

1. Ange ett namn för en ny resursgrupp eller välj **Använd befintlig** och välj en från den nedrullningsbara listan.

1. Välj ditt operativsystem. 

    * Som är värd för en Linux-app i en ASE är en ny förhandsgranskningsfunktion, så vi rekommenderar att du inte lägger till Linux-appar i en ASE som kör produktionsarbetsbelastningar. 
    * Lägga till en Linux-app i en ASE innebär att ASE också i förhandsgranskningsläge. 

1. Välj en befintlig App Service-plan i din ASE eller skapa en ny genom att följa dessa steg:

    a. Välj **skapa en ny**.

    b. Ange namn för din App Service-plan.

    c. Välj din ASE i den **plats** listrutan. Som är värd för en Linux-app i en ASE är endast aktiverad i 6 regioner för tillfället: **Västra USA, östra USA, västra Europa, Norra Europa, östra Asien, sydöstra.** 

    d. Välj en **isolerad** prisnivå. Välj **Välj**.

    e. Välj **OK**.
    
    ![Isolerade prisnivåer][2]

    > [!NOTE]
    > Linux-appar och Windows-appar kan inte vara i samma App Service-planen, men kan finnas i samma App Service Environment. 
    >

2. Välj **Skapa**.

## <a name="how-scale-works"></a>Hur skala fungerar ##

Varje App Service-app körs i en App Service plan. Behållare-modellen är miljöer Håll App Service-planer, App Service-planer och håll och appar. När du skalar en app, skala App Service-planen och därmed skala alla appar i samma plan.

I ASEv2, när du skalar en App Service-plan läggs den nödvändiga infrastrukturen automatiskt. Det finns en tidsfördröjning till att skala driften när infrastrukturen har lagts till. I ASEv1 måste du lägga den nödvändiga infrastrukturen innan du kan skapa eller skala ut App Service-planen. 

I multitenant App Service är skalning vanligtvis omedelbar eftersom en pool av resurser är tillgängliga för den. Det finns ingen sådan buffert i en ASE och resurser vid behov.

Du kan skala upp till 100 instanser i en ASE. De 100 instanserna kan finnas i en enda App Service-plan eller fördelade på flera App Service-planer.

## <a name="ip-addresses"></a>IP-adresser ##

App Service har möjlighet att allokera en dedikerad IP-adress till en app. Den här funktionen är tillgänglig när du har konfigurerat en IP-baserad SSL, enligt beskrivningen i [binda ett befintligt anpassat SSL-certifikat till Azure App Service][ConfigureSSL]. I en ASE är det dock en viktig undantag. Du kan inte lägga till ytterligare IP-adresser som ska användas för en IP-baserad SSL i en ILB ASE.

Du måste tilldela IP-adresser som resurser innan du kan använda dem i ASEv1. I ASEv2 använder du dem från din app precis som i App Service för flera innehavare. Det finns alltid en ledig adress i ASEv2 upp till 30 IP-adresser. Varje gång läggs du använder en, en annan så att en adress alltid är tillgängliga för användning. Taget fördröjning krävs för att tilldela en annan IP-adress, vilket förhindrar att lägga till IP-adresser i snabb följd.

## <a name="front-end-scaling"></a>Klientdelskalning ##

I ASEv2, när du skalar ut din App Service-planer läggs arbetare automatiskt som stöder dem. Varje ASE har skapats med två klientdelar. Dessutom slutar framför automatiskt skala ut enligt en taxa på en klientdel för varje 15 instanser i din App Service-planer. Till exempel, om du har 15 instanser, finns så det tre klientdelar. Om du skalar upp till 30 instanser, så finns det fyra klientdelens avslutas och så vidare.

Det här antalet klientdelar ska vara mer än tillräckligt för de flesta scenarier. Dock kan du skala ut i en snabbare takt. Du kan ändra förhållande till så låg som en främre slut för varje fem instanser. Det finns en avgift för att ändra förhållandet. Mer information finns i [Azure App Service-priser][Pricing].

Klientdelsresurserna är HTTP/HTTPS-slutpunkt för ASE. Minnesanvändning per klient är konsekvent cirka 60 procent med frontend standardkonfigurationen. Slutkundsarbetsbelastningar kör inte på en klientdel. Viktiga faktorer för klientsidan med respekt att skala är processor, som drivs i första hand av HTTPS-trafik.

## <a name="app-access"></a>Appåtkomst ##

I en extern ASE skiljer den domän som används när du skapar appar sig från App Service för flera innehavare. Den innehåller namnet på ASE. Mer information om hur du skapar en extern ASE finns i [skapa en App Service environment][MakeExternalASE]. Domännamnet i en extern ASE ut *.&lt; asename&gt;. p.azurewebsites.net*. Exempel: om din ASE heter _extern ase_ och du vara värd för en app som kallas _contoso_ eftersom ASE måste du nå den på följande webbadresser:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

URL: en contoso.scm.external-ase.p.azurewebsites.net används för att få åtkomst till Kudu-konsolen eller för att publicera din app med hjälp av web distribuera. Information om Kudu-konsolen finns i [Kudu-konsolen för Azure App Service][Kudu]. Kudu-konsolen ger dig ett webbgränssnitt för felsökning, ladda upp filer, redigerar filer och mycket mer.

I en ILB ASE måste fastställa du domänen vid tidpunkten för distribution. Läs mer om hur du skapar en ILB ASE, [skapa och använda en ILB ASE][MakeILBASE]. Om du anger domännamnet _ilb-ase.info_, för appar i denna ASE använder domänen under skapa appar. För appen med namnet _contoso_, URL: er är:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publicering ##

Precis som med multitenant App Service i en ASE kan du publicera med:

- Webbdistribution.
- FTP.
- Kontinuerlig integrering.
- Dra och släpp i Kudu-konsolen.
- En IDE, till exempel Visual Studio, Eclipse och IntelliJ IDEA.

Med en extern ASE fungerar dessa alla publiceringsalternativ på samma sätt. Mer information finns i [distribution i Azure App Service][AppDeploy]. 

Den största skillnaden publicering är med avseende på en ILB ASE. Med en ILB ASE publiceringsslutpunkterna alla bara är tillgängliga via den interna Belastningsutjämnaren. Den interna Belastningsutjämnaren finns på en privat IP-adress i ASE-undernät i det virtuella nätverket. Om du inte har åtkomst till den interna Belastningsutjämnaren kan publicera du inte alla appar i denna ASE. Enligt vad som anges i [skapa och använda en ILB ASE][MakeILBASE], måste du konfigurera DNS för appar i systemet. Det omfattar SCM-slutpunkten. Om de inte har definierats korrekt kan publicera du inte. Din IDE: er måste också ha nätverksåtkomst till den interna Belastningsutjämnaren för att publicera direkt till den.

Direkt fungerar Internet-baserad CI-system, t.ex GitHub och Azure DevOps, inte med en ILB ASE eftersom publiceringsslutpunkten inte är åtkomlig via Internet. För Azure DevOps, kan du undvika detta genom att installera en lokal version-agent i det interna nätverket där den kan nå den interna Belastningsutjämnaren. Alternativt kan du också använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Du kan se det i appens publiceringsprofil och i appens portalblad (i **översikt** > **Essentials** och även i **egenskaper**). 

## <a name="pricing"></a>Prissättning ##

Priserna som kallas SKU **isolerad** har skapats för användning endast med ASEv2. Alla App Service-planer som finns i ASEv2 finns i SKU med isolerad prissättning. Isolerad App Service-plan priser kan variera per region. 

Det finns en fast avgift för ASE själva förutom priset för din App Service-planer. Det fasta priset inte ändras med storleken på din ASE och betalar för ASE-infrastrukturen på en standard-skalning mängden ytterligare 1 klientdel för varje 15 App Service-planinstanser.  

Om standard skala antalet 1 klientdel för varje 15 App Service-planinstanser inte är tillräckligt fort, kan du justera förhållandet vid vilka klientdelar läggs eller storleken på klientdelarna.  När du justerar förhållande eller storlek, betalar du för frontend-kärnor som inte skulle läggas till som standard.  

Till exempel om du ändrar skalan förhållandet och 10 en klientdel har lagts till för varje 10 instanser i din App Service-planer. Fast pris täcker tariffen skala en klientdel för varje 15 instanser. Med en skala förhållandet mellan 10 betala en avgift för tredje klientdelen som läggs till för de 10 App Service-planinstanser. Du behöver inte betala för den när du når 15 instanser eftersom den har lagts till automatiskt.

Om du justera storleken på klientdelarna till 2 kärnor, men inte justera förhållandet betala för extra kärnor.  En ASE skapas med 2 klientdelar, så även under automatisk skalning tröskeln du betala för 2 extra kärnor om du ökar storleken till 2 kärnor klientdelar.

Mer information finns i [Azure App Service-priser][Pricing].

## <a name="delete-an-ase"></a>Ta bort en ASE ##

Ta bort en ase-miljö: 

1. Använd **ta bort** överst i den **App Service Environment** bladet. 

1. Ange namnet på din ASE att bekräfta att du vill ta bort den. När du tar bort en ase-miljö kan du ta bort allt innehåll i den också. 

    ![ASE-borttagning][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
