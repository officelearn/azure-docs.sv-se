---
title: Avancerad data säkerhet för IaaS i Azure Security Center | Microsoft Docs
description: " Lär dig hur du aktiverar avancerad data säkerhet för IaaS i Azure Security Center. "
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
ms.openlocfilehash: 068fb9f61b7dcb3948e4f03c284ddfa680522c85
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907037"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Avancerad data säkerhet för SQL-servrar på Azure Virtual Machines (för hands version)
Avancerad data säkerhet för SQL-servrar på Azure Virtual Machines är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den här förhands gransknings funktionen innehåller funktioner för att identifiera och åtgärda potentiella databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på hot mot databasen. 

Det här säkerhets erbjudandet för virtuella Azure-datorer med SQL-servrar baseras på samma grundläggande teknik som används i [Azure SQL Database Advanced Data Security-paketet](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Översikt

Avancerad data säkerhet ger en uppsättning avancerade SQL-säkerhetsfunktioner, som består av sårbarhets bedömning och Avancerat skydd.

* [Sårbarhets bedömning](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) är ett enkelt sätt att konfigurera en tjänst som kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Den ger insyn i ditt säkerhets tillstånd och innehåller stegen för att lösa säkerhets problem och förbättra din databas Fortifications.
* [Avancerat skydd](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din SQL Server. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och tillhandahåller åtgärds säkerhets aviseringar om avvikande databas åtkomst mönster. De här aviseringarna ger information om misstänkta aktiviteter och rekommenderade åtgärder för att undersöka och minimera hotet.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Kom igång med avancerad data säkerhet för SQL på virtuella Azure-datorer

Följande steg hjälper dig att komma igång med avancerad data säkerhet för SQL på Azure VM: ar offentlig för hands version.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Konfigurera avancerad data säkerhet för SQL på virtuella Azure-datorer

Aktivera avancerad data säkerhet för SQL-servrar på Virtual Machines på prenumerations-/arbets ytans nivå:
 
1. Från Security Centerens marginal List öppnar du sidan **Inställningar för pris &** .

1. Välj den prenumeration eller arbets yta för vilken du vill aktivera avancerad data säkerhet för SQL på virtuella Azure-datorer.

1. Växla alternativet för **SQL-servrar på virtuell dator (för hands version)** till aktive rad. 

    (Klicka på skärm bilden för att expandera)

    [![Security Center rekommendationer och aviseringar som visas i administrations Center för Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Avancerad data säkerhet för SQL-servrar kommer att aktive ras på alla SQL-servrar som är anslutna till den valda arbets ytan eller standard arbets ytan för den valda prenumerationen.

    >[!NOTE]
    > Lösningen är helt aktiv efter den första omstarten av SQL Server. 

Skapa en ny arbets yta genom att följa anvisningarna i [skapa en Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Du ansluter SQL Servers värd till en arbets yta genom att följa anvisningarna i [Anslut Windows-datorer till Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-atp-alerts"></a>Konfigurera e-postavisering om ATP-aviseringar 

Du kan ange en lista över mottagare som ska få ett e-postmeddelande när Security Center aviseringar genereras. E-postmeddelandet innehåller en direkt länk till aviseringen i Azure Security Center med all relevant information. 

1. Gå till **Security Center** > **prissättnings & inställningar** och klicka på den relevanta prenumerationen

    ![Prenumerations inställningar](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Klicka på **e-postmeddelanden**på menyn Inställningar. 
1. I text rutan **e-postadress** anger du de e-postadresser som ska ta emot meddelandena. Du kan ange mer än en e-postadress genom att avgränsa e-postadresserna med kommatecken (,).  Till exempel admin1@mycompany.comadmin2@mycompany.comadmin3@mycompany.com

    ![E-postinställningar](./media/security-center-advanced-iaas-data/email-settings.png)

1. I inställningar för **e-postavisering** anger du följande alternativ:
  
    * **Skicka e-postmeddelande för aviseringar med hög allvarlighets grad**: i stället för att skicka e-postmeddelanden för alla aviseringar skickar du bara för aviseringar med hög allvarlighets grad.
    * **Skicka också e-postmeddelanden till prenumerations ägare**: skicka meddelanden till prenumerations ägarna.

1. Klicka på **Spara**överst på skärmen med **e-postaviseringar** .

  > [!NOTE]
  > Se till att klicka på **Spara** innan du stänger fönstret, annars sparas inte de nya inställningarna för **e-postaviseringar** .

## <a name="explore-vulnerability-assessment-reports"></a>Utforska rapporter om sårbarhets bedömning

Instrument panelen för sårbarhets bedömning ger en översikt över dina utvärderings resultat över alla dina databaser. Du kan visa distributionen av databaser enligt SQL Server version, tillsammans med en sammanfattning av misslyckade jämfört med att skicka databaser och en översikt över misslyckade kontroller efter risk distribution.

Du kan visa resultaten för sårbarhets bedömning direkt från Security Center.

1. Från Security Centerens marginal List, under resurs säkerhets HYGIEN, väljer du **Data & lagring**.

1. Välj **säkerhets risker för SQL-databaser i virtuella datorer som ska åtgärdas (för hands version)** . Mer information finns i [Security Center rekommendationer](security-center-recommendations.md). 

    [![* * säkerhets risker på dina SQL-databaser i virtuella datorer bör åtgärdas (för hands version) * * rekommendation](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Den detaljerade vyn för den här rekommendationen visas.

    [![detaljerad vy för * *-sårbarheter på dina SQL-databaser i virtuella datorer bör åtgärdas (för hands version) * * rekommendation](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. För att öka detalj nivån för mer information:

    * En översikt över scannade resurser (databaser) och en lista över säkerhets kontroller som har testats får du genom att klicka på Server.
    [![sårbarheter grupperade efter SQL Server](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * En översikt över sårbarheter grupperade efter en speciell SQL-databas får du genom att klicka på databasens intresse.
    [![sårbarheter grupperade efter SQL Server](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    I varje vy sorteras säkerhets kontrollerna efter **allvarlighets grad**. Klicka på en säkerhets kontroll för att visa ett informations fönster med en **Beskrivning**, hur du **åtgärdar** den och annan relaterad information som **påverkan** eller **benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Avancerat skydd för SQL-servrar i Azure VM-aviseringar
Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja SQL-servrar. Dessa händelser kan utlösa följande aviseringar:

### <a name="anomalous-access-pattern-alerts-preview"></a>Varningar om avvikande åtkomst mönster (förhands granskning)

* **Åtkomst från ovanlig plats:** Den här aviseringen utlöses när åtkomst mönstret ändras till SQL Server, där någon har loggat in på SQL-servern från en ovanlig geografisk plats. Möjliga orsaker:
    * En angripare eller tidigare skadlig anslutning har till gång till din SQL Server.
    * En legitim användare har använt din SQL Server från en ny plats.
* **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. Möjliga orsaker:
    * En angripare försöker att göra en överträdelse av SQL med vanliga angrepps verktyg.
    * En legitim inträngande testning i praktiken.
* **Access from unfamiliar principal** (Åtkomst från ovanligt huvudkonto): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern med ett ovanligt huvudkonto (SQL-användare). Möjliga orsaker:
    * En angripare eller tidigare skadlig anslutning har till gång till din SQL Server. 
    * En legitim användare har använt din SQL Server från med ett nytt huvud konto.
* **Brute force SQL credentials** (Nyckelsökningsattack mot SQL-autentiseringsuppgifter): Den här aviseringen utlöses när det sker ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. Möjliga orsaker:
    * En angripare försöker få en överträdelse av SQL med brute force.
    * En legitim inträngande testning i praktiken.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Möjliga SQL-injektering-attacker (stöds i SQL Server 2019)

* **Sårbarhet för SQL-inmatning**: den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Aviseringen kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Möjliga orsaker:
    * Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
    * Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
* **Potential SQL injection** (Potentiell SQL-inmatning): Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Osäkert kommando (stöds i SQL Server 2019)

* **Potentiellt osäker åtgärd**: den här aviseringen utlöses när ett starkt privilegierat och eventuellt osäkert kommando körs. Möjliga orsaker:
    * Kommandot som rekommenderas att inaktive ras för bättre säkerhet position är aktiverat.
    * En angripare försöker utnyttja SQL-åtkomst eller eskalera privilegier.   


## <a name="explore-and-investigate-security-alerts"></a>Utforska och undersök säkerhets aviseringar

Dina data säkerhets aviseringar är tillgängliga i Security Center sidan aviseringar, på resursens säkerhets flik eller via den direkta länken i e-postaviseringarna.

1. Så här visar du aviseringar:

    * I Security Center klickar du på **säkerhets aviseringar** från sid panelen och väljer en avisering.
    * I resurs omfånget – öppna den relevanta resurs sidan och klicka på **säkerhet**från sido panelen. 

1. Aviseringar är utformade för att vara fristående, med detaljerade åtgärds steg och information om undersökningar i var och en. Du kan undersöka ytterligare med hjälp av andra Azure Security Center-och Azure Sentinel-funktioner för en bredare vy:

    * Aktivera SQL Server gransknings funktion för ytterligare undersökningar. Om du är en Azure Sentinel-användare kan du överföra SQL-gransknings loggarna från Windows säkerhets logg händelser till kontroll och få en omfattande gransknings upplevelse.
    * Använd Security Center rekommendationer för värddatorn som anges i varje avisering för att förbättra din säkerhets position. Detta minskar risken för framtida attacker. 


## <a name="next-steps"></a>Nästa steg

Information om relaterade material finns i följande artikel:

- [Så här åtgärdar du rekommendationer](security-center-remediate-recommendations.md)