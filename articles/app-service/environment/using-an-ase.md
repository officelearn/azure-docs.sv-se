---
title: Använda och hantera en apptjänstmiljö
description: Lär dig hur du skapar, publicerar och skalar appar i en apptjänstmiljö. Hitta alla vanliga uppgifter i den här artikeln.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 8a73c1998203a8696b67a5e7eb3af23898239265
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477632"
---
# <a name="use-an-app-service-environment"></a>Använd App Service Environment

En App Service Environment (ASE) är en distribution av Azure App Service till ett undernät i en kunds Azure Virtual Network-instans. En ASE består av:

- **Front slutar**: Där HTTP eller HTTPS avslutas i en App Service-miljö.
- **Arbetare**: De resurser som är värd för dina appar.
- **Databas**: Innehåller information som definierar miljön.
- **Lagring**: Används som värd för de kundpublicerade apparna.

Du kan distribuera en ASE med en extern eller intern virtuell IP (VIP) för appåtkomst. En distribution med en extern VIP kallas ofta för extern *ASE*. En distribution med en intern VIP kallas en *ILB ASE* eftersom den använder en intern belastningsutjämnare (ILB). Mer information om ILB ASE finns i [Skapa och använda en ILB ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Skapa en app i en ASE

Om du vill skapa en app i en ASE använder du samma process som när du normalt skapar en app, men med några små skillnader. När du skapar en ny apptjänstplan:

- I stället för att välja en geografisk plats där du vill distribuera appen väljer du en ASE som plats.
- Alla App Service-abonnemang som skapas i en ASE kan bara vara i en isolerad prisnivå.

Om du inte har någon ASE kan du skapa en genom att följa instruktionerna i [Skapa en apptjänstmiljö][MakeExternalASE].

Så här skapar du en app i en ASE:

1. Välj Skapa en**resurswebb** >  **Create a resource** > +**Mobilapp**.

1. Ange ett namn för appen. Om du redan har valt en apptjänstplan i en ASE återspeglar appens domännamn domännamn ase:

    ![Val av appnamn][1]

1. Välj en prenumeration.

1. Ange ett namn på en ny resursgrupp eller välj **Använd befintlig** och välj ett i listrutan.

1. Välj ditt operativsystem.

1. Välj en befintlig App Service-plan i DIN ASE eller skapa en ny genom att följa dessa steg:

    a. På Azure-portalens vänstermeny väljer du **Skapa en resurs > Web App**.

    b. Välj prenumerationen.

    c. Markera eller skapa resursgruppen.

    d. Ange namnet på webbappen.

    e. Välj **Kod** eller **DockerContainer**.

    f. Välj en körningsstack.

    g. Välj **Linux** eller **Windows**. 

    h. Välj din ASE i listrutan **Region.** 

    i. Välj eller skapa en ny apptjänstplan. Om du skapar en ny App Service-plan väljer du lämplig **isolerad** SKU-storlek.

    ![Isolerade prisnivåer][2]

    > [!NOTE]
    > Linux-appar och Windows-appar kan inte finnas i samma App Service-plan, men de kan finnas i samma App Service-miljö.
    >

1. Välj **Granska + skapa**, kontrollera att informationen är korrekt och välj sedan **Skapa**.

## <a name="how-scale-works"></a>Så här fungerar skalan

Varje App Service-app körs i en apptjänstplan. App Service-miljöer innehåller App Service-abonnemang och App Service-abonnemang innehåller appar. När du skalar en app skalar du även App Service-planen och alla appar i samma plan.

När du skalar en App Service-plan läggs den nödvändiga infrastrukturen till automatiskt. Det finns en tidsfördröjning för att skala åtgärder medan infrastrukturen läggs till. Om du gör flera skalningsåtgärder i följd, den första begäran om infrastrukturskala åtgärdas och de andra står i kö. När den första skalningsåtgärden är klar fungerar alla andra infrastrukturbegäranden tillsammans. Och när infrastrukturen läggs till tilldelas App Service-planerna efter behov. Att skapa en ny App Service-plan är i sig en skalningsåtgärd eftersom den begär ytterligare maskinvara.

I apptjänsten för flera trogna är skalning omedelbar eftersom en resurspool är lätt tillgänglig för att stödja den. I en ASE finns det ingen sådan buffert och resurser fördelas baserat på behov.

I en ASE kan du skala en apptjänstplan upp till 100 instanser. En ASE kan ha upp till 201 totala instanser i alla App Service-planer i den ASE.

## <a name="ip-addresses"></a>IP-adresser

Apptjänsten kan allokera en dedikerad IP-adress till en app. Den här funktionen är tillgänglig när du har konfigurerat IP-baserad SSL, enligt beskrivningen i [Bind ett befintligt anpassat TLS/SSL-certifikat till Azure App Service][ConfigureSSL]. I en ILB ASE kan du inte lägga till fler IP-adresser som ska användas för IP-baserad SSL.

Med en extern ASE kan du konfigurera IP-baserad SSL för din app på samma sätt som i apptjänsten för flera trogna. Det finns alltid en ledig adress i ASE, upp till 30 IP-adresser. Varje gång du använder en läggs en annan till så att en adress alltid är lätt tillgänglig. En tidsfördröjning krävs för att allokera en annan IP-adress. Den fördröjningen förhindrar att IP-adresser lägger tills i snabb följd.

## <a name="front-end-scaling"></a>Front-end skalning

När du skalar ut apptjänstplanerna läggs arbetare automatiskt till för att stödja dem. Varje ASE skapas med två främre ändar. Frontändarna skalas automatiskt ut med en hastighet av en frontend för varje uppsättning av 15 App Service-planinstanser. Om du till exempel har tre App Service-planer med fem instanser vardera, skulle du ha totalt 15 instanser och tre främre ändar. Om du skalar till totalt 30 instanser har du fyra främre ändar. Det här mönstret fortsätter när du skalar ut.

Antalet frontändar som allokeras som standard är bra för en måttlig belastning. Du kan sänka förhållandet till så lite som en front för varje fem instanser. Du kan också ändra storleken på frontändarna. Som standard är de enstaka kärna. I Azure-portalen kan du ändra deras storlek till två eller fyra kärnor i stället.

Det finns en avgift för att ändra förhållandet eller front-end storlekar. Mer information finns i [Azure App Service priser][Pricing]. Om du vill förbättra laddningskapaciteten för din ASE får du större förbättringar genom att först skala till tvåkärniga frontänden innan du justerar skalningsförhållandet. Om du ändrar kärnstorleken på dina främre ändar kan du uppgradera din ASE och bör göras utanför ordinarie kontorstid.

Frontend-resurser är HTTP/HTTPS-slutpunkten för ASE. Med standardkonfigurationen för klientdelen är minnesanvändningen per fronten konsekvent cirka 60 procent. Den främsta anledningen till att skala dina främre ändar är CPU-användning, som främst drivs av HTTPS-trafik.

## <a name="app-access"></a>Appåtkomst

I ett externt ASE är det domänsuffix som används för att skapa appar *.&lt; asename&gt;.p.azurewebsites.net*. Om din ASE heter _extern ase_ och du är värd för en app som heter _contoso_ i ase, når du den på dessa webbadresser:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Information om hur du skapar en extern ASE finns i [Skapa en apptjänstmiljö][MakeExternalASE].

I en ILB ASE är domänsuffixet som används för att skapa appar *.&lt; asename&gt;.appserviceenvironment.net*. Om din ASE heter _ilb-ase_ och du är värd för en app som heter _contoso_ i ase, når du den på dessa webbadresser:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Information om hur du skapar en ILB ASE finns i [Skapa och använda en ILB ASE][MakeILBASE].

SCM-URL:en används för att komma åt Kudu-konsolen eller för att publicera din app med hjälp av Webbut distribuera. Information om Kudu-konsolen finns i [Kudu-konsolen för Azure App Service][Kudu]. Kudu-konsolen ger dig ett webbgränssnitt för felsökning, uppladdning av filer, redigering av filer och mycket mer.

## <a name="publishing"></a>Publicera

I en ASE, som med apptjänsten för flera trogna, kan du publicera med följande metoder:

- Webbdistribution
- FTP
- Kontinuerlig integration (CI)
- Dra och släpp i Kudu-konsolen
- En IDE, till exempel Visual Studio, Eclipse eller IntelliJ IDEA

Med en extern ASE fungerar dessa publiceringsalternativ på samma sätt. Mer information finns [i Distribution i Azure App Service][AppDeploy].

Publicering skiljer sig avsevärt med en ILB ASE, för vilken publiceringsslutpunkterna endast är tillgängliga via ILB. ILB finns på en privat IP i ASE-undernätet i det virtuella nätverket. Om du inte har nätverksåtkomst till ILB kan du inte publicera några appar på ase-området. Som anges i [Skapa och använda en ILB ASE][MakeILBASE]måste du konfigurera DNS för apparna i systemet. Detta krav omfattar SCM-slutpunkten. Om slutpunkterna inte är korrekt definierade kan du inte publicera. Dina ID-företag måste också ha nätverksåtkomst till ILB för att kunna publicera direkt till den.

Utan ytterligare ändringar fungerar internetbaserade KI-system som GitHub och Azure DevOps inte med en ILB ASE eftersom publiceringsslutpunkten inte är tillgänglig för internet. Du kan aktivera publicering till en ILB ASE från Azure DevOps genom att installera en självvärdad versionsagent i det virtuella nätverket som innehåller ILB ASE. Alternativt kan du också använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Du kan se den i appens publiceringsprofil och i appens portalfönster (i > **Översiktsavsikter** och även i **Egenskaper**). **Overview**

## <a name="storage"></a>Storage

En ASE har 1 TB lagringsutrymme för alla appar i ASE. En App Service-plan i den isolerade prissättningen SKU har en gräns på 250 GB som standard. Om du har fem eller fler App Service-planer bör du vara noga med att inte överskrida 1 TB-gränsen för ASE. Om du behöver mer än gränsen på 250 GB i en App Service-plan kontaktar du supporten för att justera appserviceplansgränsen till maximalt 1 TB. När plangränsen har justerats finns det fortfarande en gräns på 1 TB i alla App Service-planer i ASE.

## <a name="logging"></a>Loggning

Du kan integrera din ASE med Azure Monitor för att skicka loggar om ASE till Azure Storage, Azure Event Hubs eller Log Analytics. Dessa objekt loggas idag:

| Situationen | Meddelande |
|---------|----------|
| ASE är ohälsosamt | Det angivna ASE-avtalet är felaktigt på grund av en ogiltig konfiguration av virtuella nätverk. ASE kommer att avbrytas om det felaktiga tillståndet fortsätter. Se till att de https://docs.microsoft.com/azure/app-service/environment/network-inforiktlinjer som definieras här följs: . |
| ASE-undernätet är nästan utan utrymme | Det angivna ASE finns i ett undernät som är nästan på utrymme. Det {0} finns återstående adresser. När dessa adresser är uttömda kommer ASE inte att kunna skalas.  |
| ASE närmar sig total instansgräns | Den angivna ASE närmar sig den totala instansgränsen för ASE. Den innehåller {0} för närvarande App Service Plan-instanser med högst 201 instanser. |
| ASE kan inte nå ett beroende | Det angivna ASE-programmet kan {0}inte nå .  Se till att de https://docs.microsoft.com/azure/app-service/environment/network-inforiktlinjer som definieras här följs: . |
| ASE är tillfälligt | Det angivna ASE-systemet är pausat. ASE-avstängningen kan bero på ett kontobortfall eller en ogiltig virtuell nätverkskonfiguration. Lös grundorsaken och återuppta ASE för att fortsätta betjäna trafik. |
| ASE-uppgraderingen har startat | En plattformsuppgradering till angiven ASE har påbörjats. Räkna med förseningar i skalningsåtgärder. |
| ASE-uppgraderingen har slutförts | En plattformsuppgradering till angiven ASE har slutförts. |
| Skalningsåtgärder har startat | En apptjänstplan{0}( ) har börjat skalas. Önskat {1} tillstånd: Jag{2} arbetare.
| Skalningsåtgärderna har slutförts | En apptjänstplan{0}( ) har skalats klart. Nuvarande {1} tillstånd:{2} Jag arbetare. |
| Skalningsåtgärder har misslyckats | Det gick inte{0}att skala en apptjänstplan ( ) . Nuvarande {1} tillstånd:{2} Jag arbetare. |

Så här aktiverar du loggning på din ASE:

1. Gå till **Diagnostikinställningarna**i portalen .
1. Välj **Lägg till diagnostikinställning**.
1. Ange ett namn för loggintegrering.
1. Markera och konfigurera önskade loggmål.
1. Välj **AppServiceEnvironmentPlatformLogs**.

![Ase-diagnostiklogginställningar][4]

Om du integrerar med Log Analytics kan du se loggarna genom att välja **Loggar** från ASE-portalen och skapa en fråga mot **AppServiceEnvironmentPlatformLogs**.

## <a name="upgrade-preference"></a>Uppgraderingsinställning

Om du har flera ASEs, kanske du vill att vissa ASEs ska uppgraderas före andra. I ASE **HostingEnvironment Resource Manager-objektet** kan du ange ett värde för **upgradePreference**. **Inställningen upgradePreference** kan konfigureras med hjälp av en https://resources.azure.commall, ARMClient eller . De tre möjliga värdena är:

- **Ingen:** Azure uppgraderar din ASE i ingen särskild batch. Detta värde är standard.
- **Tidigt:** Din ASE kommer att uppgraderas under den första halvan av App Service uppgraderingar.
- **Sent:** Din ASE kommer att uppgraderas under andra halvan av App Service uppgraderingar.

Om du använder https://resources.azure.comgör du så här för att ange **värdet för upgradePreferences:**

1. Gå till resources.azure.com och logga in med ditt Azure-konto.
1. Gå igenom resurserna till\/\[prenumerationer prenumerationsnamn\]\/resourceGroups\/\[resursgruppsnamnsleverantörer\]\/\/Microsoft.Web\/hostingMiljöer\/\[ASE-namn\].
1. Välj **Läs/skriv** högst upp.
1. Välj **Redigera**.
1. Ange **upgradePreference** till vilket av de tre värden du vill ha.
1. Välj **Korrigeringsfil**.

![resurser azure com display][5]

**UpgradePreferences-funktionen** är mest meningsfull när du har flera ASEs eftersom dina "tidiga" ASEs kommer att uppgraderas innan din "Sena" ASEs. När du har flera ASEs, bör du ställa in din utveckling och testa ASEs vara "tidigt" och din produktion ASEs vara "Sent".

## <a name="pricing"></a>Prissättning

Prissättningen SKU kallas *isolerade* är endast för användning med ASEs. Alla App Service-abonnemang som finns i ASE finns i SKU:n för isolerad prissättning. Isolerade priser för App Service-planer kan variera mellan olika regioner.

Förutom priset på dina App Service-planer finns det ett schablonbelopp för SJÄLVA ASE. Det fasta priset ändras inte med storleken på din ASE. Det betalar för ASE-infrastrukturen med en standardskala på ytterligare en front för varje 15 App Service-planinstanser.

Om standardskalningshastigheten för en frontend för varje 15 App Service-planinstanser inte är tillräckligt snabb, kan du justera förhållandet med vilket klientändarna läggs till eller storleken på klientändarna. När du justerar förhållandet eller storleken betalar du för frontend-kärnor som inte skulle läggas till som standard.

Om du till exempel justerar skalningsförhållandet till 10 läggs en klientdel till för varje 10-instans i apptjänstplanerna. Den fasta avgiften täcker en skalenlig skattesats på en front för varje 15 instanser. Med ett skalförhållande på 10 betalar du en avgift för den tredje fronten som läggs till för 10 App Service-planinstanserna. Du behöver inte betala för det när du når 15 instanser eftersom det lades till automatiskt.

Om du justerar storleken på frontändarna till två kärnor men inte justerar förhållandet betalar du för de extra kärnorna. En ASE skapas med två främre ändar, så även under den automatiska skalningströskeln skulle du betala för två extra kärnor om du ökade storleken till tvåkärniga frontänden.

Mer information finns i [Azure App Service priser][Pricing].

## <a name="delete-an-ase"></a>Ta bort en ASE

Så här tar du bort en ASE:

1. Välj **Ta bort** högst upp i fönstret App Service **Environment.**

1. Ange namnet på ASE för att bekräfta att du vill ta bort den. När du tar bort en ASE tar du också bort allt innehåll i den.

    ![ASE-borttagning][3]

1. Välj **OK**.

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
