---
title: Hotidentifiering – Azure SQL Data Warehouse | Microsoft Docs
description: Konfigurera hotidentifiering och utforska misstänkta händelser i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 112d093946099e623d1edfa4555eda681069892e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243393"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Hotidentifiering i Azure SQL Data Warehouse
Konfigurera hotidentifiering och utforska misstänkta händelser i Azure SQL Data Warehouse.

## <a name="what-is-threat-detection"></a>Vad är identifiering av hot
Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. 

Hotidentifiering ger ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användare kan utforska misstänkta händelser med hjälp av [Azure SQL Data Warehouse Auditing](sql-data-warehouse-auditing-overview.md) att avgöra om de är följden av ett försök att komma åt, tränga in i eller utnyttja data i datalagret.
Hotidentifiering gör det enkelt att hantera potentiella hot mot informationslagret utan att behöva vara säkerhetsexpert eller hantera avancerade säkerhetsövervakningssystem.

Hotidentifiering identifierar till exempel vissa avvikande databasaktiviteter som kan tyda på försök till SQL-inmatning. SQL-inmatning är ett av de vanligt förekommande säkerhetsproblemen för webbprogram på Internet, vars syfte är att attackera datadrivna program. Angripare kan utnyttja sårbarheter i program för att mata in skadliga SQL-instruktioner i programmets inmatningsfält i syfte att tränga in eller ändra data i databasen.

## <a name="set-up-threat-detection-for-your-database"></a>Konfigurera hotidentifiering för databasen
1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till konfigurationsbladet i SQL Data Warehouse som du vill övervaka. Välj **Granskning och hotidentifiering** på bladet Inställningar.
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. I den **granskning och Hotidentifiering** configuration bladet aktivera **på** granskning, som visar inställningarna för hotidentifiering.
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. Aktivera **på** Hotidentifiering.
5. Konfigurera en lista över e-postmeddelanden som ska få säkerhetsaviseringar när avvikande data warehouse-aktiviteter.
6. Klicka på **spara** i den **granskning och Hotidentifiering** configuration bladet för att spara den nya eller uppdaterade granskning och hotidentifiering princip.
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande data warehouse-aktiviteter när en misstänkt händelse
1. Du får ett e-postavisering när avvikande databasaktiviteter. <br/>
   I e-postmeddelandet får du information om den misstänkta händelsen, inklusive typen av avvikande aktivitet, databasens namn, servernamn och tidpunkt för händelsen. Dessutom ingår information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.<br/>
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. I e-post, klickar du på den **Azure SQL-granskningsloggen** länka, som startar Azure-portalen och visa relevanta granskning poster vid tidpunkten för den misstänkta händelsen.
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Klicka på granskningsposter om du vill visa mer information om misstänkta databasaktiviteter, till exempel SQL-instruktionen, fel felorsak eller ett klient-IP.
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. På bladet med granskningsposter klickar du på **Öppna i Excel** om du vill öppna en förkonfigurerad Excel-mall för att importera och köra djupare analyser av granskningsloggen vid tiden för den misstänkta händelsen.<br/>
   **Obs!** I Excel 2010 eller senare Power Query och **snabb kombinering** inställning är obligatorisk
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. Konfigurera inställningen **Kombinera snabbt** i menyfliksområdet **POWER QUERY** genom att välja **Alternativ** så öppnas alternativdialogrutan. Välj avsnittet Sekretess och välj det andra alternativet ”Ignorera sekretessnivåerna och förbättra eventuellt prestandan”:
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. När du vill läsa in SQL-granskningsloggar kontrollerar du att parametrarna på inställningsfliken är korrekta. Sedan väljer du menyfliken Data och klickar på knappen Uppdatera alla.
   
    ![Navigeringsfönster](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. Resultatet visas på sidan för **SQL-granskningsloggar** och du kan köra analyser av identifierade avvikande händelser och minimera säkerhetshändelsens inverkan på ditt program.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [skydda ett informationslager](sql-data-warehouse-overview-manage-security.md).
