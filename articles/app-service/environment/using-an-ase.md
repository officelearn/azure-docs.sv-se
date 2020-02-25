---
title: Använda och hantera en App Service-miljön
description: Hur du skapar, publicerar och skalar appar i en Azure App Services miljö. Hitta vanliga uppgifter i ett dokument.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565704"
---
# <a name="use-an-app-service-environment"></a>Använda en App Service-miljö #

App Service-miljön (ASE) är en distribution av Azure App Service till ett undernät i en kunds virtuella Azure-nätverk. En ASE består av:

- **Frontend-** slut: klient delen är den plats där http/https avslutas i en app Services miljö.
- **Arbetare**: arbetarna är de resurser som är värdar för dina appar.
- **Databas**: databasen innehåller information som definierar miljön.
- **Lagring**: lagringen används som värd för kundens publicerade appar.

Du kan distribuera en ASE med en extern eller intern VIP för åtkomst till appar. Distributionen med en extern VIP kallas ofta för en extern ASE. Den interna versionen kallas för ILB-ASE eftersom den använder en intern belastningsutjämnare (ILB). Mer information om ILB-ASE finns i [skapa och använda en ILB-ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Skapa en app i en ASE ##

Om du vill skapa en app i en ASE använder du samma process som när du skapar den normalt, men med några små skillnader. När du skapar en ny App Service plan (ASP):

- I stället för att välja en geografisk plats där du vill distribuera din app väljer du en ASE som plats.
- Alla App Services planer som har skapats i en ASE kan bara finnas på en isolerad pris nivå.

Om du inte har en ASE kan du skapa en genom att följa anvisningarna i [skapa en app Services miljö][MakeExternalASE].

Så här skapar du en app i en ASE:

1. Välj **skapa en resurs** > **webb och mobilt** >  **-webbapp**.

2. Ange ett namn för appen. Om du redan har valt en App Service plan i en ASE, återspeglar domän namnet för appen domän namnet för ASE.

    ![Val av app-namn][1]

1. Välj en prenumeration.

1. Ange ett namn för en ny resurs grupp eller Välj **Använd befintlig** och välj en i list rutan.

1. Välj ditt operativsystem. 

1. Välj en befintlig App Service plan i ASE eller skapa en ny genom att följa dessa steg:

    a. Välj **skapa en resurs > webbapp**från menyn Azure Portal vänstra sidan.

    b. Välj prenumerationen.
    
    c. Välj eller skapa resurs gruppen.
    
    d. Ange namnet på din webbapp.
    
    e. Välj kod eller DockerContainer.
    
    f. Välj körnings stack.
    
    g. Välj Linux eller Windows. 
    
    h. Välj din ASE i list rutan **region** . 
    
    i. Välj eller skapa en ny App Service plan. Om du skapar en ny App Service plan väljer du lämplig **isolerad** SKU-storlek.
    
    ![Isolerade pris nivåer][2]

    > [!NOTE]
    > Linux-appar och Windows-appar kan inte vara i samma App Service plan, men kan vara i samma App Service-miljön. 
    >

2. Välj **Granska + skapa** och välj sedan **skapa** om informationen är korrekt.

## <a name="how-scale-works"></a>Så här fungerar skala ##

Varje App Service App körs i en App Service plan. App Service miljöer innehåller App Service-planer och App Service planer. När du skalar en app skalar du App Service plan och skalar därför alla appar i samma plan.

När du skalar ett App Service plan läggs den infrastruktur som behövs automatiskt till. Det finns en tids fördröjning för skalning av åtgärder när infrastrukturen läggs till. Om du utför flera skalnings åtgärder i sekvensen, behandlas den första begäran om infrastruktur skalning på och andra köer. När den första skalnings åtgärden slutförs, begär den andra infrastrukturen alla att fungera tillsammans. När infrastrukturen läggs till, tilldelas App Service planer efter behov. Att skapa en ny App Service plan är i sig en skalnings åtgärd när den begär ytterligare maskin vara. 

I App Service flera innehavare är skalningen omedelbart eftersom det är enkelt att använda en pool med resurser för att stödja den. Det finns ingen sådan buffert i en ASE och resurserna allokeras vid behov.

I en ASE kan du skala en App Service plan upp till 100 instanser. En ASE kan ha upp till 201 sammanlagt antal instanser i alla App Service planer som finns i ASE. 

## <a name="ip-addresses"></a>IP-adresser ##

App Service kan allokera en dedikerad IP-adress till en app. Möjligheten att allokera en särskild IP-adress till en app är tillgänglig när du har konfigurerat en IP-baserad SSL, enligt beskrivningen i [BIND ett befintligt anpassat SSL-certifikat till Azure App Service][ConfigureSSL]. I en ILB-ASE kan du inte lägga till ytterligare IP-adresser som ska användas för en IP-baserad SSL.

Med en extern ASE kan du konfigurera IP-baserad SSL för din app på samma sätt som du gör i App Service flera innehavare. Det finns alltid en reserv adress i ASE upp till 30 IP-adresser. Varje gång du använder ett, läggs en annan till så att en adress alltid är tillgänglig för användning. En tids fördröjning krävs för att allokera en annan IP-adress, vilket förhindrar att IP-adresser läggs till i snabb följd.

## <a name="front-end-scaling"></a>Skalning på klient Sidan ##

När du skalar dina App Service-planer läggs arbetare automatiskt till för att stödja dem. Varje ASE skapas med två frontend-ändar. Klient delen kan skalas ut automatiskt med en hastighet av en klient del för varje totalt 15 App Service plan instanser. Om du har tre App Service planer med fem instanser, skulle du ha totalt 15 instanser och tre frontend-sidor. Om du skalar till totalt 30 instanser har du fyra frontend-ändar och så vidare. 

Antalet klient delar som tilldelas som standard är lämpligt för en måttlig belastning. Du kan ändra förhållandet till så litet som en klient del för var femte instans. Du kan också ändra storleken på frontend-slutet. De är som standard en enda kärna. Du kan ändra storleken på frontend-ändarna i portalen till två eller fyra kärn storlekar i stället. Det finns en avgift för att ändra förhållandet eller storlekarna på frontend-ändar. Mer information finns i [Azure App Service prissättning][Pricing]. Om du vill förbättra belastnings kapaciteten för din ASE får du bättre förbättringar genom att först skala till två kärnor innan du justerar skalnings förhållandet. Om du ändrar kärn storleken för dina frontend-ändar kommer din ASE att uppgraderas och bör utföras utanför ordinarie arbets tid.

Klient resurser är HTTP/HTTPS-slutpunkten för ASE. Med standard konfigurationen på klient sidan är minnes användningen konsekvent runt 60 procent. Den främsta anledningen till att skala dina frontend-ändar är CPU, som främst drivs av HTTPS-trafik.

## <a name="app-access"></a>Appåtkomst ##

I en extern ASE är det domänsuffix som används för att skapa appar *.&lt;asename&gt;. p.azurewebsites.net*. Om din ASE har namnet _external-ASE_ och du är värd för en app som heter _contoso_ i den ASE, så kommer du till följande URL: er:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Mer information om hur du skapar en extern ASE finns i [skapa en app service-miljö][MakeExternalASE]

I en ILB-ASE är det domänsuffix som används för att skapa appar *.&lt;asename&gt;. appserviceenvironment.net*. Om din ASE heter _ILB-ASE_ och du är värd för en app som heter _contoso_ i den ASE, så kommer du till följande URL: er:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Mer information om hur du skapar en ILB-ASE finns i [skapa och använda en ILB-ASE][MakeILBASE]. 

SCM-URL: en används för att få åtkomst till kudu-konsolen eller för att publicera din app med hjälp av webb distribution. Information om kudu-konsolen finns i [kudu-konsolen för Azure App Service][Kudu]. Kudu-konsolen ger dig ett webb gränssnitt för fel sökning, överföring av filer, redigering av filer och mycket mer.

## <a name="publishing"></a>Publicera ##

Precis som med App Service med flera innehavare kan du i en ASE publicera med:

- Webb distribution.
- -.
- Kontinuerlig integrering.
- Dra och släpp i kudu-konsolen.
- En IDE, till exempel Visual Studio, Sol förmörkelse eller IntelliJ-idé.

Med en extern ASE fungerar dessa publicerings alternativ som alla. Mer information finns i [distribution i Azure App Service][AppDeploy]. 

Den största skillnaden med publicering är i förhållande till en ILB-ASE. Med en ILB-ASE är publicerings slut punkterna bara tillgängliga via ILB. ILB finns på en privat IP-adress i ASE-undernätet i det virtuella nätverket. Om du inte har nätverks åtkomst till ILB kan du inte publicera några appar på den ASE. Som anges i [skapa och Använd en ILB-ASE][MakeILBASE]måste du konfigurera DNS för apparna i systemet. Som inkluderar SCM-slutpunkten. Om de inte är korrekt definierade kan du inte publicera. Din IDE: er måste också ha nätverks åtkomst till ILB för att kunna publicera direkt till den.

Som standard fungerar inte Internetbaserade CI-system, till exempel GitHub och Azure DevOps, med en ILB-ASE eftersom publicerings slut punkten inte är tillgänglig för Internet. Du kan aktivera publicering till en ILB-ASE från Azure DevOps genom att installera en lokal versions agent i det virtuella nätverket som innehåller ILB ASE. Du kan också använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Du kan se den i appens publicerings profil och i appens Portal blad (i **översikt** > **Essentials** och även i **Egenskaper**). 

## <a name="storage"></a>Storage

En ASE har 1 TB lagrings utrymme för alla appar i ASE. En isolerad SKU App Service plan har en gräns på 250 GB som standard. Om du har fem eller fler App Services planer måste du vara noga med att du inte överskrider gränsen på 1 TB för ASE. Om du behöver mer än 250 GB-gränsen i en App Service plan kan du kontakta supporten för att justera App Service plan gränsen till maximalt 1 TB. När plan gränsen har justerats finns det fortfarande en gräns på 1 TB över alla App Service planer i ASE. 

## <a name="logging"></a>Loggning ##

Du kan integrera din ASE med Azure Monitor för att skicka loggar om ASE till lagring, Event Hub eller Log Analytics. De objekt som loggas idag är:

| Tillståndet | Meddelande |
|---------|----------|
| ASE är inte felfri | Den angivna ASE är inte felfri på grund av en ogiltig konfiguration av virtuellt nätverk. ASE inaktive ras om ohälsosamt tillstånd fortsätter. Se till att de rikt linjer som anges här följs: https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE-undernätet är nästan slut på utrymme | Den angivna ASE finns i ett undernät som är nästan slut på utrymme. Det finns {0} återstående adresser. När de här adresserna är uttömda kan ASE inte skalas  |
| ASE närmar sig den totala instans gränsen | Den angivna ASE närmar sig den totala instans gränsen för ASE. Den innehåller för närvarande {0} App Service plans instanser av högst 201 instanser. |
| ASE kan inte komma åt ett beroende | Det angivna ASE kan inte uppnås {0}.  Se till att de rikt linjer som anges här följs: https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE är inaktive rad | Den angivna ASE har pausats. ASE-inskjutningen kan bero på en konto brister eller en ogiltig konfiguration av virtuellt nätverk. Lös rotor saken och återuppta ASE för att fortsätta betjäna trafik |
| ASE-uppgraderingen har startat | En plattforms uppgradering till den angivna ASE har påbörjats. Förväntade fördröjningar vid skalnings åtgärder |
| ASE-uppgraderingen har slutförts | En plattforms uppgradering till den angivna ASE har avslut ATS |
| Skalnings åtgärder har startat | En App Service plan ({0}) har börjat skala. Önskat tillstånd: {1} jag{2} arbetare 
| Skalnings åtgärder har slutförts | En App Service plan ({0}) har slutat skala. Nuvarande tillstånd: {1} jag{2} arbetare |
| Skalnings åtgärder har misslyckats | Det gick inte att skala en App Service plan ({0}). Nuvarande tillstånd: {1} jag{2} arbetare |

Så här aktiverar du loggning på din ASE: 

1. Gå till diagnostikinställningar i portalen.  
1. Välj Lägg till diagnostisk inställning.
1. Ange ett namn för logg integreringen
1. Kontrol lera och konfigurera önskade logg destinationer. 
1. Kontrol lera AppServiceEnvironmentPlatformLogs

![Logg inställningar för ASE-diagnostik][4]

Om du integrerar med Log Analytics kan du se loggarna genom att välja loggar från ASE-portalen och skapa en fråga mot AppServiceEnvironmentPlatformLogs. 

## <a name="upgrade-preference"></a>Uppgraderings inställning ##

Om du har flera ASE kanske du vill få en del ASE att uppgradera innan andra. I ASE HostingEnvironment Resource Manager-objektet kan du ange ett värde för UpgradePreference. Inställningen upgradePreference kan konfigureras via mall, ARMClient eller https://resources.azure.com.  De tre värde alternativen är:

* Ingen – ingen är standard och innebär att Azure kommer att uppgradera din ASE i ingen särskild batch
* Tidigt – tidigt innebär att din ASE kommer att uppgraderas i den första halvan av App Service uppgraderingar
* Sent-sent innebär att din ASE uppgraderas i den andra halvan av App Service uppgraderingar

Om du använder https://resources.azure.comkan du ange upgradePreferences-värdet genom att:

1. Gå till resources.azure.com och logga in med ditt Azure-konto
1. Navigera genom prenumerationer\/\[prenumerations namn\]\/resourceGroups\/\[resurs grupp namn\]\/providers\/Microsoft. Web\/hostingEnvironments\/\[ASE namn\]
1. Välja Läs/skriv överst
1. Välj Redigera
1. Ändra värdet för upgradePreference till det som önskas från de tre alternativen.
1. Välj korrigering

![resurser Azure com-visning][5]

UpgradePreferences-funktionen är verkligen den mest användbara när du har flera ASE eftersom din "tidiga" uppgraderade ASE uppgraderas före "sent"-ASE. När du har flera ASE bör du ha din dev/test-ASE inställd på "tidigt" och din produktions ASE ska anges som "sent".

## <a name="pricing"></a>Priser ##

Pris-SKU: n som kallas **isolerad** är endast för användning med ASE. Alla App Service planer som finns i ASE finns i den isolerade pris-SKU: n. Isolerade App Service plans priser kan variera per region. 

Utöver priset för dina App Service-planer finns det ett fast pris för ASE. Den fasta taxan ändras inte med storleken på din ASE och betalar för ASE-infrastrukturen med en standard skalnings takt för varje 15 App Service plan instans.  

Om standard skalnings hastigheten för en frontend-period för var 15 App Service plan instanserna inte är tillräckligt snabb, kan du justera förhållandet mellan front-och frontend-slut.  När du justerar förhållandet eller storleken betalar du för de frontend-slut kärnor som inte ska läggas till som standard.  

Om du till exempel ändrar skalnings förhållandet till 10 läggs en klient del till för varje 10-instans i dina App Service planer. Den fasta avgiften omfattar en skalnings takt på en klient del för var 15: a instans. Med en skalnings kvot på 10 betalar du en avgift för den tredje klient delen som har lagts till för de 10 App Service plan instanserna. Du behöver inte betala för det när du når 15 instanser, eftersom den lades till automatiskt.

Om du justerade storleken på frontend-ändarna till två kärnor men inte justerar kvoten, betalar du för de extra kärnorna.  En ASE skapas med två klient delar, så till och med under tröskelvärdet för automatisk skalning betalar du för två extra kärnor om du har ökat storleken till två kärnor i front-ändar.

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
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


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
