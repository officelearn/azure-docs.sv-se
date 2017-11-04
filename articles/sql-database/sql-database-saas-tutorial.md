---
title: "Självstudier för flera innehavare SaaS - Azure SQL Database | Microsoft Docs"
description: "Distribuera och utforska Wingtip SaaS flera innehavare-program som visar SaaS mönster med Azure SQL Database."
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
ms.date: 07/26/2017
ms.author: sstein
ms.openlocfilehash: f624485d32901abb5f0d01c86fc30ddaaae261a3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-and-explore-a-multi-tenant-application-that-uses-azure-sql-database---wingtip-saas"></a>Distribuera och utforska ett program med flera innehavare som använder Azure SQL Database - Wingtip SaaS

I kursen får du distribuera och utforska Wingtip SaaS-program. Programmet använder ett databas-per-klient, SaaS-programmönster för att hantera flera klienter. Programmet är utformad för att presentera Azure SQL Database-funktioner som förenklar aktiverar SaaS-scenarier.

Fem minuter efter att du klickar på knappen *distribuera till Azure* nedan, har du ett fungerande SaaS-program för flera klienter som använder SQL Database i molnet. Programmet har distribuerats med tre exempel klienter, var och en med sin egen databas alla distribuerade till en elastisk SQL-pool. Appen har distribuerats till din Azure-prenumeration som ger fullständig åtkomst till utforska och fungerar med enskilda programkomponenter. Programmet källa kod och hantering av skript är tillgängliga i WingtipSaaS GitHub-lagringsplatsen.


I den här guiden lär du dig:

> [!div class="checklist"]

> * Så här distribuerar du Wingtip SaaS-program
> * Var du kan hämta programmets källkod och av hanteringsskript
> * Om de servrar, pooler och databaser som ingår i appen
> * Hur klienter mappas till sina data med den *katalog*
> * Hur du etablerar en ny klient
> * Så här övervakar du klient aktivitet i appen

Om du vill utforska olika SaaS design- och hanteringsmönster, finns det en [uppsättning relaterade guider](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials) som bygger på den här inledande distributionen. När du går igenom guiderna, kan du undersöka skripten som tillhandahålls och se hur de olika SaaS-mönstren implementeras. Stega dig igenom skripten i varje guide för att få en djupare förståelse för hur man implementerar de olika SQL Database-funktionerna som förenklar utvecklingen av SaaS-program.

## <a name="prerequisites"></a>Krav

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-saas-application"></a>Distribuera Wingtip SaaS-program

Distribuera Wingtip SaaS app:

1. Klicka på den **till Azure** knappen öppnar Distributionsmall Wingtip SaaS Azure portal. Mallen kräver två parametervärden. ett namn för en ny resursgrupp och ett användarnamn som särskiljer den här distributionen från andra distributioner av Wingtip SaaS-app. Nästa steg innehåller information för att ange dessa värden.

   Se till att Observera exakta värden som du använder, eftersom du behöver ange dem senare i en konfigurationsfil.

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Ange nödvändiga parametervärden för distribution:

    > [!IMPORTANT]
    > En del autentisering och brandväggar för servern har avsiktligt lämnats oskyddade i demonstrationssyfte. **Skapa en ny resursgrupp**, och Använd inte befintliga resursgrupper, servrar eller pooler. Använd inte det här programmet eller alla resurser som den skapar för produktion. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.

    * **Resursgruppen** – Välj **Skapa nytt** och ger en **namn** för resursgruppen. Välj en **plats** från den nedrullningsbara listan.
    * **Användaren** -resurser kräver globalt unikt namn. För att säkerställa unikhet varje gång du distribuerar programmet ange ett värde att skilja mellan resurser som du skapar från resurser som skapats av andra distribution av programmet Wingtip. Vi rekommenderar att du använder ett kort **användar**namn, till exempel dina initialer plus ett tal (till exempel *bg1*) och att du sedan använder det som resursgruppens namn (till exempel *wingtip-bg1*). Den **användaren** parametern kan bara innehålla bokstäver, siffror och bindestreck (inga blanksteg). Det första och sista tecknet måste vara en bokstav eller en siffra (alla gemener rekommenderas).


