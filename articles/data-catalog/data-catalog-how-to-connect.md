---
title: Ansluta till datakällor i Azure Data Catalog
description: How-to-artikel som belyser hur du ansluter till datakällor som identifierats med Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1190a0f34206004b72730a6af85bbe5db7d9961a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976775"
---
# <a name="how-to-connect-to-data-sources"></a>Så här ansluter du till datakällor
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Med andra ord handlar **Azure Data Catalog** om att hjälpa människor att upptäcka, förstå och använda datakällor och hjälpa organisationer att få mer värde från sina befintliga data. En viktig aspekt av det här scenariot är att använda data – när en användare upptäcker en datakälla och förstår dess syfte är nästa steg att ansluta till datakällan för att använda sina data.

## <a name="data-source-locations"></a>Platser för datakälla
Under registrering av datakälla tar **Azure Data Catalog** emot metadata om datakällan. Dessa metadata innehåller information om datakällans plats. Informationen om platsen varierar från datakälla till datakälla, men den innehåller alltid den information som behövs för att ansluta. Platsen för en SQL Server-tabell innehåller till exempel servernamn, databasnamn, schemanamn och tabellnamn, medan platsen för en SQL Server Reporting Services-rapport innehåller servernamnet och sökvägen till rapporten. Andra datakälltyper har platser som återspeglar källsystemets struktur och funktioner.

## <a name="integrated-client-tools"></a>Integrerade klientverktyg
Det enklaste sättet att ansluta till en datakälla är att använda "Öppna in..." på **Portalen för Azure Data Catalog.** På den här menyn visas en lista med alternativ för anslutning till den valda datatillgången.
När du använder standardpanelvyn är den här menyn tillgänglig på varje panel.

 ![Öppna en SQL Server-tabell i Excel från datatillgångspanelen](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

När du använder listvyn är menyn tillgänglig i sökfältet högst upp i portalfönstret.

 ![Öppna en SQL Server Reporting Services-rapport i Rapporthanteraren](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Klientprogram som stöds
När du använder "Öppna in..." för datakällor i Azure Data Catalog-portalen måste rätt klientprogram installeras på klientdatorn.

| Öppna i ansökan | Filändelsen / protokoll | Programversioner som stöds |
| --- | --- | --- |
| Excel |Odc |Excel 2010 eller senare |
| Excel (topp 1000) |Odc |Excel 2010 eller senare |
| Power Query |.xlsx |Excel 2016 eller Excel 2010 eller Excel 2013 med tillägget Power Query för Excel installerat |
| Power BI Desktop |.pbix (pbix) |Power BI Desktop juli 2016 eller senare |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 eller senare med SQL Server-verktyg installerat |
| Rapporthanterare |http:// |Se [webbläsarkrav för SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Dina data, dina verktyg
Vilka alternativ som är tillgängliga på menyn beror på vilken typ av datatillgång som för närvarande är vald. Naturligtvis kommer inte alla möjliga verktyg att ingå i "Öppna i ..." men det är fortfarande enkelt att ansluta till datakällan med hjälp av något klientverktyg. När en datatillgång väljs i **Azure Data Catalog-portalen** visas den fullständiga platsen i egenskapsfönstret.

 ![Anslutningsinformation för en SQL Server-tabell](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Information om anslutningsinformation skiljer sig från datakälltyp till datakälltyp, men informationen som ingår i portalen ger dig allt du behöver för att ansluta till datakällan i valfritt klientverktyg. Användare kan kopiera anslutningsinformationen för de datakällor som de har upptäckt med Hjälp av **Azure Data Catalog**, så att de kan arbeta med data i sitt verktyg.

## <a name="connecting-and-data-source-permissions"></a>Ansluta och datakällabehörigheter
Även om **Azure Data Catalog** gör datakällor upptäckbara, förblir åtkomsten till själva datan under kontroll av datakällans ägare eller administratör. Att identifiera en datakälla i **Azure Data Catalog** ger inte en användare några behörigheter att komma åt själva datakällan.

För att göra det enklare för användare som upptäcker en datakälla men inte har behörighet att komma åt sina data kan användarna ange information i egenskapen Begär åtkomst när de kommenterar en datakälla. Information som lämnas här – inklusive länkar till processen eller kontaktpunkten för att få åtkomst till datakällan – presenteras tillsammans med datakällans platsinformation i portalen.

 ![Anslutningsinformation med instruktioner för åtkomst till begäran](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Sammanfattning
Om du registrerar en datakälla med **Azure Data Catalog** kan data identifieras genom att kopiera strukturella och beskrivande metadata från datakällan till katalogtjänsten. När en datakälla har registrerats och upptäckts kan användare ansluta till datakällan från **Azure Data Catalog-portalen** "Öppna i...". eller använda sina dataverktyg.

## <a name="see-also"></a>Se även
* [Komma igång med](data-catalog-get-started.md) azure datakatalogsjälvstudie för steg-för-steg-information om hur du ansluter till datakällor.
