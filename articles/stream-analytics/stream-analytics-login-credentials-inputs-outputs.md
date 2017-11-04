---
title: "Stream Analytics: Rotera logga in autentiseringsuppgifterna för in- och utdataenheter | Microsoft Docs"
description: "Lär dig mer om att uppdatera autentiseringsuppgifterna för Stream Analytics-in- och utdataenheter."
keywords: "autentiseringsuppgifter för inloggning"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: a1a927fa9c34b38e54fdb22782e80fd13bf430c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Rotera inloggningsuppgifterna för in- och utdataenheter i Stream Analytics-jobb
## <a name="abstract"></a>Abstrakt
Azure Stream Analytics idag tillåter inte att ersätta autentiseringsuppgifter på en in-/ utdata när jobbet körs.

Medan Azure Stream Analytics stöder återuppta ett jobb från senaste utdata, vill vi dela hela processen för att minimera fördröjning mellan stoppas och starta jobbet och rotera inloggningsuppgifterna.

## <a name="part-1---prepare-the-new-set-of-credentials"></a>Del 1 – förbereda den nya uppsättningen autentiseringsuppgifter:
Den här delen gäller följande indata/utdata:

* Blob Storage
* Händelsehubbar
* SQL Database
* Table Storage

Andra indata/utdata, fortsätter du med del 2.

### <a name="blob-storagetable-storage"></a>BLOB storage/tabellagring
1. Gå till lagring-tillägget på Azure-hanteringsportalen:  
   ![graphic1][graphic1]
2. Leta upp den lagring som används av ditt jobb och gå till den:  
   ![graphic2][graphic2]
3. Klicka på Hantera åtkomstnycklar-kommando:  
   ![graphic3][graphic3]
4. Mellan den primära åtkomstnyckeln och den sekundära åtkomstnyckeln **Välj den som inte används av ditt jobb**.
5. Klicka på Generera:  
   ![graphic4][graphic4]
6. Kopiera den nyligen skapade nyckeln:  
   ![graphic5][graphic5]
7. Fortsätta att del 2.

### <a name="event-hubs"></a>Händelsehubbar
1. Gå till Service Bus-tillägget på Azure-hanteringsportalen:  
   ![graphic6][graphic6]
2. Leta upp Service Bus Namespace som används av ditt jobb och gå till den:  
   ![graphic7][graphic7]
3. Om jobbet använder en princip för delad åtkomst på Service Bus-Namespace, hoppa till steg 6  
4. Gå till fliken Händelsehubbar:  
   ![graphic8][graphic8]
5. Hitta Händelsehubben som används av ditt jobb och gå till den:  
   ![graphic9][graphic9]
6. Gå till fliken Konfigurera:  
   ![graphic10][graphic10]
7. Leta upp den princip för delad åtkomst som används av ditt jobb på principnamn listrutan:  
   ![graphic11][graphic11]
8. Mellan primärnyckeln och sekundärnyckeln, **Välj den som inte används av ditt jobb**.  
9. Klicka på Generera:  
   ![graphic12][graphic12]
10. Kopiera den nyligen skapade nyckeln:  
   ![graphic13][graphic13]
11. Fortsätta att del 2.  

### <a name="sql-database"></a>SQL Database
> [!NOTE]
> Obs: du behöver ansluta till tjänsten SQL-databasen. Vi ska visa hur du gör detta med hjälp av hanteringen av på Azure-hanteringsportalen, men du kan välja att använda vissa klientsidan verktyg som SQL Server Management Studio samt.
>
> 

1. Gå till tillägget SQL-databaser på Azure-hanteringsportalen:  
   ![graphic14][graphic14]
2. Leta reda på SQL-databas som används av ditt jobb och **klickar du på servern** länk på samma rad:  
   ![graphic15][graphic15]
3. Klicka på kommandot hantera:  
   ![graphic16][graphic16]
4. Typen databasen Master:  
   ![graphic17][graphic17]
5. Ange ditt användarnamn, lösenord och klicka på Logga in:  
   ![graphic18][graphic18]
6. Klicka på ny fråga:  
   ![graphic19][graphic19]
