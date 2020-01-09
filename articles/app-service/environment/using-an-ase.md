---
title: Använda och hantera ASE
description: Hur du skapar, publicerar och skalar appar i en Azure App Services miljö. Hitta vanliga uppgifter i ett dokument.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 806d6ddb09cbaf14c9c488e3d3b39909c22ef284
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374938"
---
# <a name="use-an-app-service-environment"></a>Använda en App Service-miljö #

Azure App Service-miljön är en distribution av Azure App Service till ett undernät i en kunds virtuella Azure-nätverk. Det består av:

- **Klient**delar: klient delen upphör där http/https avslutas i en app Services miljö (ASE).
- **Arbetare**: arbetarna är de resurser som är värdar för dina appar.
- **Databas**: databasen innehåller information som definierar miljön.
- **Lagring**: lagringen används som värd för kundens publicerade appar.

> [!NOTE]
> Det finns två versioner av App Service-miljön: ASEv1 och ASEv2. I ASEv1 måste du hantera resurserna innan du kan använda dem. Information om hur du konfigurerar och hanterar ASEv1 finns i [Konfigurera en app service miljö v1][ConfigureASEv1]. Resten av den här artikeln fokuserar på ASEv2.
>
>

Du kan distribuera en ASE (ASEv1 och ASEv2) med en extern eller intern VIP för åtkomst till appar. Distributionen med en extern VIP kallas ofta för en extern ASE. Den interna versionen kallas för ILB-ASE eftersom den använder en intern belastningsutjämnare (ILB). Mer information om ILB-ASE finns i [skapa och använda en ILB-ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Skapa en app i en ASE ##

Om du vill skapa en app i en ASE använder du samma process som när du skapar den normalt, men med några små skillnader. När du skapar en ny App Service plan:

- I stället för att välja en geografisk plats där du vill distribuera din app väljer du en ASE som plats.
- Alla App Services planer som skapats i en ASE måste finnas på en isolerad pris nivå.

Om du inte har en ASE kan du skapa en genom att följa anvisningarna i [skapa en app Services miljö][MakeExternalASE].

Så här skapar du en app i en ASE:

1. Välj **skapa en resurs** > **webb och mobilt** >  **-webbapp**.

2. Ange ett namn för appen. Om du redan har valt en App Service plan i en ASE, återspeglar domän namnet för appen domän namnet för ASE.

    ![Val av app-namn][1]

1. Välj en prenumeration.

1. Ange ett namn för en ny resurs grupp eller Välj **Använd befintlig** och välj en i list rutan.

1. Välj ditt operativsystem. 

1. Välj en befintlig App Service plan i ASE eller skapa en ny genom att följa dessa steg:

    a. Välj **Skapa nytt**.

    b. Ange namnet på App Service plan.

    c. Välj din ASE i list rutan **plats** . 
    
    d. Välj en **isolerad** pris nivå. Välj **Välj**.

    e. Välj **OK**.
    
    ![Isolerade pris nivåer][2]

    > [!NOTE]
    > Linux-appar och Windows-appar kan inte vara i samma App Service plan, men kan vara i samma App Service-miljön. 
    >

2. Välj **Skapa**.

## <a name="how-scale-works"></a>Så här fungerar skala ##

Varje App Service App körs i en App Service plan. Behållar modellen är miljöer som innehåller App Service planer och App Service planer. När du skalar en app skalar du App Service plan och skalar därför alla appar i samma plan.

När du skalar ett App Service plan i ASEv2 läggs den infrastruktur som behövs automatiskt till. Det finns en tids fördröjning för skalning av åtgärder när infrastrukturen läggs till. I ASEv1 måste den infrastruktur som krävs läggas till innan du kan skapa eller skala ut din App Service plan. 

I App Service flera innehavare är skalning vanligt vis direkt eftersom en pool av resurser är lättillgänglig för att stödja den. Det finns ingen sådan buffert i en ASE och resurserna allokeras vid behov.

I en ASE kan du skala upp till 100 instanser. De 100 instanserna kan vara alla i ett enda App Service plan eller fördelas över flera App Services planer.

## <a name="ip-addresses"></a>IP-adresser ##

App Service kan allokera en dedikerad IP-adress till en app. Den här funktionen är tillgänglig när du har konfigurerat en IP-baserad SSL, enligt beskrivningen i [BIND ett befintligt anpassat SSL-certifikat till Azure App Service][ConfigureSSL]. Det finns dock ett särskilt undantag i en ASE. Du kan inte lägga till ytterligare IP-adresser som ska användas för en IP-baserad SSL i en ILB-ASE.

I ASEv1 måste du allokera IP-adresserna som resurser innan du kan använda dem. I ASEv2 använder du dem från din app precis som du gör i App Service för flera innehavare. Det finns alltid en reserv adress i ASEv2 upp till 30 IP-adresser. Varje gång du använder ett, läggs en annan till så att en adress alltid är tillgänglig för användning. En tids fördröjning krävs för att allokera en annan IP-adress, vilket förhindrar att IP-adresser läggs till i snabb följd.

## <a name="front-end-scaling"></a>Skalning på klient Sidan ##

När du skalar dina App Service-planer i ASEv2 läggs arbetare automatiskt till för att stödja dem. Varje ASE skapas med två frontend-sidor. Dessutom skalas klient delen ut automatiskt med en takt på en klient del för varje 15 instanser i dina App Service planer. Om du till exempel har 15 instanser har du tre klient delar. Om du skalar till 30 instanser har du fyra klient delar och så vidare.

Antalet klient delar bör vara mer än tillräckligt för de flesta scenarier. Du kan dock skala ut med en högre hastighet. Du kan ändra förhållandet till så litet som en klient del för var femte instans. Det kostar ett att ändra förhållandet. Mer information finns i [Azure App Service prissättning][Pricing].

Klient dels resurser är HTTP/HTTPS-slutpunkten för ASE. Med standard konfigurationen för klient delen, är minnes användningen konsekvent runt 60 procent. Kundens arbets belastningar körs inte på klient sidan. Nyckel faktorn för en klient del med avseende på skala är processorn, som främst drivs av HTTPS-trafik.

## <a name="app-access"></a>Appåtkomst ##

I en extern ASE skiljer sig den domän som används när du skapar apparna från App Service för flera innehavare. Den innehåller namnet på ASE. Mer information om hur du skapar en extern ASE finns i [skapa en app service-miljö][MakeExternalASE]. Domän namnet i en extern ASE ser ut så här *:&lt;asename&gt;. p.azurewebsites.net*. Om din ASE till exempel heter _extern-ASE_ och du är värd för en app som heter _contoso_ i den ASE, så kommer du till följande URL: er:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

URL-contoso.scm.external-ase.p.azurewebsites.net används för att få åtkomst till kudu-konsolen eller för att publicera din app med hjälp av webb distribution. Information om kudu-konsolen finns i [kudu-konsolen för Azure App Service][Kudu]. Kudu-konsolen ger dig ett webb gränssnitt för fel sökning, överföring av filer, redigering av filer och mycket mer.

I en ILB-ASE bestämmer du domänen vid distributions tiden. Mer information om hur du skapar en ILB-ASE finns i [skapa och använda en ILB-ASE][MakeILBASE]. Om du anger domän namnet _ILB-ASE.info_använder apparna i ASE domänen när appen skapas. URL: erna är följande för appen med namnet _contoso_:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publicering ##

Precis som med App Service med flera innehavare kan du i en ASE publicera med:

- Webb distribution.
- -.
- Kontinuerlig integrering.
- Dra och släpp i kudu-konsolen.
- En IDE, till exempel Visual Studio, Sol förmörkelse eller IntelliJ-idé.

Med en extern ASE fungerar dessa publicerings alternativ som alla. Mer information finns i [distribution i Azure App Service][AppDeploy]. 

Den största skillnaden med publicering är i förhållande till en ILB-ASE. Med en ILB-ASE är publicerings slut punkterna bara tillgängliga via ILB. ILB finns på en privat IP-adress i ASE-undernätet i det virtuella nätverket. Om du inte har nätverks åtkomst till ILB kan du inte publicera några appar på den ASE. Som anges i [skapa och Använd en ILB-ASE][MakeILBASE]måste du konfigurera DNS för apparna i systemet. Som inkluderar SCM-slutpunkten. Om de inte är korrekt definierade kan du inte publicera. Din IDE: er måste också ha nätverks åtkomst till ILB för att kunna publicera direkt till den.

Som standard fungerar inte Internetbaserade CI-system, till exempel GitHub och Azure DevOps, med en ILB-ASE eftersom publicerings slut punkten inte är tillgänglig för Internet. För Azure DevOps kan du undvika detta genom att installera en lokal versions agent i det interna nätverket där den kan komma åt ILB. Du kan också använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Du kan se den i appens publicerings profil och i appens Portal blad (i **översikt** > **Essentials** och även i **Egenskaper**). 

## <a name="pricing"></a>Prissättning ##

Pris-SKU: n som heter **isolerad** skapades endast för användning med ASEv2. Alla App Service planer som finns i ASEv2 finns i den isolerade pris-SKU: n. Isolerade App Service plans priser kan variera per region. 

Utöver priset för dina App Service-planer finns det ett fast pris för ASE. Den fasta taxan ändras inte med storleken på din ASE och betalar för ASE-infrastrukturen med en standard skalnings hastighet på 1 ytterligare klient del för varje 15 App Service plan instans.  

Om standard skalnings frekvensen 1 för varje 15 App Service plan instanser inte är tillräckligt snabb, kan du justera förhållandet mellan front-och utgångs punkter.  När du justerar förhållandet eller storleken betalar du för de front-end-kärnor som inte ska läggas till som standard.  

Om du till exempel ändrar skalnings förhållandet till 10 läggs en klient del till för varje 10-instans i dina App Service planer. Den fasta avgiften omfattar en skalnings takt för en klient del för var 15: a instans. Med en skalnings kvot på 10 betalar du en avgift för den tredje klient delen som har lagts till för de 10 App Service plan instanserna. Du behöver inte betala för det när du når 15 instanser, eftersom den lades till automatiskt.

Om du justerade storleken på frontend-ändarna till 2 kärnor, men inte justerar kvoten, betalar du för de extra kärnorna.  En ASE skapas med 2 klient delar, så till och med under tröskelvärdet för automatisk skalning skulle du betala för 2 extra kärnor om du har ökat storleken till 2 kärnor på klient sidan.

Mer information finns i [Azure App Service prissättning][Pricing].

## <a name="delete-an-ase"></a>Ta bort en ASE ##

Så här tar du bort en ASE: 

1. Använd **ta bort** överst på bladet **App Service-miljön** . 

1. Ange namnet på din ASE för att bekräfta att du vill ta bort den. När du tar bort en ASE tar du även bort allt innehåll i det. 

    ![Borttagning av ASE][3]

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
