---
title: Avancerad datasäkerhet för IaaS i Azure Security Center | Microsoft-dokument
description: " Lär dig hur du aktiverar avancerad datasäkerhet för IaaS i Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282736"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Avancerad datasäkerhet för SQL-servrar på Virtuella Azure-datorer (förhandsversion)
Avancerad datasäkerhet för SQL-servrar på Virtuella Azure-datorer är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den här förhandsgranskningsfunktionen innehåller funktioner för att identifiera och minska potentiella databassårbarheter och identifiera avvikande aktiviteter som kan tyda på hot mot databasen. 

Det här säkerhetserbjudandet för Azure VMs SQL-servrar baseras på samma grundläggande teknik som används i [Azure SQL Database Advanced Data Security Package](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Översikt

Avancerad datasäkerhet ger en uppsättning avancerade SQL-säkerhetsfunktioner, bestående av sårbarhetsbedömning och avancerat hotskydd.

* [Sårbarhetsbedömning](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) är en enkel att konfigurera tjänst som kan identifiera, spåra och hjälpa dig att åtgärda potentiella databassårbarheter. Det ger insyn i ditt säkerhetstillstånd och innehåller stegen för att lösa säkerhetsproblem och förbättra dina databasbefästningar.
* [Avancerat skydd mot hot](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din SQL-server. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och ger åtgärdsorienterade säkerhetsaviseringar på avvikande databasåtkomstmönster. Dessa aviseringar ger information om misstänkt aktivitet och rekommenderade åtgärder för att undersöka och minska hotet.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Komma igång med avancerad datasäkerhet för SQL på virtuella Azure-datorer

Följande steg får dig att komma igång med Avancerad datasäkerhet för SQL på Azure VMs Public Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Konfigurera avancerad datasäkerhet för SQL på virtuella Azure-datorer

Aktivera avancerad datasäkerhet för SQL-servrar på virtuella datorer på prenumerations-/arbetsytanivå:
 
1. Öppna sidan Priser & inställningar **i** Säkerhetscenters sidofält.

1. Välj den prenumeration eller arbetsyta som du vill aktivera avancerad datasäkerhet för SQL på virtuella Azure-datorer.

1. Växla alternativet för **SQL-servrar på vm (förhandsversion)** så att det är aktiverat. 

    (Klicka på skärmdumpen för att expandera)

    [![Rekommendationer och aviseringar i Security Center som visas i Administrationscenter för Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Avancerad datasäkerhet för SQL-servrar aktiveras på alla SQL-servrar som är anslutna till den valda arbetsytan eller standardarbetsytan för den valda prenumerationen.

    >[!NOTE]
    > Lösningen kommer att vara helt aktiv efter den första omstarten av SQL Server. 

Om du vill skapa en ny arbetsyta följer du instruktionerna i [Skapa en log analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Om du vill ansluta SQL Server-värden till en arbetsyta följer du instruktionerna i [Anslut Windows-datorer till Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-security-alerts"></a>Konfigurera e-postmeddelande för säkerhetsaviseringar 

Du kan ange en lista över mottagare för att få ett e-postmeddelande när Security Center-aviseringar genereras. E-postmeddelandet innehåller en direktlänk till aviseringen i Azure Security Center med all relevant information. 

1. Gå till**security center-priser** **Security Center** > & inställningar och klicka på relevant prenumeration

    ![Inställningar för prenumeration](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Klicka på **E-postmeddelanden**på Menyn Inställningar . 
1. I textrutan **E-postadress** anger du e-postadresserna för att få meddelandena. Du kan ange mer än en e-postadress genom att separera e-postadresserna med ett kommatecken (,).  Till admin1@mycompany.comexempeladmin2@mycompany.com,admin3@mycompany.com

    ![E-postinställningar](./media/security-center-advanced-iaas-data/email-settings.png)

1. Ange följande alternativ i inställningarna **för e-postmeddelande:**
  
    * **Skicka e-postavisering för varningar med hög allvarlighetsgrad**: I stället för att skicka e-postmeddelanden för alla aviseringar skickar du bara för aviseringar med hög allvarlighetsgrad.
    * **Skicka även e-postmeddelanden till prenumerationsägare**: Skicka meddelanden till prenumerationsägarna också.

1. Klicka på **Spara**högst upp på skärmen **E-postaviseringar** .

  > [!NOTE]
  > Var noga med att klicka på **Spara** innan du stänger fönstret, annars sparas inte de nya **e-postmeddelandeninställningarna.**

## <a name="explore-vulnerability-assessment-reports"></a>Utforska sårbarhetsbedömningsrapporter

Instrumentpanelen för bedömning av sårbarhet ger en översikt över dina utvärderingsresultat i alla databaser. Du kan visa distributionen av databaser enligt SQL Server-versionen, tillsammans med en sammanfattning av misslyckade databaser jämfört med att skicka databaser och en övergripande sammanfattning av misslyckade kontroller enligt riskdistribution.

Du kan visa resultaten för sårbarhetsbedömning direkt från Security Center.

1. Välj **Data & Lagring i**Säkerhetscenters sidofält under RESURSSÄKERHETSHYGIEN .

1. Välj **rekommendationssäkerhetsproblem på dina SQL-databaser i virtuella datorer ska åtgärdas (förhandsgranska)**. Mer information finns i [Rekommendationer för Säkerhetscenter](security-center-recommendations.md). 

    [![**Sårbarheter i DINA SQL-databaser i virtuella datorer bör åtgärdas (förhandsversion)** rekommendation](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Den detaljerade vyn för denna rekommendation visas.

    [![Detaljerad vy för **Säkerhetsproblem på dina SQL-databaser i virtuella datorer bör åtgärdas (förhandsversion)** rekommendation](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Så här detaljgransar du för mer information:

    * Om du vill ha en översikt över skannade resurser (databaser) och listan över säkerhetskontroller som testades klickar du på den server som är av intresse.
    [![Säkerhetsproblem grupperade efter SQL-server](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Om du vill ha en översikt över de säkerhetsproblem som grupperas av en viss SQL-databas klickar du på intressedatabasen.
    [![Säkerhetsproblem grupperade efter SQL-server](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    I varje vy sorteras säkerhetskontrollerna efter **allvarlighetsgrad**. Klicka på en specifik säkerhetskontroll om du vill visa ett informationsfönster med en **beskrivning,** hur du **åtgärdar** den och annan relaterad information, till exempel **Impact** eller **Benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Avancerat hotskydd för SQL-servrar på Azure-virtuella datorer
Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja SQL-servrar. Dessa händelser kan utlösa följande aviseringar:

### <a name="anomalous-access-pattern-alerts-preview"></a>Avvikande åtkomstmönstervarningar (förhandsgranskning)

* **Tillgång från ovanlig plats:** Den här aviseringen utlöses när åtkomstmönstret ändras till SQL-servern, där någon har loggat in på SQL-servern från en ovanlig geografisk plats. Potentiella orsaker:
    * En angripare eller tidigare skadlig anställd har använt din SQL Server.
    * En legitim användare har åtkomst till din SQL Server från en ny plats.
* **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. Potentiella orsaker:
    * En angripare som försöker bryta mot din SQL med hjälp av vanliga attackverktyg.
    * En legitim penetrationstestning i aktion.
* **Access from unfamiliar principal** (Åtkomst från ovanligt huvudkonto): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern med ett ovanligt huvudkonto (SQL-användare). Potentiella orsaker:
    * En angripare eller tidigare skadlig anställd har använt din SQL Server. 
    * En legitim användare har kommit åt din SQL Server från med ett nytt huvudnamn.
* **Brute force SQL credentials** (Nyckelsökningsattack mot SQL-autentiseringsuppgifter): Den här aviseringen utlöses när det sker ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. Potentiella orsaker:
    * En angripare som försöker bryta mot din SQL med brute force.
    * En legitim penetrationstestning i aktion.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potentiella SQL-injektionsattacker (stöds i SQL Server 2019)

* **Säkerhetsproblem för SQL-injektion:** Den här aviseringen utlöses när ett program genererar ett felaktigt SQL-uttryck i databasen. Aviseringen kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Potentiella orsaker:
    * Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
    * Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
* **Potential SQL injection** (Potentiell SQL-inmatning): Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Kommandot Osäkert (stöds i SQL Server 2019)

* **Potentiellt osäker åtgärd**: Den här aviseringen utlöses när ett mycket privilegierat och potentiellt osäkert kommando körs. Potentiella orsaker:
    * Kommando som rekommenderas att inaktiveras för bättre säkerhetshållning är aktiverat.
    * En angripare som försöker utnyttja SQL-åtkomst eller eskalera privilegier.   


## <a name="explore-and-investigate-security-alerts"></a>Utforska och undersöka säkerhetsaviseringar

Dina datasäkerhetsaviseringar är tillgängliga på Säkerhetscenters sida för aviseringar, resursens säkerhetsflik eller via den direkta länken i e-postmeddelandena.

1. För att visa aviseringar:

    * I Security Center - Klicka på **Säkerhetsaviseringar** från sidofältet och välj en avisering.
    * I resursomfattningen - Öppna relevant resurssida och klicka på **Säkerhet**från sidofältet . 

1. Aviseringar är utformade för att vara fristående, med detaljerade reparationssteg och undersökningsinformation i var och en. Du kan undersöka vidare genom att använda andra Azure Security Center- och Azure Sentinel-funktioner för en bredare vy:

    * Aktivera SQL Server granskningsfunktion för ytterligare undersökningar. Om du är azure sentinel-användare kan du ladda upp SQL-granskningsloggarna från Händelserna i Windows Security Log till Sentinel och få en omfattande undersökningsupplevelse.
    * Om du vill förbättra säkerhetspositionen använder du Security Centers rekommendationer för den värddator som anges i varje avisering. Detta kommer att minska riskerna för framtida attacker. 


## <a name="next-steps"></a>Nästa steg

För relaterat material, se följande artikel:

- [Så här åtgärdar du rekommendationer](security-center-remediate-recommendations.md)