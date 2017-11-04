---
title: "Azure SQL-databas med flera innehavare app exempel – Wingtip SaaS | Microsoft Docs"
description: "Lär dig mer med hjälp av ett exempelprogram för flera innehavare som använder Azure SQL Database, Wingtip SaaS-exempel"
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.openlocfilehash: 46c9a3eadc2c23959b4d08649c6c0215d44b493e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Introduktion till en SQL-databas med flera innehavare SaaS app-exempel

Den *Wingtip SaaS* programmet är en exempelapp för flera innehavare som visar de unika fördelarna med SQL-databas. Appen använder ett SaaS-mönster med en databas-per-klient, för att hantera flera klienter. Appen är avsedd att demonstrerar funktionerna i Azure SQL Database som möjliggör SaaS scenarier, inklusive flera SaaS design- och mönster. För att snabbt komma igång, distribuerar Wingtip SaaS-appen på mindre än fem minuter!

Programmet källa koden och hantering av skript är tillgängliga i den [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-lagringsplatsen. Att köra skripten [hämtningsmapp Learning moduler](#download-and-unblock-the-wingtip-saas-scripts) till den lokala datorn.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL-databas Wingtip SaaS-självstudier

När appen har distribuerats, utforska följande kurser som bygger på den första distributionen. Dessa självstudiekurser utforska vanliga SaaS-mönster som utnyttjar inbyggda funktioner för SQL Database, SQL Data Warehouse och andra Azure-tjänster. Självstudier innehåller PowerShell-skript med förklaringar som förenklar förstå och implementera samma SaaS management mönster i dina program.


| Självstudier | Beskrivning |
|:--|:--|
|[Distribuera och utforska Wingtip SaaS-program](sql-database-saas-tutorial.md)| **BÖRJA HÄR!** Distribuera och utforska Wingtip SaaS-program på Azure-prenumerationen. |
|[Etablera och katalogen klienter](sql-database-saas-tutorial-provision-and-catalog.md)| Lär dig hur programmet ansluter till klienter som använder en katalog-databas och hur klienter mappas till deras data i katalogen. |
|[Övervaka och hantera prestanda](sql-database-saas-tutorial-performance-monitoring.md)| Lär dig hur du använder övervakningsfunktionerna i SQL-databas och hur du ställer in meddelanden när tröskelvärden för prestanda har överskridits. |
|[Övervakare med logganalys (OMS)](sql-database-saas-tutorial-log-analytics.md) | Lär dig mer om hur du använder [logganalys](../log-analytics/log-analytics-overview.md) att övervaka stora mängder resurser över flera pooler. |
|[Återställa en enskild klient](sql-database-saas-tutorial-restore-single-tenant.md)| Lär dig mer om att återställa en databas för klient till en tidigare tidpunkt. Steg för att återställa till en parallell databas, lämnar den befintliga databasen klient online, ingår också. |
|[Hantera schemat för klient](sql-database-saas-tutorial-schema-management.md)| Lär dig mer om att uppdatera schema och uppdatera referensdata, över alla Wingtip SaaS-klienter. |
|[Köra ad hoc-analytics](sql-database-saas-tutorial-adhoc-analytics.md) | Skapa en ad hoc-analytics databas och kör realtid distribuerade frågor över alla klienter.  |
|[Kör klienten analytics](sql-database-saas-tutorial-tenant-analytics.md) | Extrahera klientdata till en analytics databasen eller data warehouse kör offline analytiska frågor. |



## <a name="application-architecture"></a>Programarkitektur

Wingtip SaaS-appen använder databasen per klient modellen och använder SQL elastiska pooler för att maximera effektiviteten. En katalog databas används för etablering och mappningen klienter till deras data. Core Wingtip SaaS-program använder en pool med tre exempel innehavare plus katalogdatabasen. Slutför många av de Wingtip SaaS självstudier resultera i tillägg till den första distributionen genom att introducera analytiska databaser flera databaser schemahantering, osv.


![Wingtip SaaS-arkitekturen](media/sql-database-wtp-overview/app-architecture.png)


När du går igenom självstudierna och arbetar med appen är det viktigt att fokusera på SaaS-mönster i samband med datanivå. Med andra ord: fokusera på datanivån och överanalysera inte själva appen. Implementeringen av dessa SaaS mönster är att förstå för att implementera dessa mönster i dina program när du funderar på att alla nödvändiga ändringar för dina specifika affärsbehov.

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Hämta och avblockera Wingtip SaaS-skript

Körbara innehållet (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. När skript från en zip-fil, ***Följ stegen nedan för att låsa upp ZIP-filen innan du extraherar***. Detta säkerställer att skript tillåts köra.

1. Bläddra till [github-repo-Wingtip SaaS](https://github.com/Microsoft/WingtipSaaS).
1. Klicka på **kloning eller hämta**.
1. Klicka på **hämta ZIP** och spara filen.
1. Högerklicka på den **WingtipSaaS master.zip** fil och markera **egenskaper**.
1. På den **allmänna** väljer **avblockera**.
1. Klicka på **OK**.
1. Extrahera filerna.

Skript finns i den *... \\WingtipSaaS master\\Learning moduler* mapp.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Arbeta med PowerShell-skript Wingtip SaaS

Om du vill få ut mesta av exemplet behöver du fördjupa dig i de angivna skript. Använd brytpunkter och gå igenom de skript som undersöker information om hur de olika SaaS-mönster implementeras. För att enkelt gå igenom angivna skript och moduler för bästa förstå, bör du använda den [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Uppdatera konfigurationsfilen för distributionen

Redigera den **UserConfig.psm1** fil med resurs-grupp- och värdet som anges under distributionen:

1. Öppna den *PowerShell ISE* och läsa in... \\Learning moduler\\*UserConfig.psm1* 
1. Uppdatera *ResourceGroupName* och *namn* med specifika värden för din distribution (på rader 10 och 11 endast).
1. Spara ändringarna!

Om dessa värden anges här helt enkelt att du inte behöver uppdatera dessa distribution-specifika värden i alla skript.

### <a name="execute-scripts-by-pressing-f5"></a>Kör skript genom att trycka på F5

Använda flera skript *$PSScriptRoot* att navigera mappar, och *$PSScriptRoot* utvärderas bara när skript körs genom att trycka på **F5**.  Markera och kör en markering (**F8**) kan leda till fel, trycker du på **F5** när du kör skript.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Stega igenom skripten för att undersöka implementeringen

Det bästa sättet att förstå skripten är genom att gå igenom dem och se vad de gör. Kolla in de medföljande **Demo -** skript som finns ett lättöverskådligt arbetsflöde på hög nivå. Den **Demo -** skript visar de steg som krävs för att utföra varje aktivitet, så ange brytpunkter och öka detaljnivån djupare i enskilda anrop för att se implementeringsdetaljerna för de olika SaaS-mönster.

Tips för att utforska och stega igenom PowerShell-skript:

* Öppna **Demo -** skript i PowerShell ISE.
* Köra eller fortsätta med **F5** (med hjälp av **F8** rekommenderas inte eftersom *$PSScriptRoot* utvärderas inte när du kör val av ett skript).
* Placera brytpunkter genom att klicka, eller välja en rad och trycka på **F9**.
* Stega över en funktion eller skriptanrop med **F10**.
* Stega in i en funktion eller skriptanrop med **F11**.
* Stega ut ur den aktuella funktionen eller skriptanropet med **Skift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Utforska databasschemat och kör SQL-frågor med SSMS

Använd [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) att ansluta och bläddra programservrar och databaser.

Ursprungligen har två SQL Database-servrar för att ansluta till – den *tenants1 -&lt;användare&gt;*  server, och *katalog -&lt;användaren&gt;*  server. För att säkerställa en lyckad demo-anslutning, båda servrarna har en [brandväggsregel](sql-database-firewall-configure.md) så att alla IP-adresser via.


1. Öppna *SSMS* och anslut till servern *tenants1-&lt;användare&gt;.database.windows.net*.
1. Klicka på **anslut** > **databasmotor...** :

   ![katalogserver](media/sql-database-wtp-overview/connect.png)

1. Autentiseringsuppgifterna för demot är: inloggningsnamn = *developer*, lösenord = *P@ssword1*

   ![anslutning](media\sql-database-wtp-overview\tenants1-connect.png)

1. Upprepa stegen 2-3 och anslut till servern *catalog-&lt;användare&gt;.database.windows.net*.

När du har anslutit, borde du se bägge servrarna. I listan över databaser kan vara olika beroende på de klienter du har etablerat:

![objektutforskaren](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Nästa steg

[Distribuera Wingtip SaaS-program](sql-database-saas-tutorial.md)