1. **Distribuera programmet**.

    * Klicka om du accepterar villkoren.
    * Klicka på **Köp**.

1. Övervaka distributionsstatus genom att klicka på **aviseringar** (klockikonen till höger om sökrutan). Distribuera appen Wingtip SaaS tar cirka fem minuter.

   ![distributionen lyckades](media/sql-database-saas-tutorial/succeeded.png)

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Hämta och avblockera Wingtip SaaS-skript

Ladda ned källan kod och hantering av skript vid distribution av programmet.

> [!IMPORTANT]
> Körbara innehållet (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. Följ stegen nedan för att låsa upp ZIP-filen innan du extraherar när extraherar skript från en zip-fil. Detta säkerställer att skript tillåts köra.

1. Bläddra till [github-repo-Wingtip SaaS](https://github.com/Microsoft/WingtipSaaS).
1. Klicka på **kloning eller hämta**.
1. Klicka på **hämta ZIP** och spara filen.
1. Högerklicka på den **WingtipSaaS master.zip** fil och markera **egenskaper**.
1. På den **allmänna** väljer **avblockera**, och klicka på **tillämpa**.
1. Klicka på **OK**.
1. Extrahera filerna.

Skript finns i den *... \\WingtipSaaS master\\Learning moduler* mapp.

## <a name="update-the-configuration-file-for-this-deployment"></a>Uppdatera konfigurationsfilen för den här distributionen

Innan du kör alla skript, ange den *resursgruppen* och *användare* värdena i **UserConfig.psm1**. Ange dessa variabler de värden som du anger under distributionen.

1. Öppna... \\inlärningsmoduler\\*UserConfig.psm1* i *PowerShell ISE*
1. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på rader 10 och 11 endast).
1. Spara ändringarna!

Det här helt enkelt att hålla du behöver uppdatera dessa distribution-specifika värden i alla skript.

## <a name="run-the-application"></a>Köra programmet

Appen visar platser, som konsertlokaler, jazzklubbar, sportklubbar som organiserar evenemang. Handelsplatser registrera som kunder (eller klienter) i Wingtip-plattformen för ett enkelt sätt att visa en lista över händelser och sälja biljetter. Varje plats får en personaliserad webbapp för att hantera sina evenemang och sälja biljetter, oberoende och isolerat från andra klienter. Varje klient hämtar under försättsbladen, en SQL-databas som har distribuerats till en elastisk SQL-pool.

En central **evenemangshubb** innehåller en lista över de klient-URL:er som är specifika för din distribution.

1. Öppna den _händelser hubb_ i webbläsaren: http://events.wtp.&lt; ANVÄNDAREN&gt;. trafficmanager.net (Ersätt med användarnamnet för din distribution):

    ![evenemangshubben](media/sql-database-saas-tutorial/events-hub.png)

1. Klicka på **Fabrikam Jazzklubb** i *evenemangshubben*.

   ![Händelser](./media/sql-database-saas-tutorial/fabrikam.png)


Att kontrollera distributionen av inkommande begäranden, appen använder [ *Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Evenemangssidorna, som är klientspecifika, kräver att klientnamnen finns med i URL:erna. Alla klient-URL:er inkluderar ditt specifika *användare*-värde och har det här formatet: http://events.wtp.&lt; användare&gt;.trafficmanager.net/*fabrikamjazzclub*. Evenemangsappen parsar klientnamnet från URL:en och använder det för att skapa en nyckel för att komma åt katalogen med hjälp av[*fragmentkarthantering*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). Katalogen mappar nyckeln till klientens databasplatsen. Den **händelser hubb** använder utökade metadata i katalogen för att hämta namnet på den klient som är associerade med varje databas för att ange URL-listan.

I en produktionsmiljö, skulle du vanligtvis skapa en CNAME DNS-post för att [*peka ett företags internetdomän*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) till Traffic Manager-profilen.

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

Den första distributionen skapas tre exempel hyresgäster, men nu ska vi skapa en annan klient om du vill se hur detta påverkar det distribuerade programmet. Wingtip SaaS etablering hyresgäster arbetsflödet beskrivs i den [etablera och katalogen kursen](sql-database-saas-tutorial-provision-and-catalog.md). I det här steget kan skapa du snabbt en ny klient.

1. Öppna... \\inlärningsmoduler\etablera och katalogisera\\*Demo-ProvisionAndCatalog.ps1* i *PowerShell ISE*.
1. Tryck på **F5** att köra skriptet (lämna standardvärdena för tillfället).

   > [!NOTE]
   > Många SaaS Wingtip skript använder *$PSScriptRoot* att navigera mappar för att anropa funktioner i andra skript. Den här variabeln utvärderas bara när skriptet har körts genom att trycka på **F5**.  Markera och kör en markering (**F8**) kan leda till fel, trycker du på **F5** när du kör skript.

Den nya innehavare databasen skapas i en elastisk SQL-pool, initiera och registreras i katalogen. Efter lyckad etableringen är den nya innehavaren biljett sälja *händelser* platsen visas i webbläsaren:

![Ny klient](./media/sql-database-saas-tutorial/red-maple-racing.png)

Uppdatera den *händelser hubb* och den nya innehavaren visas i listan.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Utforska servrar, pooler, och klientdatabaser

Nu när du har startat körs en belastning för samlingen av innehavare kan vi titta på några av de resurser som har distribuerats:

1. I den [Azure-portalen](http://portal.azure.com)genom att bläddra i listan över SQL-servrar och öppna den **katalog -&lt;användare&gt;**  server. Katalogservern innehåller två databaser. Den **tenantcatalog**, och **basetenantdb** (en tom *gyllene* eller malldatabasen som kopieras om du vill skapa nya klienter).

   ![databaser](./media/sql-database-saas-tutorial/databases.png)

1. Gå tillbaka till listan med SQL-servrar och öppna den **tenants1 -&lt;användare&gt;**  server som innehåller klient-databaser. Varje klient-databasen är en _elastisk Standard_ databas i en standard 50 eDTU-pool. Observera också att det finns en _Röd lönn tävling_ databasen, klient-databasen som du tidigare har etablerats.

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Övervaka poolen

Om belastningsgeneratorn har kört i flera minuter, borde tillräckliga data finnas tillgänglig för att börja titta på några av de inbyggda övervakningsfunktionerna i poolerna och databaserna.

1. Bläddra till servern **tenants1 -&lt;användare&gt;**, och klicka på **Pool1** att visa resursanvändningen för poolen (belastningen generator körde för en timme i följande diagram):

   ![övervaka poolen](./media/sql-database-saas-tutorial/monitor-pool.png)

De här diagrammen visar hur väl lämpade elastiska pooler och SQL Database är för arbetsbelastningar från SaaS-program. Fyra databaser som toppar på så mycket som 40 eDTU:er stöds enkelt i en 50 eDTU-pool. Om de har etablerats som fristående databaser, de skulle varje måste vara en S2 (50 DTU) för att stödja belastning. Kostnaden för 4 fristående S2 databaser är nästan 3 gånger priset på poolen och poolen har fortfarande tillräckligt med utrymme för många flera databaser. I verkliga situationer använder SQL Database kunder för närvarande upp till 500 databaser i 200 eDTU pooler. Mer information finns i [prestandaövervakningsguiden](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]

> * Så här distribuerar du Wingtip SaaS-program
> * Om de servrar, pooler och databaser som ingår i appen
> * Klienter mappas till sina data med *katalogen*
> * Hur du etablerar nya klienter
> * Hur du ser poolanvändning för att övervaka klientaktivitet
> * Hur du tar bort exempelresurser för att stoppa relaterad fakturering

Nu kan du testa [guiden etablera och katalogisera](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip SaaS-program](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* Läs mer om elastiska pooler, i [*vad är en Azure SQL elastisk pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Läs om elastiska jobb i [*hantera utskalade molndatabaser*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* Läs mer om SaaS-program med flera klienter i [*Designmönster för SaaS-program med flera klienter*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)
