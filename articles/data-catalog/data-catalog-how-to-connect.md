---
title: "Hur du ansluter till datakällor | Microsoft Docs"
description: "Artikel markering av hur du ansluter till datakällor som identifierats med Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 8176a952107a630d42d557e568a230f1cdc840aa
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-connect-to-data-sources"></a>Så här ansluter du till datakällor
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en helt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Med andra ord **Azure Data Catalog** handlar om hjälper människor identifiera, förstå och använda datakällor och hjälper organisationer att få ut mer av sina befintliga data. En viktig aspekt av det här scenariot använder data – när en användare identifierar en datakälla och förstår sitt syfte, nästa steg är att ansluta till datakällan för att placera data ska användas.

## <a name="data-source-locations"></a>Källplatser för data
Under registrering av datakälla, **Azure Data Catalog** tar emot metadata om datakällan. Dessa metadata innehåller information om datakällans plats. Information om platsen varierar från datakällan till datakällan, men det kommer alltid att innehålla information som behövs för att ansluta. Platsen för en tabell i SQL Server innehåller till exempel servernamn, databasnamn, schemanamnet och tabellnamn, medan platsen för en SQL Server Reporting Services-rapport innehåller namnet på servern och sökvägen till rapporten. Andra typer av datakällor har platser som motsvarar struktur och funktionerna i källsystemet.

## <a name="integrated-client-tools"></a>Integrerad klientverktyg
Det enklaste sättet att ansluta till en datakälla är att använda den ”öppna i...” menyn i den **Azure Data Catalog** portal. Den här menyn visar en lista med alternativ för att ansluta till den valda datatillgången.
När du använder standardvyn för sida vid sida, är den här menyn tillgängligt på varje bricka.

 ![Öppna en SQL Server-tabellen i Excel från panelen data tillgångsinformation](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

När du använder listvyn finns på menyn i sökfältet längst upp i fönstret portalen.

 ![Öppna en SQL Server Reporting Services-rapport i Report Manager från sökfältet](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Klientprogram som stöds
När du använder den ”öppna i...” menyn för datakällor i Azure Data Catalog-portalen rätt klientprogrammet måste installeras på klientdatorn.

| Öppna i programmet | Filnamnstillägget / protokoll | Versioner av det program som stöds |
| --- | --- | --- |
| Excel |.odc |Excel 2010 eller senare |
| Excel (överst 1000) |.odc |Excel 2010 eller senare |
| Power Query |.xlsx |Excel 2016 eller Excel 2010 eller Excel 2013 med Power Query för Excel-tillägg installeras |
| Power BI Desktop |.pbix |Power BI Desktop juli 2016 eller senare |
| SQL Server Data Tools |vsweb: / / |Visual Studio 2013 uppdatering 4 eller senare med SQL Server tooling installerad |
| Report Manager |http:// |Se [Webbläsarkrav för SQL Server Reporting Services](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Dina data, din verktyg
Alternativen i menyn beror på vilken typ av datatillgång markerade. Naturligtvis inte alla möjliga verktyg ska inkluderas i den ”öppna i...” menyn, men det är fortfarande enkelt att ansluta till datakällan med ett klientverktyg. När en datatillgång väljs i den **Azure Data Catalog** portalen fullständiga placering visas i egenskapsfönstret.

 ![Anslutningsinformationen för SQL Server-tabell](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Anslutningsinformationen för information kommer skiljer sig från typ av datakälla typ av datakälla, men informationen i portalen får du allt du behöver för att ansluta till datakällan i alla klientverktyg. Användare kan kopiera innehållet för datakällorna som de har identifierats med hjälp av **Azure Data Catalog**, så att de kan arbeta med data i önskat verktyg.

## <a name="connecting-and-data-source-permissions"></a>Ansluta och data behörigheter för datakällor
Även om **Azure Data Catalog** gör datakällor kan identifieras, åtkomst till data förblir själva kontrolleras av datakälla ägare eller administratör. Identifiera en datakälla i **Azure Data Catalog** ger inte användaren behörighet att komma åt datakällan.

Om du vill göra det lättare för användare som identifierar en datakälla men har inte behörighet att komma åt sina data kan ange användare information i egenskapen begär åtkomst när kommentera en datakälla. Informationen som visas här – inklusive länkar till processen eller kontaktpunkt för att få åtkomst till datakällan – visas tillsammans med information om datakällan plats i portalen.

 ![Anslutningsinformationen med instruktionerna för begäran om åtkomst](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Sammanfattning
Registrera en datakälla med **Azure Data Catalog** gör att data kan identifieras genom att kopiera strukturella och beskrivande metadata från datakällan till katalogtjänsten. När en datakälla har registrerats och identifierade kan användare kan ansluta till datakällan från den **Azure Data Catalog** portal ”öppna i...” ” menyn eller med hjälp av sina Dataverktyg föredrar.

## <a name="see-also"></a>Se även
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md) självstudiekursens steg för steg-information om hur du ansluter till datakällor.
