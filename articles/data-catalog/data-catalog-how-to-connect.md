---
title: Hur du ansluter till datakällor i Azure Data Catalog
description: Artikel visar hur du ansluter till datakällor som identifierats med Azure Data Catalog.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 759758c9f3d0f1dadf2048e8ef15eeab8a77ef07
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053498"
---
# <a name="how-to-connect-to-data-sources"></a>Så här ansluter du till datakällor
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för företagsdatakällor. Med andra ord **Azure Data Catalog** handlar om hjälper användarna att identifiera, förstå och använda datakällor och hjälper organisationer att få ut mer av sina befintliga data. En viktig aspekt av det här scenariot använder data – när en användare upptäcker en datakälla och förstår sitt syfte, nästa steg är att ansluta till datakällan som ska publicera sina data att använda.

## <a name="data-source-locations"></a>Data-källplatser
Under registrering av datakälla, **Azure Data Catalog** tar emot metadata om datakällan. Dessa metadata innehåller information om datakällans plats. Information om platsen varierar från datakällan till datakällan, men det kommer alltid att innehålla information som behövs för att ansluta. Platsen för en SQL Server-tabell innehåller till exempel servernamn, databasnamn, schemanamn och tabellnamn, medan platsen för en SQL Server Reporting Services-rapport innehåller namnet på servern och sökvägen till rapporten. Andra typer av datakällor har platser som återspeglar struktur och funktioner i källsystemet.

## <a name="integrated-client-tools"></a>Integrerade klientverktyg
Det enklaste sättet att ansluta till en datakälla är att använda den ”öppna i...” menyn i den **Azure Data Catalog** portal. Den här menyn visar en lista med alternativ för att ansluta till den valda datatillgången.
När du använder panelen standardvyn, är den här menyn tillgänglig i varje panel.

 ![Öppna en SQL Server-tabell i Excel från panelen data tillgång](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

När du använder listvyn finns på menyn i sökfältet längst ned i portalfönstret.

 ![Öppna en SQL Server Reporting Services-rapport i Report Manager från sökfältet](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Klientprogram som stöds
När du använder det ”öppna i...” menyn för datakällor i Azure Data Catalog-portalen, rätt klientprogrammet måste installeras på klientdatorn.

| Öppna i programmet | Filnamnstillägget / protokoll | Versioner av program som stöds |
| --- | --- | --- |
| Excel |ODC |Excel 2010 eller senare |
| Excel (Top 1000) |ODC |Excel 2010 eller senare |
| Power Query |.xlsx |Excel 2016 eller Excel 2010 eller Excel 2013 med Power Query för Excel-tillägget installerat |
| Power BI Desktop |.pbix |Power BI Desktop juli 2016 eller senare |
| SQL Server Data Tools |vsweb: / / |Visual Studio 2013 Update 4 eller senare med SQL Server-verktyg som installerats |
| Rapporthanteraren |http:// |Se [Webbläsarkrav för SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Dina data och dina verktyg
Alternativen i menyn beror på vilken typ av datatillgång markerad. Naturligtvis kan inte alla möjliga verktyg ska ingå i den ”öppna i...” menyn, men det är fortfarande enkelt att ansluta till datakällan med alla klientverktyg. När en datatillgång är markerad i den **Azure Data Catalog** portal, fullständiga placering visas i egenskapsfönstret.

 ![Anslutningsinformation för en SQL Server-tabell](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Information anslutningsinformationen skiljer sig från typen av datakälla för typ av datakälla, men informationen som ingår i portalen ger dig allt du behöver för att ansluta till datakällan i alla klientverktyg. Användare kan kopiera anslutningsinformationen för datakällorna som de har identifierats med hjälp av **Azure Data Catalog**, så att de kan arbeta med data i önskat verktyg.

## <a name="connecting-and-data-source-permissions"></a>Behörigheter för anslutning och data källa
Även om **Azure Data Catalog** gör datakällor kan identifieras, åtkomst till data förblir själva kontrolleras av dataägaren eller administratör. Identifiera en datakälla i **Azure Data Catalog** ger inte en användare behörighet att komma åt datakällan.

Om du vill göra det enklare för användare som upptäcker en datakälla men har inte behörighet att komma åt sina data, kan användare ange information i egenskapen begär åtkomst när kommentera en datakälla. Informationen här, inklusive länkar till processen eller kontaktpunkt för att få åtkomst till datakälla – visas tillsammans med informationen om datakällan plats i portalen.

 ![Anslutningsinformationen med instruktionerna för begäran om åtkomst](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Sammanfattning
Registrera en datakälla med **Azure Data Catalog** gör dessa data kan upptäckas genom att kopiera strukturella och beskrivande metadata från datakällan till Catalog-tjänsten. När en datakälla har registrerats och identifierats kan användare kan ansluta till datakällan från den **Azure Data Catalog** portal ”öppna i...” ” menyn eller med hjälp av sina Dataverktyg.

## <a name="see-also"></a>Se också
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md) självstudierna stegvis information om hur du ansluter till datakällor.
