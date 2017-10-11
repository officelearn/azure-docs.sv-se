---
title: "Kom igång med SQL Data Warehouse Hotidentifiering"
description: "Hur du kommer igång med Hotidentifiering"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: f4a2376fe4fb710d031c35ca7fdbf4c7bb0f3caa
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-threat-detection"></a>Kom igång med hotidentifiering
> [!div class="op_single_selector"]
> * [Granskning](sql-data-warehouse-auditing-overview.md)
> * [Hotidentifiering](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>Översikt
Hotidentifiering identifierar avvikande databasaktiviteter som indikerar potentiella säkerhetshot mot databasen. Hotidentifiering är en förhandsversion och stöds för SQL Data Warehouse.

Hotidentifiering innehåller ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter. Användare kan utforska misstänkta händelser med hjälp av [Azure SQL Data Warehouse-granskning](sql-data-warehouse-auditing-overview.md) att avgöra om de härrör från ett försök att komma åt, bryta mot eller utnyttja data i datalagret.
Hotidentifiering gör det enkelt att adressen potentiella hot till datalagret utan att behöva vara en expert säkerhet eller hantera avancerade säkerhetsövervakning system.

Hotidentifiering identifierar till exempel vissa avvikande databasaktiviteter som indikerar potentiella försök med SQL injection. SQL injection är en av säkerhetsproblem för vanliga Web program på Internet, används till att attackera datadrivna program. Angripare kan utnyttja säkerhetsproblem för programmet att mata in skadlig SQL-instruktioner i programmet post fält för brott mot eller ändra data i databasen.

## <a name="set-up-threat-detection-for-your-database"></a>Ställ in hotidentifiering för din databas
1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Navigera till bladet för konfiguration av SQL Data Warehouse som du vill övervaka. I bladet inställningar väljer **granskning och Hotidentifiering**.
   
    ![Navigeringsfönstret][1]
3. I den **granskning och Hotidentifiering** configuration bladet Stäng **ON** granskning, som visar inställningarna för identifiering av hot.
   
    ![Navigeringsfönstret][2]
4. Aktivera **på** Hotidentifiering.
5. Konfigurera en lista över e-postmeddelanden som ska ta emot säkerhetsaviseringar vid identifiering av avvikande data warehouse aktiviteter.
6. Klicka på **spara** i den **Auditing & Threat detection** configuration bladet för att spara den nya eller uppdaterade granskning och hot princip.
   
    ![Navigeringsfönstret][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Utforska data avvikande aktiviteter vid identifiering av en misstänkt aktivitet
1. Du får ett e-postmeddelande vid identifiering av avvikande databasaktiviteter. <br/>
   E-postmeddelandet ger information om händelsen misstänkta säkerhet inklusive avvikande aktiviteter, databasens namn, servernamn och tidpunkt för händelsen. Dessutom ger information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minska den potentiella risken till databasen.<br/>
   
    ![Navigeringsfönstret][4]
2. I e-post, klickar du på den **Azure SQL-granskning logg** länk som startar den klassiska Azure-portalen och visa de granskning register vid ungefär samma tidpunkt misstänkta händelsen.
   
    ![Navigeringsfönstret][5]
3. Klicka på granskningsposter om du vill visa mer information om misstänkt databasaktiviteter, till exempel SQL-instruktionen, fel orsak och klient-IP.
   
    ![Navigeringsfönstret][6]
4. I bladet granskning poster klickar du på **öppna i Excel** för att öppna en förkonfigurerad excel-mall som du vill importera och kör djupare analys av granskningsloggen vid ungefär samma tidpunkt misstänkta händelsen.<br/>
   **Obs:** i Excel 2010 eller senare, Power Query och **snabb kombinera** inställningen är obligatorisk
   
    ![Navigeringsfönstret][7]
5. Så här konfigurerar du den **snabb kombinera** - inställningen i den **POWER QUERY** menyfliksområdet fliken **alternativ** att visa dialogrutan Alternativ. Välj avsnittet sekretess och väljer det andra alternativet - ”Ignorera sekretessnivåerna och förbättra prestanda”:
   
    ![Navigeringsfönstret][8]
6. Kontrollera att parametrarna i inställningar på fliken är korrekt och välj ”uppgifter” menyfliksområdet och klicka på knappen 'Uppdatera alla' för att läsa in SQL-granskningsloggar.
   
    ![Navigeringsfönstret][9]
7. Resultatet visas i den **SQL granskningsloggar** blad som gör det möjligt att köra djupare analys av avvikande aktiviteter som upptäckts och minimera effekten av säkerhetshändelse i ditt program.

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
