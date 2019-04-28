---
title: Rotera autentiseringsuppgifter för inloggning i Azure Stream Analytics-jobb
description: Den här artikeln beskriver hur du uppdaterar autentiseringsuppgifterna för indata och utdata egenskaperna i Azure Stream Analytics jobb.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 14e24c1e9a61eb7ea73a949e17ffbf8c5b768f05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479664"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Rotera autentiseringsuppgifter för inloggning för indata och utdata för ett Stream Analytics-jobb

När du återskapar autentiseringsuppgifter för ett indata eller utdata för ett Stream Analytics-jobb kan bör du uppdatera jobbet med nya autentiseringsuppgifter. Du måste stoppa jobbet innan du uppdaterar autentiseringsuppgifterna, autentiseringsuppgifterna kan inte byta ut medan jobbet körs. För att minska fördröjningen mellan stoppa och starta om jobbet, Stream Analytics stöder återuppta ett jobb från dess senaste utdata. Det här avsnittet beskriver processen för att rotera autentiseringsuppgifter för inloggning och starta om jobbet med nya autentiseringsuppgifter.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Återskapa nya autentiseringsuppgifter och uppdatera ditt jobb med nya autentiseringsuppgifter 

I det här avsnittet ska vägleder vi dig genom återskapats autentiseringsuppgifter för Blob Storage, Event Hubs, SQL-databas och Table Storage. 

### <a name="blob-storagetable-storage"></a>BLOB storage-/ tabellagring
1. Logga in på Azure portal > Bläddra storage-konto som du använde för indata/utdata för Stream Analytics-jobbet.    
2. Öppna från avsnittet inställningar **åtkomstnycklar**. Välj det som inte används av jobbet och återskapa den mellan två standardnycklar (key1, key2):  
   ![Återskapa nycklar för lagringskonto](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Kopiera den nya nyckeln.    
4. Bläddra ditt Stream Analytics-jobb i Azure Portal > Välj **stoppa** och vänta tills jobbet stoppa.    
5. Leta upp Blob/Table storage indata/utdata som du vill uppdatera autentiseringsuppgifterna.    
6. Hitta den **Lagringskontonyckel** fältet och klistra in din nyligen genererade nyckel > klickar du på **spara**.    
7. En anslutningstest startar automatiskt när du sparar ändringarna, kan du visa den från fliken aviseringar. Det finns två meddelanden en motsvarar sparar uppdateringen och andra motsvarar testar anslutningen:  
   ![Meddelanden när du har redigerat nyckeln](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Gå vidare till [startar ditt jobb än senast stoppad](#start-your-job-from-the-last-stopped-time) avsnittet.

### <a name="event-hubs"></a>Händelsehubbar

1. Logga in på Azure portal > Bläddra Händelsehubben som du använde för indata/utdata för Stream Analytics-jobbet.    
2. Öppna från avsnittet inställningar **principer för delad åtkomst** och markerar principen som kräver åtkomst. Mellan den **primärnyckel** och **sekundärnyckel**, Välj det som inte används av jobbet och återskapa den:  
   ![Återskapa nycklar för Event Hubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Kopiera den nya nyckeln.    
4. Bläddra ditt Stream Analytics-jobb i Azure Portal > Välj **stoppa** och vänta tills jobbet stoppa.    
5. Leta upp Event hubs indata/utdata som du vill uppdatera autentiseringsuppgifterna.    
6. Hitta den **principnyckel för Event Hub** fältet och klistra in din nyligen genererade nyckel > klickar du på **spara**.    
7. En anslutningstest startar automatiskt när du sparar ändringarna, se till att det har har passerat.    
8. Gå vidare till [startar ditt jobb än senast stoppad](#start-your-job-from-the-last-stopped-time) avsnittet.

### <a name="sql-database"></a>SQL Database

Du behöver ansluta till SQL-databasen för att uppdatera autentiseringsuppgifterna för en befintlig användare. Du kan uppdatera autentiseringsuppgifterna med hjälp av Azure-portalen eller ett klientsidan verktyg som SQL Server Management Studio. Det här avsnittet beskrivs processen för att uppdatera autentiseringsuppgifterna med hjälp av Azure-portalen.

1. Logga in på Azure portal > Bläddra SQL-databasen som du använde som utdata för Stream Analytics-jobbet.    
2. Från **datautforskaren**, inloggning/ansluta till din databas > Välj typ av auktorisering som **SQL server-autentisering** > Skriv din **inloggning** och  **Lösenordet** information > Välj **Ok**.  
   ![Återskapa autentiseringsuppgifterna för SQL-databas](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Ändra lösenordet för en av dina användare genom att köra följande fråga på fliken fråga (Ersätt `<user_name>` med ditt användarnamn och `<new_password>` med ditt nya lösenord):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Anteckna det nya lösenordet.    
5. Bläddra ditt Stream Analytics-jobb i Azure Portal > Välj **stoppa** och vänta tills jobbet stoppa.    
6. Leta upp SQL database-utdata som du vill ska rotera autentiseringsuppgifter. Uppdatera lösenordet och spara ändringarna.    
7. En anslutningstest startar automatiskt när du sparar ändringarna, se till att det har har passerat.    
8. Gå vidare till [startar ditt jobb än senast stoppad](#start-your-job-from-the-last-stopped-time) avsnittet.

### <a name="power-bi"></a>Power BI
1. Logga in på Azure portal > Bläddra ditt Stream Analytics-jobb > Välj **stoppa** och vänta tills jobbet stoppa.    
2. Hitta Power BI-utdata som du vill förnya autentiseringsuppgifter > klickar du på den **förnya auktoriseringen** (du bör se ett meddelande) > **spara** ändringarna.    
3. En anslutningstest startar automatiskt när du sparar ändringarna, se till att det har har passerat.    
4. Gå vidare till [startar ditt jobb än senast stoppad](#start-your-job-from-the-last-stopped-time) avsnittet.

## <a name="start-your-job-from-the-last-stopped-time"></a>Starta ditt jobb från senast stoppad

1. Gå till jobbets **översikt** fönstret > Välj **starta** att starta jobbet.    
2. Välj **senast stoppad** > klickar du på **starta**. Observera att alternativet ”senast stoppad” visas endast om du tidigare körde jobbet och haft några utdata genereras. Jobbet startas baserat på den senaste utdatavärde tid.
   ![Starta Stream Analytics-jobbet](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