7. Typen i följande fråga ersätta < login_name > med ditt användarnamn och ersätta <enterStrongPasswordHere> med ditt nya lösenord:  
   `CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8. Klicka på Kör:  
   ![graphic20][graphic20]
9. Gå tillbaka till steg 2 och den här gången klickar du på databasen:  
   ![graphic21][graphic21]
10. Klicka på kommandot hantera:  
   ![graphic22][graphic22]
11. Ange ditt användarnamn, lösenord och klicka på Logga in:  
   ![graphic23][graphic23]
12. Klicka på ny fråga:  
   ![graphic24][graphic24]
13. Skriv i följande fråga ersätta < användarnamn > med ett namn som du vill använda att identifiera den här inloggningen i kontexten för den här databasen (du kan ange samma värde som du gav för < login_name >, till exempel) och ersätter < login_name > med din nya användarnamn:  
   `CREATE USER <user_name> FROM LOGIN <login_name>`
14. Klicka på Kör:  
   ![graphic25][graphic25]
15. Nu bör du ge din nya användare med samma roller och behörigheter som den ursprungliga användaren hade.
16. Fortsätta att del 2.

## <a name="part-2-stopping-the-stream-analytics-job"></a>Del 2: Stoppar Stream Analytics-jobbet
1. Gå till Stream Analytics-tillägget på Azure-hanteringsportalen:  
   ![graphic26][graphic26]
2. Leta upp ditt jobb och gå till den:  
   ![graphic27][graphic27]
3. Gå till fliken för indata eller utdata baserat på om du rotera autentiseringsuppgifter på indata eller utdata.  
   ![graphic28][graphic28]
4. Klicka på kommandot Stop och bekräfta att jobbet har stoppats:  
   ![graphic29][graphic29] väntar du tills jobbet att stoppa.
5. Leta upp indata/utdata som du vill rotera autentiseringsuppgifter på och gå till den:  
   ![graphic30][graphic30]
6. Gå vidare till del 3.

## <a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Del 3: Redigera autentiseringsuppgifter på Stream Analytics-jobbet
### <a name="blob-storagetable-storage"></a>BLOB storage/tabellagring
1. Leta upp fältet Lagringskontonyckel och klistra in den nyligen skapade nyckeln till den:  
   ![graphic31][graphic31]
2. Klicka på kommandot Spara och bekräfta att spara dina ändringar:  
   ![graphic32][graphic32]
3. En anslutningstest startar automatiskt när du sparar ändringarna, se till att har har passerat.
4. Gå vidare till del 4.

### <a name="event-hubs"></a>Händelsehubbar
1. Hitta nyckelfältet Event Hub princip och klistra in den nyligen skapade nyckeln i den:  
   ![graphic33][graphic33]
2. Klicka på kommandot Spara och bekräfta att spara dina ändringar:  
   ![graphic34][graphic34]
3. En anslutningstest startar automatiskt när du sparar ändringarna, se till att den har passerat.
4. Gå vidare till del 4.

### <a name="power-bi"></a>Power BI
1. Klicka på förnya auktoriseringen:  

   ![graphic35][graphic35]
2. Du får följande bekräftelse:  

   ![graphic36][graphic36]
3. Klicka på kommandot Spara och bekräfta att spara dina ändringar:  
   ![graphic37][graphic37]
4. En anslutningstest startar automatiskt när du sparar ändringarna, se till att den passerat har.
5. Gå vidare till del 4.

### <a name="sql-database"></a>SQL Database
1. Hitta fälten användarnamn och lösenord och klistra in den nyligen skapade uppsättningen autentiseringsuppgifter i dem:  
   ![graphic38][graphic38]
2. Klicka på kommandot Spara och bekräfta att spara dina ändringar:  
   ![graphic39][graphic39]
3. En anslutningstest startar automatiskt när du sparar ändringarna, se till att den har passerat.  
4. Gå vidare till del 4.

## <a name="part-4-starting-your-job-from-last-stopped-time"></a>Del 4: Från och med jobbet senast stoppad
1. Navigera bort från in-/ utdata:  
   ![graphic40][graphic40]
2. Klicka på Start-kommando:  
   ![graphic41][graphic41]
3. Välj senast stoppad och klicka på OK:  
   ![graphic42][graphic42]
4. Gå vidare till del 5.  

## <a name="part-5-removing-the-old-set-of-credentials"></a>Del 5: Ta bort den gamla uppsättningen autentiseringsuppgifter
Den här delen gäller följande indata/utdata:

* Blob Storage
* Händelsehubbar
* SQL Database
* Table Storage

### <a name="blob-storagetable-storage"></a>BLOB storage/tabellagring
Upprepa del 1 för åtkomstnyckel som tidigare användes av ditt jobb för att förnya nu oanvända snabbtangent.

### <a name="event-hubs"></a>Händelsehubbar
Upprepa del 1 för den nyckel som tidigare användes av ditt jobb för att förnya nu oanvända nyckeln.

### <a name="sql-database"></a>SQL Database
1. Gå tillbaka till frågefönstret från del 1 steg 7 och anger följande fråga ersätta < previous_login_name > med det användarnamn som tidigare användes av ditt jobb:  
   `DROP LOGIN <previous_login_name>`  
2. Klicka på Kör:  
   ![graphic43][graphic43]  

Du bör få följande bekräftelse: 

    Command(s) completed successfully.

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png

