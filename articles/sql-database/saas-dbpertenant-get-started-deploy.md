---
title: "Självstudier för flera innehavare SaaS - Azure SQL Database | Microsoft Docs"
description: "Distribuera och utforska Wingtip biljetter SaaS flera innehavare-program som visar databas per klient och andra SaaS-mönster med Azure SQL Database."
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: f91ddff81e51e7cc3d1561dc799013764530924b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Distribuera och utforska ett SaaS-program med flera innehavare som använder databasen per klient mönster med Azure SQL Database

I den här självstudiekursen, distribuera och utforska Wingtip biljetter SaaS databasen per klient program. Programmet använder ett mönster för databasen per klient, för att lagra data för flera innehavare. Programmet är utformad för att presentera Azure SQL Database-funktioner som förenklar aktiverar SaaS-scenarier.

Fem minuter efter att du klickar på knappen *distribuera till Azure* nedan, har du ett fungerande SaaS-program för flera klienter som använder SQL Database i molnet. Programmet har distribuerats med tre exempel klienter, var och en med sin egen databas alla distribuerade till en elastisk SQL-pool. Appen har distribuerats till din Azure-prenumeration som ger fullständig åtkomst till utforska och fungerar med enskilda programkomponenter. Programmet källa kod och hantering av skript är tillgängliga i WingtipTicketsSaaS DbPerTenant GitHub-lagringsplatsen.


I den här guiden lär du dig:

> [!div class="checklist"]

> * Så här distribuerar du Wingtip SaaS-program
> * Var du kan hämta programmets källkod och av hanteringsskript
> * Om de servrar, pooler och databaser som ingår i appen
> * Hur klienter mappas till sina data med den *katalog*
> * Hur du etablerar en ny klient
> * Så här övervakar du klient aktivitet i appen

Om du vill utforska olika SaaS design- och hanteringsmönster, finns det en [uppsättning relaterade guider](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) som bygger på den här inledande distributionen. När du går igenom guiderna, kan du undersöka skripten som tillhandahålls och se hur de olika SaaS-mönstren implementeras. Stega dig igenom skripten i varje guide för att få en djupare förståelse för hur man implementerar de olika SQL Database-funktionerna som förenklar utvecklingen av SaaS-program.

## <a name="prerequisites"></a>Krav

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuera Wingtip biljetter SaaS-program

Distribuera appen:

1. Klicka på den **till Azure** knappen öppnar Wingtip biljetter SaaS databas per klient Distributionsmall i Azure-portalen. Mallen kräver två parametervärden. ett namn för en ny resursgrupp och ett användarnamn som särskiljer den här distributionen från andra distributioner av Wingtip biljetter SaaS databasen per klient app. Nästa steg innehåller information för att ange dessa värden.

   Se till att Observera exakta värden som du använder, eftersom du behöver ange dem senare i en konfigurationsfil.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Ange nödvändiga parametervärden för distribution:

    > [!IMPORTANT]
    > En del autentisering och brandväggar för servern har avsiktligt lämnats oskyddade i demonstrationssyfte. **Skapa en ny resursgrupp**. Använd inte befintliga resursgrupper, servrar eller pooler. Använd inte det här programmet, skript och eventuella distribuerade resurser för produktion. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.

    * **Resursgruppen** – Välj **Skapa nytt** och ger en **namn** för resursgruppen. 
    * **Plats** – Välj en **plats** från den nedrullningsbara listan.
    * **Användaren** -resurser kräver globalt unikt namn. För att säkerställa unikhet varje gång du distribuerar programmet ange ett värde att skilja mellan resurser som du skapar från resurser som skapats av andra distribution av programmet Wingtip. Vi rekommenderar att du använder en kort **användare** namn, till exempel din initialer plus ett tal (till exempel *af1*), och sedan använda som resursgruppens namn (till exempel *wingtip af1*). Den **användaren** parametern kan bara innehålla bokstäver, siffror och bindestreck (inga blanksteg). Det första och sista tecknet måste vara en bokstav eller en siffra (alla gemener rekommenderas).


1. **Distribuera programmet**.

    * Klicka om du accepterar villkoren.
    * Klicka på **Köp**.

