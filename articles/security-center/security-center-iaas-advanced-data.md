---
title: Avancerad datasäkerhet för IaaS i Azure Security Center | Microsoft Docs
description: " Lär dig mer om att aktivera avancerad säkerhet för IaaS i Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2019
ms.author: monhaber
ms.openlocfilehash: e601bbaa0d15078fc2b19b5b7c536e3a1f6d20ad
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442754"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>Avancerad säkerhet för SQL-servrar på IaaS
Avancerad säkerhet för SQL-servrar på IaaS är en enhetlig paket för avancerade funktioner för SQL-säkerhet. Den innehåller för närvarande funktioner för lyfta upp och åtgärda säkerhetsrisker i databasen och identifiera avvikande aktiviteter som kan tyda på ett hot mot din databas.

Den här säkerhetsuppdateringen erbjudande för IaaS SQL-servrar är baserad på samma grundläggande teknik som används i den [avancerade datasäkerhet för Azure SQL Database-paketet](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Översikt

Avancerade datasäkerhet (AD) innehåller en uppsättning avancerade säkerhetsfunktioner för SQL, som består av utvärdering av säkerhetsrisker och Avancerat skydd.

* [Sårbarhetsbedömning](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) är ett enkelt sätt att konfigurera tjänsten som kan upptäcka, spåra och hjälper dig att åtgärda säkerhetsrisker i databasen. Den ger insyn i ditt säkerhetsläge och innehåller steg för att lösa säkerhetsproblem och förbättra din databas fortifications.
* [Avancerat skydd](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja SQL-servern. Ständigt övervakar din databas för misstänkta aktiviteter och innehåller åtgärdsorienterade säkerhetsaviseringar om avvikande mönster i databasåtkomst. Dessa aviseringar ger information om misstänkt aktivitet och rekommenderade åtgärder för att undersöka och åtgärda hot.

## <a name="get-started-with-ads-for-iaas"></a>Kom igång med ANNONSER för IaaS

Följande steg hjälper dig igång med ANNONSER för IaaS.

### <a name="set-up-ads-for-iaas"></a>Konfigurera ANNONSER för IaaS

**Innan du börjar**: Du behöver en Log Analytics-arbetsyta för att lagra säkerhetsloggar som analyseras. Om du inte har någon så du kan skapa ett enkelt, enligt beskrivningen i [skapa en Log Analytics-arbetsyta i Azure-portalen](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Anslut den virtuella datorn som är värd för SQLServer till Log Analytics-arbetsytan. Anvisningar finns i [ansluta Windows-datorer till Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Från Azure Marketplace, går du till den [SQL Advanced Data Security-lösningen](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Du kan hitta den med hjälp av alternativet marketplace-sökning som finns i följande bild.) Den **SQL Advanced datasäkerhet** öppnas.

    ![Avancerad säkerhet för IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Klicka på **Skapa**. Arbetsplatsen visas.

    ![Skapa avancerade datasäkerhet](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Välj arbetsytan du använder och klicka på **skapa**.

   ![Välj arbetsyta](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Starta om den [Virtuella datorns SQLServer](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Utforska och undersöka säkerhetsaviseringar

Du kan visa och hantera din aktuella säkerhetsaviseringar.

1. Klicka på **Security Center** > **säkerhetsaviseringar**, och klicka på en avisering.

    ![Hitta aviseringen](./media/security-center-advanced-iaas-data/find-alert.png)

1. Från den **angripna resursen** kolumn, klicka på en resurs som har attackerats.

1. Om du vill visa detaljerad information om aviseringen och åtgärder för att undersöka den aktuella hot och framtida hot-adressering, rulla nedåt i **allmän information** sidan, och i den **åtgärdssteg** klickar du på  **UNDERSÖKNINGSSTEG** länk.

    ![Reparationssteg](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Om du vill visa loggfilerna som är associerade med en utlöst varningen kan du gå till **Log analytics-arbetsytor** och utför följande steg:

     > [!NOTE]
     > Om **Log analytics-arbetsytor** inte visas på den vänstra menyn, klickar du på **alla tjänster**, och Sök efter **Log analytics-arbetsytor**.

    1. Tänk på kolumnerna som visas i **prisnivå** och **WorkspaceID** kolumner. (**Log analytics-arbetsytor** > **Redigera kolumner**, lägga till **prisnivå** och **WorkspaceID**.)

     ![Redigera kolumner](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Klicka på den arbetsyta som har aviseringen loggar.

    1. Under den **Allmänt** -menyn klickar du på **loggar**

    1. Klicka på ögat bredvid **SQLAdvancedThreatProtection** tabell. Loggarna visas.

     ![Visa loggar](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Konfigurera e-postmeddelande om ATP-aviseringar 

Du kan ange en lista över mottagare som tar emot ett e-postmeddelande när ASC aviseringar genereras. E-postmeddelandet innehåller en direktlänk till aviseringen i Azure Security Center med all relevant information. 

1. Gå till **Security Center** > **säkerhetsprincip** och på raden i den relevanta prenumeration klickar du på **redigera inställningar >**.

    ![Prenumerationsinställningar](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Från den **inställningar** menyn klickar du på **e-postmeddelanden**. 
1. I den **e-postadress** text anger du e-postadresser för att ta emot meddelanden. Du kan ange fler än en e-postadress genom att avgränsa e-postadresser med kommatecken (,).  Till exempel admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![E-postinställningar](./media/security-center-advanced-iaas-data/email-settings.png)

1. I den **e-postavisering** inställningar måste ställa in följande alternativ:
  
    * **Skicka e-postmeddelande för aviseringar med hög allvarlighetsgrad**: I stället för att skicka e-post för alla aviseringar, skicka endast för varningar med hög angelägenhetsgrad.
    * **Även skicka e-postmeddelanden till prenumerationsägarna**:  Skicka meddelanden till prenumerationer ägare för.

1. Längst upp på den **e-postmeddelanden** klickar du på **spara**.

  > [!NOTE]
  > Se till att klicka på **spara** innan du stänger fönstret eller nya **e-postavisering** kommer inte att spara inställningarna.

## <a name="explore-vulnerability-assessment-reports"></a>Utforska Vulnerability Assessment Reports

Vulnerability assessment instrumentpanelen innehåller en översikt över utvärderingsresultatet över alla dina databaser. Du kan visa autentiseringstrafikens databaser enligt version av SQL Server, tillsammans med en sammanfattning av misslyckas och skicka databaser och en översiktlig sammanfattning av misslyckade kontroller enligt risk distribution.

Du kan visa dina Vulnerability assessment resultat och rapporter direkt från Log Analytics.

1. Gå till Log Analytics-arbetsytan med ADS-lösningen.
1. Gå till **lösningar** och välj den **Sårbarhetsbedömning för SQL** lösning.
1. I den **sammanfattning** fönstret klickar du på **visa sammanfattning** och välj din **SQL Sårbarhetsbedömningsrapport**.

    ![SQL-utvärderingsrapport](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Rapporten instrumentpanelen har lästs in. Se till att tidsperioden anges till minst den **senaste 7 dagarna** eftersom utvärderingen sårbarhetsgenomsökningar körs på dina databaser på ett fast schema för en gång per 7 dagar.

    ![Ange senaste 7 dagarna](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Om du vill granska nedåt för mer information klickar du på ett instrumentpanelselement. Exempel:

   1. Klicka på kryssrutan säkerhetsproblem i den **misslyckades kontrollerar sammanfattning** avsnitt för att visa en Log Analytics-tabell med resultat för den här kontrollen över alla databaser. De som har resultat visas först.

   1. Klicka på genom för att visa detaljer för varje säkerhetsproblem, inklusive säkerhetsproblem beskrivning och påverkan, status, risker och de faktiska resultaten för den här databasen. Du kan också se den faktiska frågan som kördes för att utföra den här kontrollen, och information om åtgärder för att lösa problemet.

    ![Välj arbetsyta](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Välj arbetsyta](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Du kan köra alla Log Analytics-frågor på dina data för utvärdering av säkerhetsrisker resultat, statistikforskning data efter dina behov.

## <a name="advanced-threat-protection-for-sql-servers-on-iaas-alerts"></a>Avancerat skydd för SQL-servrar på IaaS-aviseringar
Aviseringarna genereras av onormala och potentiellt skadliga försök att komma åt eller utnyttja SQL-servrar. Dessa händelser kan utlösa följande aviseringar:

### <a name="anomalous-access-pattern-alerts"></a>Avvikande åtkomst mönstret aviseringar

* **Åtkomst från ovanlig plats:** Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till SQLServer, där någon har loggat in till SQLServer från en ovanlig geografisk plats. Möjliga orsaker:
     * En angripare eller f.d. skadliga utsträckning har åtkomst till din SQL-Server.
     * En legitim användare har åtkomst till din SQL-Server från en ny plats.
* **Åtkomst från ett potentiellt skadligt program**: hans aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. Möjliga orsaker:
     * En angripare som försöker att bryta mot din SQL med vanliga angreppsverktyg.
     * En legitim pågående intrångstestning.
* **Åtkomst från okänd huvudnamn**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret till SQLServer, där någon har loggat in till SQLServer med ett ovanligt huvudkonto (SQL-användare). Möjliga orsaker:
     * En angripare eller f.d. skadliga utsträckning har åtkomst till din SQL-Server. 
     * En legitim användare har åtkomst till din SQL Server med ett nytt huvudnamn.
* **Brute force SQL autentiseringsuppgifter**: Den här aviseringen utlöses när det finns ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. Möjliga orsaker:
     * En angripare som försöker att bryta mot din SQL med brute force.
     * En legitim pågående intrångstestning.

### <a name="potential-sql-injection-attacks-coming"></a>Potentiell SQL-inmatningsattacker (komma)

* **Sårbarhet för SQL-inmatning**: Den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Aviseringen kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Möjliga orsaker:
     * Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
     * Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
* **Potentiell SQL-inmatning**: Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart sårbarhet för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.
