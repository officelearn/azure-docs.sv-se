---
title: Använda en Azure Apptjänst-miljö
description: Skapa, publicera och skala appar i Azure Apptjänst-miljö
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
ms.openlocfilehash: 66ef20616df77dc809a79e516a53133a80759dc7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355312"
---
# <a name="use-an-app-service-environment"></a>Använd en Apptjänst-miljö #

## <a name="overview"></a>Översikt ##

Azure Apptjänst-miljön är en distribution av Azure App Service till ett undernät i kundens virtuella Azure-nätverket. Det består av:

- **Främre ends**: frontwebbservrarna är där HTTP/HTTPS avslutas i en Apptjänst-miljö (ASE).
- **Anställda**: arbetare är de resurser som är värdar för dina appar.
- **Databasen**: databasen innehåller information som definierar miljön.
- **Lagring**: lagringsutrymmet som används som värd för kund-publicerade appar.

> [!NOTE]
> Det finns två versioner av Apptjänstmiljö: ASEv1 och ASEv2. Du måste hantera resurserna innan du kan använda dem i ASEv1. Information om hur du konfigurerar och hanterar ASEv1 finns [konfigurerar du en Apptjänst-miljö v1][ConfigureASEv1]. Resten av den här artikeln fokuserar på ASEv2.
>
>

