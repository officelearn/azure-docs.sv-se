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
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: 287da68617a9527bc398df577cf8d10773fa8557
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202167"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Avancerad data säkerhet för SQL-servrar på Azure Virtual Machines (offentlig för hands version)
Avancerad data säkerhet för SQL-servrar på Azure Virtual Machines är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den för närvarande (i offentlig för hands version) innehåller funktioner för Visa och åtgärder för att åtgärda eventuella sårbarheter i databasen och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen. 

Det här säkerhets erbjudandet för virtuella Azure-datorer med SQL-servrar baseras på samma grundläggande teknik som används i [Azure SQL Database Advanced Data Security-paketet](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Översikt

Avancerad data säkerhet ger en uppsättning avancerade SQL-säkerhetsfunktioner, som består av sårbarhets bedömning och Avancerat skydd.

* [Sårbarhets bedömning](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) är ett enkelt sätt att konfigurera en tjänst som kan upptäcka, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Den ger insyn i ditt säkerhets tillstånd och innehåller stegen för att lösa säkerhets problem och förbättra din databas Fortifications.
* [Avancerat skydd](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din SQL Server. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och tillhandahåller åtgärds säkerhets aviseringar om avvikande databas åtkomst mönster. De här aviseringarna ger information om misstänkta aktiviteter och rekommenderade åtgärder för att undersöka och minimera hotet.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Kom igång med avancerad data säkerhet för SQL på virtuella Azure-datorer

Följande steg hjälper dig att komma igång med avancerad data säkerhet för SQL på Azure VM: ar offentlig för hands version.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Konfigurera avancerad data säkerhet för SQL på virtuella Azure-datorer

**Innan du börjar**: Du behöver en Log Analytics arbets yta för att lagra de säkerhets loggar som analyseras. Om du inte har något kan du skapa ett enkelt, enligt beskrivningen i [skapa en Log Analytics arbets yta i Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Anslut den virtuella datorn som är värd för SQL-servern till Log Analytics arbets ytan. Instruktioner finns i [ansluta Windows-datorer till Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Från Azure Marketplace går du till [SQL-lösningen för avancerad data säkerhet](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Du hittar det med hjälp av Sök alternativet i Marketplace, som du ser i följande bild.) Sidan **SQL avancerad data säkerhet** öppnas.

    ![Avancerad data säkerhet för IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Klicka på **Skapa**. Arbets platserna visas.

    ![Avancerad data säkerhet skapa](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Välj arbets ytan som du vill använda och klicka på **skapa**.

   ![Välj arbetsyta](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Starta om den [virtuella datorns SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Utforska och undersök säkerhets aviseringar

Du kan visa och hantera dina aktuella säkerhets aviseringar.

1. Klicka på **Security Center** > **säkerhets aviseringar**och klicka på en avisering.

    ![Sök avisering](./media/security-center-advanced-iaas-data/find-alert.png)

1. I kolumnen **attackerad resurs** klickar du på en resurs som har attacker ATS.

1. Om du vill visa information om aviseringar och åtgärder för att undersöka det aktuella hotet och åtgärda framtida hot, bläddrar du ned sidan **allmän information** och klickar på länken gransknings steg i avsnittet **reparations steg** .

    ![Reparationssteg](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Om du vill visa de loggar som är associerade med aktivering av aviseringen går du till **Log Analytics-arbetsytor** och utför följande steg:

     > [!NOTE]
     > Om **Log Analytics-arbetsytor** inte visas i den vänstra menyn klickar du på **alla tjänster**och söker efter **Log Analytics-arbetsytor**.

    1. Se till att kolumnerna visar **pris nivån** och **WorkspaceID** kolumner. (**Log Analytics-arbetsytor** > **Redigera kolumner**, Lägg till **pris nivå** och **WorkspaceID**.)

     ![Redigera kolumner](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Klicka på arbets ytan som innehåller aviserings loggarna.

    1. Under menyn **Allmänt** klickar du på **loggar**

    1. Klicka på ögat bredvid **SQLAdvancedThreatProtection** -tabellen. Loggarna visas.

     ![Visa loggar](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Konfigurera e-postavisering om ATP-aviseringar 

Du kan ange en lista över mottagare som ska ta emot ett e-postmeddelande när ASC-aviseringar genereras. E-postmeddelandet innehåller en direkt länk till aviseringen i Azure Security Center med all relevant information. 

1. Gå till **Security Center** > **priser & inställningar** och klicka på den aktuella prenumerationen

    ![Prenumerations inställningar](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Klicka på **e-postmeddelanden**på menyn **Inställningar** . 
1. I text rutan **e-postadress** anger du de e-postadresser som ska ta emot meddelandena. Du kan ange mer än en e-postadress genom att avgränsa e-postadresserna med kommatecken (,).  Till exempel admin1@mycompany.com,admin2@mycompany.comadmin3@mycompany.com

      ![E-postinställningar](./media/security-center-advanced-iaas-data/email-settings.png)

1. I inställningar för **e-postavisering** anger du följande alternativ:
  
    * **Skicka e-postmeddelande för aviseringar med hög allvarlighets grad**: I stället för att skicka e-postmeddelanden för alla aviseringar skickar du bara för aviseringar med hög allvarlighets grad.
    * **Skicka också e-postmeddelanden till prenumerations ägare**:  Skicka meddelanden till prenumerations ägarna också.

1. Klicka på **Spara**överst på skärmen med **e-** postaviseringar.

  > [!NOTE]
  > Se till att klicka på **Spara** innan du stänger fönstret, annars sparas inte de nya inställningarna för **e-** postaviseringar.

## <a name="explore-vulnerability-assessment-reports"></a>Utforska rapporter om sårbarhets bedömning

Instrument panelen för sårbarhets bedömning ger en översikt över dina utvärderings resultat över alla dina databaser. Du kan visa distributionen av databaser enligt SQL Server version, tillsammans med en sammanfattning av misslyckade jämfört med att skicka databaser och en översikt över misslyckade kontroller efter risk distribution.

Du kan visa dina resultat och rapporter för sårbarhets bedömning direkt från Log Analytics.

1. Navigera till din Log Analytics arbets yta med avancerad data säkerhets lösning.
1. Navigera till **lösningar** och välj lösningen **SQL sårbarhets bedömning** .
1. I fönstret **Sammanfattning** klickar du på **Visa sammanfattning** och väljer din **SQL sårbarhet Assessment-rapport**.

    ![SQL-utvärdering rapport](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Rapportens instrument panel laddas. Se till att tidsfönstret är inställt på minst **7 dagar** sedan genomsökningar efter sårbarhets bedömning körs på dina databaser enligt ett fast schema på en gång per 7 dagar.

    ![Ange de senaste 7 dagarna](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Klicka på någon av instrument panels elementen för att öka detalj nivån. Exempel:

   1. Klicka på en sårbarhets kontroll i avsnittet **Översikt över misslyckade kontroller** om du vill visa en Log Analytics tabell med resultatet av den här kontrollen i alla databaser. De som har resultat visas först.

   1. Klicka sedan på genom att visa information om varje sårbarhet, inklusive sårbarhets beskrivning och effekt, status, associerad risk och faktiska resultat för den här databasen. Du kan också se den faktiska frågan som kördes för att utföra den här kontrollen och reparera information för att lösa problemet.

    ![Välj arbetsyta](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Välj arbetsyta](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Du kan köra alla Log Analytics frågor om resultat data för sårbarhets bedömning för att segmentera och sortera data efter dina behov.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Avancerat skydd för SQL-servrar i Azure VM-aviseringar
Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja SQL-servrar. Dessa händelser kan utlösa följande aviseringar:

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Varningar om avvikande åtkomst mönster (stöds i offentlig för hands version)

* **Åtkomst från ovanlig plats:** Den här aviseringen utlöses när åtkomst mönstret ändras till SQL Server, där någon har loggat in på SQL-servern från en ovanlig geografisk plats. Möjliga orsaker:
     * En angripare eller tidigare skadlig anslutning har till gång till din SQL Server.
     * En legitim användare har använt din SQL Server från en ny plats.
* **Åtkomst från ett potentiellt skadligt program**: hans varning utlöses när ett potentiellt skadligt program används för att komma åt databasen. Möjliga orsaker:
     * En angripare försöker att göra en överträdelse av SQL med vanliga angrepps verktyg.
     * En legitim inträngande testning i praktiken.
* **Åtkomst från okända huvud objekt**: Den här aviseringen utlöses när åtkomst mönstret ändras till SQL Server, där någon har loggat in på SQL-servern med en ovanlig huvud server (SQL-användare). Möjliga orsaker:
     * En angripare eller tidigare skadlig anslutning har till gång till din SQL Server. 
     * En legitim användare har använt din SQL Server från med ett nytt huvud konto.
* **Brute Force SQL-autentiseringsuppgifter**: Den här aviseringen utlöses när det finns ett onormalt stort antal misslyckade inloggningar med andra autentiseringsuppgifter. Möjliga orsaker:
     * En angripare försöker få en överträdelse av SQL med brute force.
     * En legitim inträngande testning i praktiken.

### <a name="potential-sql-injection-attacks-coming"></a>Potentiella SQL-injektering-attacker (kommer)

* **Sårbarhet för SQL-inmatning**: Den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Aviseringen kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Möjliga orsaker:
     * Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
     * Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
* **Potentiell SQL-inmatning**: Den här aviseringen utlöses när en aktiv sårbarhet sker mot en identifierad program sårbarhet för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.