1. Övervaka distributionsstatus genom att klicka på **aviseringar** (klockikonen till höger om sökrutan). Distribuera appen Wingtip biljetter SaaS tar cirka fem minuter.

   ![distributionen lyckades](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Hämta och avblockera hanteringsskript Wingtip biljetter

Ladda ned källan kod och hantering av skript vid distribution av programmet.

> [!IMPORTANT]
> Körbara innehållet (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. Följ stegen nedan för att låsa upp ZIP-filen innan du extraherar när extraherar skript från en zip-fil. Detta säkerställer att skript tillåts köra.

1. Bläddra till den [WingtipTicketsSaaS DbPerTenant GitHub-repo-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
1. Klicka på **kloning eller hämta**.
1. Klicka på **hämta ZIP** och spara filen.
1. Högerklicka på den **WingtipTicketsSaaS-DbPerTenant-master.zip** fil och markera **egenskaper**.
1. På den **allmänna** väljer **avblockera**, och klicka på **tillämpa**.
1. Klicka på **OK**.
1. Extrahera filerna.

Skript finns i den *... \\WingtipTicketsSaaS DbPerTenant master\\Learning moduler* mapp.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Uppdatera konfigurationsfilen för användaren för den här distributionen

Innan du kör alla skript måste du uppdatera den *resursgruppen* och *användare* värdena i **UserConfig.psm1**. Ange de värden som du använde under distributionen av dessa variabler.

1. I den *PowerShell ISE*öppnar... \\Learning moduler\\*UserConfig.psm1* 
1. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på rader 10 och 11 endast).
1. Spara ändringarna!

Dessa värden refereras i nästan alla skript.

## <a name="run-the-application"></a>Köra programmet

Appen riktar sig till platser, till exempel konsertsalar, jazz föreningar och sport föreningar som värd för händelser. Handelsplatser registrera som kunder (eller klienter) Wingtip biljetter för ett enkelt sätt att visa en lista över händelser och sälja biljetter. Varje plats hämtar en anpassad webbplats för att hantera och visa deras händelser och sälja biljetter oberoende och isoleras från andra klienter. Varje klient hämtar under försättsbladen, en SQL-databas som har distribuerats till en elastisk SQL-pool.

Central **händelser hubb** sidan innehåller en lista med länkar till innehavare i distributionen.

1. Öppna den _händelser hubb_ i webbläsaren: http://events.wingtip-dpt.&lt;användare&gt;. trafficmanager.net (ersätta &lt;användaren&gt; med din distribution användaren värde):

    ![evenemangshubben](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Klicka på **Fabrikam Jazzklubb** i *evenemangshubben*.

   ![Händelser](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


Att kontrollera distributionen av inkommande begäranden, appen använder [ *Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Evenemangssidorna, som är klientspecifika, kräver att klientnamnen finns med i URL:erna. Alla de URL: er omfattar ditt specifika *användare* värde och följande format: http://events.wingtipp-dpt.&lt;användare&gt;.trafficmanager.net/*fabrikamjazzclub*. Appen händelser Parsar klientnamn från URL: en och används för att skapa en nyckel för att komma åt en katalog som implementeras med hjälp av [ *Fragmentera kartan management*](sql-database-elastic-scale-shard-map-management.md). Katalogen mappar nyckeln till klientens databasplatsen. Den **händelser hubb** använder utökade metadata i katalogen för att hämta namnet på den klient som är associerade med varje databas för att ange URL-listan.

I en produktionsmiljö, skulle du vanligtvis skapa en CNAME DNS-post för att [*peka ett företags internetdomän*](../traffic-manager/traffic-manager-point-internet-domain.md) till Traffic Manager-profilen.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats, vi få det att fungera! Den *Demo-LoadGenerator* PowerShell-skript startar en arbetsbelastning som körs mot alla klient-databaser. Verkliga belastningen på många SaaS-appar är vanligtvis sporadiska och oförutsägbart. Om du vill simulera den här typen av belastningen producerar generatorn last fördelad över alla klienter med slumpmässig belastning på varje klient som uppstår vid slumpmässiga intervall. Därför det tar flera minuter innan load mönstret ut så är det bäst att låta generator kör minst tre eller fyra minuter innan övervakning belastningen.

1. I den *PowerShell ISE*öppnar den... \\Learning moduler\\verktyg\\*Demo-LoadGenerator.ps1* skript.
1. Tryck **F5** för att köra skriptet och starta belastningsgeneratorn (Lämna standardvärdet för parametern för tillfället).

> [!IMPORTANT]
> Öppna ett nytt fönster i PowerShell ISE för att köra andra skript. Belastningsgeneratorn körs som en serie jobb i din lokala PowerShell-session. Den *Demo-LoadGenerator.ps1* skript systemtillstånd aktiveras faktiska belastningen generator skript som körs som en aktivitet i förgrunden plus en serie belastningen generations bakgrundsjobb. Ett jobb belastningen generator anropas för varje databas som har registrerats i katalogen. Jobben körs i din lokala PowerShell-session, så att stänga PowerShell-sessionen avbryts alla jobb. Om du avbryter din dator, load generation pausas och återupptas när du aktiverar din dator.

När belastningen generator anropar belastningen generations jobb för varje klient, förgrunds-aktiviteten finns kvar i ett jobb startar tillstånd där den startas ytterligare bakgrundsjobb för alla nya klienter som tillhandahålls senare. Du kan använda *Ctrl-C* eller tryck på den *stoppa* knappen för att stoppa aktiviteten förgrunden, men befintliga bakgrundsjobb fortsätter genererar belastningen på varje databas. Om du behöver att övervaka och kontrollera bakgrundsjobb, Använd *Get-Job*, *Receive-Job* och *stoppa jobbet*. När aktiviteten förgrunden körs kan du inte använda samma PowerShell-session för att köra andra skript. Öppna ett nytt fönster i PowerShell ISE för att köra andra skript.

Om du vill starta om belastningen generator sessionen med olika parametrar, till exempel du kan stoppa aktiviteten förgrunden och och kör den *Demo-LoadGenerator.ps1* skript. Köra *Demo-LoadGenerator.ps1* första stoppar alla pågående jobb och startar sedan om generator som aktiveras av en ny uppsättning jobb med de aktuella parametrarna.

Lämna belastningen generatorn kör i läget anropar jobbet just nu.


## <a name="provision-a-new-tenant"></a>Etablera en ny klient

Den första distributionen skapas tre exempel hyresgäster, men nu ska vi skapa en annan klient om du vill se hur detta påverkar det distribuerade programmet. Wingtip biljetter SaaS etablering hyresgäster arbetsflödet beskrivs i den [etablera och katalogen kursen](saas-dbpertenant-provision-and-catalog.md). I det här steget kan skapa du snabbt en ny klient.

1. I den *PowerShell ISE*öppnar... \\Modules\Provision och katalogen\\*Demo-ProvisionAndCatalog.ps1* .
1. Tryck på **F5** att köra skriptet (lämna standardvärdena för tillfället).

   > [!NOTE]
   > Många SaaS Wingtip skript använder *$PSScriptRoot* att navigera mappar för att anropa funktioner i andra skript. Den här variabeln utvärderas bara när fullständig skriptet körs genom att trycka på **F5**.  Markera och kör en markering (**F8**) kan leda till fel, trycker du på **F5** när du kör skript.

Den nya innehavare databasen skapas i en elastisk SQL-pool, initiera och registreras i katalogen. Efter lyckad etablering, den nya innehavaren *händelser* platsen visas i webbläsaren:

![Ny klient](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Uppdatera den *händelser hubb* och den nya innehavaren visas i listan.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Utforska servrar, pooler, och klientdatabaser

Nu när du har startat körs en belastning för samlingen av innehavare kan vi titta på några av de resurser som har distribuerats:

1. I den [Azure-portalen](http://portal.azure.com)genom att bläddra i listan över SQL-servrar och öppna den **katalog-dpt -&lt;användare&gt;**  server. Katalogservern innehåller två databaserna, den **tenantcatalog** och **basetenantdb** (en mall för databas som kopieras om du vill skapa nya klienter).

   ![databaser](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Gå tillbaka till listan med SQL-servrar och öppna den **tenants1-dpt -&lt;användare&gt;**  server som innehåller klient-databaser. Varje klient-databasen är en _elastisk Standard_ databas i en standard 50 eDTU-pool. Observera också att det finns en _Röd lönn tävling_ databasen, klient-databasen som du tidigare har etablerats.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Övervaka poolen

Om belastningsgeneratorn har kört i flera minuter, borde tillräckliga data finnas tillgänglig för att börja titta på några av de inbyggda övervakningsfunktionerna i poolerna och databaserna.

Bläddra till servern **tenants1-dpt -&lt;användare&gt;**, och klicka på **Pool1** att visa resursanvändningen för poolen (belastningen generator körde för en timme i följande diagram):

   ![övervaka poolen](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

Övre diagrammet visar pool-eDTU-användning, medan ned diagrammet visar eDTU-användning av övre 5 databaserna i poolen.  De här diagrammen visar hur väl lämpade elastiska pooler och SQL Database är för arbetsbelastningar från SaaS-program. Fyra databaser som toppar på så mycket som 40 eDTU:er stöds enkelt i en 50 eDTU-pool. Om de har etablerats som fristående databaser, de skulle varje måste vara en S2 (50 DTU) för att stödja belastning. Kostnaden för 4 fristående S2 databaser är nästan 3 gånger priset på poolen och poolen har fortfarande tillräckligt med utrymme för många flera databaser. I verkliga situationer använder SQL Database kunder för närvarande upp till 500 databaser i 200 eDTU pooler. Mer information finns i [prestandaövervakningsguiden](saas-dbpertenant-performance-monitoring.md).


## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]

> * Så här distribuerar du Wingtip biljetter SaaS-program
> * Om de servrar, pooler och databaser som ingår i appen
> * Klienter mappas till sina data med *katalogen*
> * Hur du etablerar nya klienter
> * Hur du ser poolanvändning för att övervaka klientaktivitet
> * Hur du tar bort exempelresurser för att stoppa relaterad fakturering

Prova den [etablera och katalogen kursen](saas-dbpertenant-provision-and-catalog.md).



## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip biljetter SaaS databasen per klient program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* Läs mer om elastiska pooler, i [*vad är en Azure SQL elastisk pool*](sql-database-elastic-pool.md)
* Läs om elastiska jobb i [*hantera utskalade molndatabaser*](sql-database-elastic-jobs-overview.md)
* Läs mer om SaaS-program med flera klienter i [*Designmönster för SaaS-program med flera klienter*](saas-tenancy-app-design-patterns.md)
