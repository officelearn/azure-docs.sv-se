---
title: Rotera inloggningsuppgifter i Azure Stream Analytics-jobb
description: I den här artikeln beskrivs hur du uppdaterar autentiseringsuppgifterna för indata och utdatamottagare i Azure Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3ae639dd7c5a42fc6880240988f0fb2817b09f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425981"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Rotera inloggningsuppgifter för indata och utdata från ett Stream Analytics-jobb

När du återskapar autentiseringsuppgifter för en indata eller utdata från ett Stream Analytics-jobb bör du uppdatera jobbet med nya autentiseringsuppgifter. Du måste stoppa jobbet innan du uppdaterar autentiseringsuppgifterna, du kan inte ersätta autentiseringsuppgifterna medan jobbet körs. Om du vill minska fördröjningen mellan att stoppa och starta om jobbet stöder Stream Analytics att ett jobb återupptas från den senaste utdata. I det här avsnittet beskrivs hur du roterar inloggningsuppgifterna och startar om jobbet med nya autentiseringsuppgifter.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Återskapa nya autentiseringsuppgifter och uppdatera jobbet med de nya autentiseringsuppgifterna 

I det här avsnittet går vi igenom återskapande autentiseringsuppgifter för Blob Storage, Event Hubs, SQL Database och Table Storage. 

### <a name="blob-storagetable-storage"></a>Lagring av bloblagring/tabell
1. Logga in på Azure-portalen > bläddra bland det lagringskonto som du använde som indata/utdata för Stream Analytics-jobbet.    
2. Öppna **Access-nycklar**i inställningsavsnittet . Mellan de två standardnycklarna (key1, key2) väljer du den som inte används av jobbet och återskapar den:  
   ![Återskapa nycklar för lagringskonto](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Kopiera den nygenererade nyckeln.    
4. Bläddra bland ditt Stream Analytics-jobb > välj **Stopp** och vänta på att jobbet ska sluta på Azure-portalen.    
5. Leta reda på den Blob/Table storage in/output som du vill uppdatera autentiseringsuppgifter för.    
6. Leta reda på fältet **Lagringskontonyckel** och klistra in den nygenererade nyckeln > klicka på **Spara**.    
7. Ett anslutningstest startar automatiskt när du sparar ändringarna, du kan visa det från fliken aviseringar. Det finns två anmälningar, en motsvarar att spara uppdateringen och andra motsvarar att testa anslutningen:  
   ![Meddelanden efter redigering av nyckeln](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Fortsätt för att [starta jobbet från det senast stoppade tidsavsnittet.](#start-your-job-from-the-last-stopped-time)

### <a name="event-hubs"></a>Händelsehubbar

1. Logga in på Azure-portalen > bläddra i händelsehubben som du använde som indata/utdata för Stream Analytics-jobbet.    
2. Öppna principer för **delad åtkomst** i inställningsavsnittet och välj den åtkomstprincip som krävs. Mellan **primärnyckeln** och **sekundärnyckeln**väljer du den som inte används av jobbet och återskapar den:  
   ![Återskapa nycklar för eventhubbar](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Kopiera den nygenererade nyckeln.    
4. Bläddra bland ditt Stream Analytics-jobb > välj **Stopp** och vänta på att jobbet ska sluta på Azure-portalen.    
5. Leta reda på de indata/utdata för händelsehubbar som du vill uppdatera autentiseringsuppgifter för.    
6. Leta reda på fältet Principnyckel för **händelsehubben** och klistra in den nygenererade nyckeln > klicka på **Spara**.    
7. Ett anslutningstest startar automatiskt när du sparar ändringarna och kontrollerar att det har klarats.    
8. Fortsätt för att [starta jobbet från det senast stoppade tidsavsnittet.](#start-your-job-from-the-last-stopped-time)

### <a name="sql-database"></a>SQL Database

Du måste ansluta till SQL-databasen för att uppdatera inloggningsuppgifterna för en befintlig användare. Du kan uppdatera autentiseringsuppgifter med hjälp av Azure-portalen eller ett verktyg på klientsidan, till exempel SQL Server Management Studio. Det här avsnittet visar hur du uppdaterar autentiseringsuppgifter med hjälp av Azure-portalen.

1. Logga in på Azure-portalen > bläddra i SQL-databasen som du använde som utdata för Stream Analytics-jobbet.    
2. Från **Data Explorer**väljer du logga in/anslut till databasen > välja Auktoriseringstyp som **SQL-serverautentisering** > skriv in dina **inloggnings-** och **lösenordsuppgifter** > Välj **Ok**.  
   ![Återskapa autentiseringsuppgifter för SQL-databas](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. På fliken Fråga ändrar du lösenordet för en av användarens genom att `<user_name>` köra följande `<new_password>` fråga (se till att ersätta med ditt användarnamn och med ditt nya lösenord):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Anteckna det nya lösenordet.    
5. Bläddra bland ditt Stream Analytics-jobb > välj **Stopp** och vänta på att jobbet ska sluta på Azure-portalen.    
6. Leta reda på den SQL-databasutdata som du vill rotera autentiseringsuppgifter för. Uppdatera lösenordet och spara ändringar.    
7. Ett anslutningstest startar automatiskt när du sparar ändringarna och kontrollerar att det har klarats.    
8. Fortsätt för att [starta jobbet från det senast stoppade tidsavsnittet.](#start-your-job-from-the-last-stopped-time)

### <a name="power-bi"></a>Power BI
1. Logga in på Azure-portalen > bläddra i ditt Stream Analytics-jobb > väljer **Stopp** och väntar på att jobbet ska sluta.    
2. Leta upp den Power BI-utdata som du vill förnya autentiseringsuppgifter för > klickar på **förnya auktorisering** (du bör se ett meddelande om lyckade åtgärder) > **Spara** ändringarna.    
3. Ett anslutningstest startar automatiskt när du sparar ändringarna, se till att det har gått.    
4. Fortsätt för att [starta jobbet från det senast stoppade tidsavsnittet.](#start-your-job-from-the-last-stopped-time)

## <a name="start-your-job-from-the-last-stopped-time"></a>Starta jobbet från den senast stoppade tiden

1. Navigera till projektets **översiktsfönster** > väljer **Start** för att starta jobbet.    
2. Välj **När senast stoppade** > klicka på **Start**. Observera att alternativet "När senast stoppad" bara visas om du tidigare körde jobbet och har fått en del utdata genererade. Jobbet startas om baserat på det senaste utdatavärdets tid.
   ![Starta Stream Analytics-jobbet](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