Du kan distribuera en ASE (ASEv1 och ASEv2) med en extern eller intern VIP för åtkomst till appen. Distribution med en extern VIP kallas vanligtvis en extern ASE. Den interna versionen kallas ILB ASE eftersom den använder en intern belastningsutjämnare (ILB). Läs mer om ILB ASE i [skapa och använda en ILB ASE][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Skapa en webbapp i en ASE ##

Om du vill skapa en webbapp i en ASE måste använda samma process som när du skapar normalt, men med några mindre skillnader. När du skapar en ny programtjänstplan:

- I stället för att välja en geografisk plats där du vill distribuera din app kan välja du en ASE som din plats.
- Alla programtjänstplaner som skapats i en ASE måste finnas i en isolerad prisnivån.

Om du inte har en ASE, kan du skapa en genom att följa instruktionerna i [skapa en Apptjänst-miljö][MakeExternalASE].

Skapa en webbapp i en ASE:

1. Välj **skapar du en resurs** > **webb + mobilt** > **Web App**.

2. Ange ett namn för webbappen. Om du redan har valt en apptjänstplan i en ASE visar domännamnet för ASE domännamnet för appen.

    ![Val av nätverksnamn för Web app][1]

3. Välj en prenumeration.

4. Ange ett namn för en ny resursgrupp eller välj **använda befintliga** och välj en från den nedrullningsbara listan.

5. Välj ditt operativsystem. 

    * Värd för en Linux-app i en ASE är en ny funktion i förhandsversionen, så vi rekommenderar att du inte lägger till Linux-appar i en ASE som körs för tillfället produktionsarbetsbelastningar. 
    * Att lägga till en Linux-app i en ASE innebär att ASE också i förhandsgranskningsläge. 

5. Välj en befintlig programtjänstplan i din ASE eller skapa en ny genom att följa dessa steg:

    a. Välj **skapa nya**.

    b. Ange namn för din programtjänstplan.

    c. Välj din ASE i den **plats** listrutan. Värd för en Linux-app i en ASE är endast aktiverad i 6 områden för tillfället: **västra USA, östra USA, västra Europa, Norra Europa, östra, Sydostasien.** 

    d. Välj en **isolerad** prisnivån. Välj **Välj**.

    e. Välj **OK**.
    
    ![Isolerade prisnivåer][2]

    > [!NOTE]
    > Linux-webbprogram och Windows web apps får inte finnas i den samma App Service-Plan, men kan vara i samma Apptjänst-miljön. 
    >

6. Välj **Skapa**.

## <a name="how-scale-works"></a>Hur skala fungerar ##

Varje App Service-appen körs i en apptjänstplan. Behållaren modellen är miljöer håller App Service-planer och programtjänstplaner håller appar. När du skalar en app, skala App Service-plan och skala därför alla appar i samma plan.

I ASEv2, när du skalar en apptjänstplan läggs den nödvändiga infrastrukturen automatiskt. Det finns en tidsfördröjning till skalningsåtgärder när infrastrukturen har lagts till. ASEv1, måste infrastrukturen som behövs läggas innan du kan skapa eller skala upp din programtjänstplan. 

I multitenant Apptjänst är skalning vanligtvis omedelbar eftersom en pool av resurser är tillgängliga att stödja den. Det finns ingen sådan buffert i en ASE och resurser vid behov.

Du kan skala upp till 100 instanser i en ASE. De 100 instanserna kan finnas i en enda App Service-plan eller fördelade på flera programtjänstplaner.

## <a name="ip-addresses"></a>IP-adresser ##

Apptjänst har möjlighet att allokera en dedicerad IP-adress till en app. Den här funktionen är tillgänglig när du har konfigurerat IP-baserade SSL, enligt beskrivningen i [binda ett befintligt anpassat SSL-certifikat till Azure-webbappar][ConfigureSSL]. Men i en ASE finns viktiga undantag. Du kan inte lägga till ytterligare IP-adresser som ska användas för en IP-baserade SSL i en ASE ILB.

Du måste tilldela IP-adresser som resurser innan du kan använda dem i ASEv1. I ASEv2 använder du dem från din app precis som i multitenant Apptjänst. Det finns alltid en ledig adress i ASEv2 upp till 30 IP-adresser. Varje gång läggs du använder någon annan så att en adress alltid är tillgängliga för användning. Fördröjningen är att tilldela en annan IP-adress, vilket förhindrar att lägga till IP-adresser i snabb följd.

## <a name="front-end-scaling"></a>Frontend skalning ##

I ASEv2, när du skalar upp din App Service-planer läggs arbetare automatiskt till de stöds. Varje ASE skapas med två frontwebbservrarna. Dessutom slutar framför automatiskt skala ut med en hastighet av en klientdel för varje 15 instanser i App Service-planer. Till exempel om du har 15 instanser måste ha du tre frontwebbservrarna. Om du skalar till 30 instanser, finns det fyra front slutar och så vidare.

Det här antalet frontwebbservrarna bör vara mer än tillräckligt för de flesta scenarier. Du kan dock skala ut i en snabbare takt. Du kan ändra förhållandet till så låg som en främre slut för varje fem instanser. Det finns en avgift för att ändra förhållandet. Mer information finns i [priser för Azure App Service][Pricing].

Frontend resurser är HTTP/HTTPS-slutpunkt för ASE. Minnesanvändningen per klientdelen är konsekvent cirka 60 procent med frontend standardkonfigurationen. Kundens arbetsbelastningar körs inte på en klientdel. Avgörande för en klientdel vad gäller skala är CPU, som styrs i första hand av HTTPS-trafik.

## <a name="app-access"></a>Appåtkomst ##

I en extern ASE skiljer den domän som används när du skapar appar sig från multitenant Apptjänst. Den innehåller namnet på ASE. Läs mer om hur du skapar en extern ASE [skapa en Apptjänst-miljö][MakeExternalASE]. Domännamnet i en extern ASE ser ut som *.&lt; asename&gt;. p.azurewebsites.net*. Om din ASE heter exempelvis _externa ase_ och du är värd för en app som kallas _contoso_ i att ASE, du når den på följande webbadresser:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

URL: en contoso.scm.external-ase.p.azurewebsites.net används för att få åtkomst till Kudu-konsolen eller för att publicera en app med hjälp av web distribuera. Information om Kudu-konsolen finns [Kudu-konsol för Azure App Service][Kudu]. Kudu-konsolen ger dig en webbgränssnittet för felsökning, överför filer, redigera filer och mycket mer.

I en ILB ASE bestämma domänen vid tidpunkten för distribution. Läs mer om hur du skapar en ILB ASE [skapa och använda en ILB ASE][MakeILBASE]. Om du anger domännamnet _ilb ase.info_, appar i den ASE använda denna domän under skapande av app. För program med namnet _contoso_, URL: er är:

- Contoso.ilb ase.info
- Contoso.SCM.ilb ase.info

## <a name="publishing"></a>Publicering ##

Precis som med multitenant Apptjänst i en ASE kan du publicera med:

- Web-distribution.
- FTP.
- Kontinuerlig integrering.
- Dra och släpp i Kudu-konsolen.
- IDE-miljö, till exempel för Visual Studio, Eclipse eller IntelliJ IDEA.

Med en extern ASE fungerar alternativen publishing alla på samma sätt. Mer information finns i [distribution i Azure App Service][AppDeploy]. 

Den största skillnaden med publicering är med avseende på en ILB ASE. Med en ILB ASE publishing slutpunkter alla bara är tillgängliga via ILB. ILB finns på en privat IP-adress i ASE undernät i det virtuella nätverket. Om du inte har åtkomst till ILB kan du publicera alla appar på den ASE. Enligt beskrivningen i [skapa och använda en ILB ASE][MakeILBASE], måste du konfigurera DNS för program i systemet. Som innehåller SCM-slutpunkten. Om de inte är rätt definierad, kan du publicera. Din IDEs måste också ha tillgång till ILB nätverk för att publicera till den.

Internet-baserade CI system, t.ex GitHub och Visual Studio Team Services fungerar inte med en ILB ASE eftersom publishing slutpunkt inte är tillgänglig Internet. Istället måste du använda ett CI-system som använder en pull-modell, som Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Du kan se den i appens publiceringsprofilen och appens portalbladet (i **översikt** > **Essentials** och även i **egenskaper**). 

## <a name="pricing"></a>Prissättning ##

Priser SKU kallas **isolerad** skapades för ASEv2. Alla programtjänstplaner som finns i ASEv2 är isolerad priser SKU. Isolerade priser för Apptjänst-planen kan variera per region. 

Förutom priset för din App Service-planer finns en plan för ASE sig själv. Fast stöd ändras inte med storleken på din ASE och betalar för ASE infrastruktur på en standard skalning av ytterligare 1 frontend för varje 15 App Service-plan instanser.  

Om standard skala antalet 1 klientdelen för varje 15 App Service-plan instanser inte är tillräckligt snabbt, kan du justera förhållandet vid vilka framför-servrar har lagts till eller storleken på framsidan-servrar.  När du justerar förhållande eller storlek, betalar för frontend kärnor som inte skulle läggas till som standard.  

Till exempel om du ändrar skala förhållandet mellan-10 en klientdel har lagts till för varje 10 instanser i App Service-planer. Fasta avgifter omfattar en skala andel en klientdel för varje 15 instanser. Med en skala förhållandet mellan 10 betala en avgift för tredje klientdelen som har lagts till för de 10 App Service-plan instanserna. Du behöver inte betala för den när du når 15 instanser eftersom den har lagts till automatiskt.

Om du justeras storleken på fram till slutet på 2 kärnor men inte ändra förhållandet betala för extra kärnor.  En ASE skapas med 2 framför-servrar, så även under automatisk skalning tröskelvärdet som du ska betala för extra 2 kärnor om du ökar storleken till 2 kärnor framför-servrar.

Mer information finns i [priser för Azure App Service][Pricing].

## <a name="delete-an-ase"></a>Ta bort en ASE ##

Ta bort en ASE: 

1. Använd **ta bort** överst i den **Apptjänstmiljö** bladet. 

2. Ange namnet på din ASE att bekräfta att du vill ta bort den. När du tar bort en ASE kan du ta bort allt innehåll i den samt. 

    ![Ta bort ASE][3]

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
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
