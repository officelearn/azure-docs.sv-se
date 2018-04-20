---
title: Rotera inloggningsuppgifterna i Azure Stream Analytics-jobb
description: Den här artikeln beskriver hur du uppdaterar autentiseringsuppgifterna för indata och utdata egenskaperna i Azure Stream Analytics jobb.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: b49b4fecb6be70987e7e6736d78f224c03f719bf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Rotera inloggningsuppgifterna för indata och utdata för ett Stream Analytics-jobb

När du återskapar autentiseringsuppgifter för ett indata eller utdata för ett Stream Analytics-jobb bör du uppdatera jobbet med nya autentiseringsuppgifter. Du måste stoppa jobbet innan du uppdaterar autentiseringsuppgifterna, du kan inte ersätta autentiseringsuppgifterna medan jobbet körs. Om du vill minska fördröjningen mellan stoppa och starta om jobbet, Stream Analytics stöder återuppta ett jobb från senaste utdata. Det här avsnittet beskrivs processen för att rotera inloggningsuppgifterna och starta om jobbet med nya autentiseringsuppgifter.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Återskapa nya autentiseringsuppgifter och uppdatera ditt jobb med de nya autentiseringsuppgifterna 

I det här avsnittet går du igenom återskapar autentiseringsuppgifter för Blob Storage, Event Hubs, SQL-databas och Table Storage. 

### <a name="blob-storagetable-storage"></a>BLOB storage/tabellagring
1. Logga in på Azure-portalen > Bläddra storage-konto som du använde för indata/utdata för Stream Analytics-jobbet.    
2. Öppna från avsnittet inställningar **åtkomstnycklar**. Välj det alternativ som inte används av ditt jobb och återskapa den mellan två standardnycklar (key1, key2):  
   ![Återskapa nycklar för lagringskonto](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Kopiera den nya nyckeln.    
4. Bläddra Stream Analytics-jobbet från Azure-portalen > Välj **stoppa** och vänta på att stoppa jobbet.    
5. Hitta Blob/Table storage in-/ utdata som du vill uppdatera autentiseringsuppgifterna.    
6. Hitta de **Lagringskontonyckel** fältet och klistra in den nyligen skapade nyckeln > klickar du på **spara**.    
7. En anslutningstest startar automatiskt när du sparar ändringarna kan du visa den från fliken aviseringar. Det finns två meddelanden en motsvarar sparar uppdateringen och andra motsvarar testar anslutningen:  
   ![Meddelanden när du har redigerat nyckeln](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. Gå vidare till [starta jobbet från den senaste stopptid] (#start-your-job-from-the-last-stopped-time) avsnitt.

### <a name="event-hubs"></a>Händelsehubbar

1. Logga in på Azure-portalen > Bläddra Händelsehubben som du använde för indata/utdata för Stream Analytics-jobbet.    
2. Öppna från avsnittet inställningar **principer för delad åtkomst** och välj den obligatoriska åtkomstprincipen. Mellan den **primärnyckel** och **sekundärnyckeln**, Välj det alternativ som inte används av ditt jobb och återskapa det:  
   ![Återskapa nycklar för Event Hub](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Kopiera den nya nyckeln.    
4. Bläddra Stream Analytics-jobbet från Azure-portalen > Välj **stoppa** och vänta på att stoppa jobbet.    
5. Leta upp händelsen hubbar in-/ utdata som du vill uppdatera autentiseringsuppgifterna.    
6. Hitta de **Event Hub princip nyckeln** fältet och klistra in den nyligen skapade nyckeln > klickar du på **spara**.    
7. En anslutningstest startar automatiskt när du sparar ändringarna, se till att den har passerat.    
8. Gå vidare till [starta jobbet från senast stoppad](#start-your-job-from-the-last-stopped-time) avsnitt.

### <a name="sql-database"></a>SQL Database

Du måste ansluta till SQL-databasen för att uppdatera inloggningsuppgifterna för en befintlig användare. Du kan uppdatera autentiseringsuppgifterna med hjälp av Azure-portalen eller ett klientsidan verktyg som SQL Server Management Studio. Det här avsnittet beskrivs processen för att uppdatera autentiseringsuppgifterna med hjälp av Azure-portalen.

1. Logga in på Azure-portalen > Bläddra SQL-databasen som du använde som utdata för Stream Analytics-jobbet.    
2. Från **Data explorer**, inloggning/ansluta till din databas > Välj typ av auktorisering som **SQL server-autentisering** > Skriv i din **inloggning** och  **Lösenordet** information > Välj **Ok**.  
   ![Återskapa autentiseringsuppgifterna för SQL-databas](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. I fliken fråga kan du ändra lösenordet för en av dina användare genom att köra följande fråga (Ersätt `<user_name>` med ditt användarnamn och `<new_password>` med ditt nya lösenord):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Anteckna det nya lösenordet.    
5. Bläddra Stream Analytics-jobbet från Azure-portalen > Välj **stoppa** och vänta på att stoppa jobbet.    
6. Leta reda på SQL-databas utdata som du vill rotera autentiseringsuppgifter. Uppdatera lösenordet och spara ändringarna.    
7. En anslutningstest startar automatiskt när du sparar ändringarna, se till att den har passerat.    
8. Gå vidare till [starta jobbet från senast stoppad](#start-your-job-from-the-last-stopped-time) avsnitt.

### <a name="power-bi"></a>Power BI
1. Logga in på Azure-portalen > Bläddra Stream Analytics-jobbet > Välj **stoppa** och vänta på att stoppa jobbet.    
2. Leta upp Power BI-utdata som du vill förnya autentiseringsuppgifter > klickar du på den **förnya auktorisering** (du bör se ett meddelande) > **spara** ändringarna.    
3. En anslutningstest startar automatiskt när du sparar ändringarna, se till att den passerat har.    
4. Gå vidare till [starta jobbet från senast stoppad](#start-your-job-from-the-last-stopped-time) avsnitt.

## <a name="start-your-job-from-the-last-stopped-time"></a>Starta ditt jobb från senast stoppad

1. Navigera till jobbets **översikt** fönstret > Välj **starta** att starta jobbet.    
2. Välj **när senast stoppad** > klickar du på **starta**. Observera att alternativet ”när senast stoppad” visas endast om du tidigare körde jobbet och har några utdata genereras. Jobbet startas baserat på det sista utdata värdet tiden.
   ![Starta jobbet](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
