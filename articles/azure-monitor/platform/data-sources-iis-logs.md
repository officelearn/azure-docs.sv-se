---
title: IIS-loggar i Azure Monitor | Microsoft-dokument
description: IIS (Internet Information Services) lagrar användaraktivitet i loggfiler som kan samlas in av Azure Monitor.  I den här artikeln beskrivs hur du konfigurerar insamling av IIS-loggar och information om de poster som de skapar i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 1b3ae6295a639c3d59643b106b920cb606572e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670584"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Samla in IIS-loggar i Azure Monitor
IIS (Internet Information Services) lagrar användaraktivitet i loggfiler som kan samlas in av Azure Monitor och lagras som [loggdata](data-platform.md).

![IIS-loggar](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurera IIS-loggar
Azure Monitor samlar in poster från loggfiler som skapats av IIS, så du måste [konfigurera IIS för loggning](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor stöder endast IIS-loggfiler som lagras i W3C-format och stöder inte anpassade fält eller IIS Advanced Logging. Den samlar inte in loggar i NCSA- eller IIS-format.

Konfigurera IIS-loggar i Azure Monitor på [menyn Avancerade inställningar](agent-data-sources.md#configuring-data-sources).  Det finns ingen annan konfiguration än att välja **IIS-loggfiler för insamling av W3C-format**.


## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in IIS-loggposter från varje agent varje gång loggtidsstämpeln ändras. Loggen läss var **5:e minut.** Om IIS av någon anledning inte uppdaterar tidsstämpeln före överrullningstiden när en ny fil skapas, samlas poster in efter att den nya filen har skapats. Frekvensen för att skapa nya filer styrs av inställningen **Loggfilsfördstrollningsschema** för IIS-platsen, som är en gång om dagen som standard. Om inställningen är **Varje timme**samlar Azure Monitor in loggen varje timme. Om inställningen är **Daglig**samlar Azure Monitor in loggen var 24:e timme.


## <a name="iis-log-record-properties"></a>Egenskaper för IIS-loggpost
IIS-loggposter har en typ av **W3CIISLog** och har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Namnet på den dator som händelsen hämtades från. |
| Cip |Klientens IP-adress. |
| csMethod (0) |Metod för begäran, till exempel GET eller POST. |
| csReferer (0) |Webbplats som användaren har följt en länk från till den aktuella webbplatsen. |
| csUserAgent (csUserAgent) |Klientens webbläsartyp. |
| csUserName |Namn på den autentiserade användare som kom åt servern. Anonyma användare anges med ett bindestreck. |
| csUriStem (0) |Målet för begäran, till exempel en webbsida. |
| csUriQuery (0) |Fråga, om någon, som klienten försökte utföra. |
| ManagementGroupName |Namn på ledningsgruppen för Operations Manager-agenter.  För andra agenter är detta\<AOI- arbetsyte-ID\> |
| RemoteIPCountry |Land/region för klientens IP-adress. |
| RemoteIPLatitude |Latitud för klient-IP-adressen. |
| RemoteIPLongitude |Longitud för klientens IP-adress. |
| scStatus (fmStatus) |HTTP-statuskod. |
| scSubStatus |Felkod för understatus. |
| scWin32Status |Windows-statuskod. |
| Sip |webbserverns IP-adress. |
| SourceSystem |OpsMgr (på ett sätt) |
| sPort (på ett sätt) |Port på den server som klienten är ansluten till. |
| sSiteName (SSiteName) |Namnet på IIS-webbplatsen. |
| TimeGenerated |Datum och tid då posten loggades. |
| Tidskörd |Tid för att bearbeta begäran i millisekunder. |

## <a name="log-queries-with-iis-logs"></a>Logga frågor med IIS-loggar
Följande tabell innehåller olika exempel på loggfrågor som hämtar IIS-loggposter.

| Söka i data | Beskrivning |
|:--- |:--- |
| W3CIISLog |Alla IIS-loggposter. |
| W3CIISLog &#124; där scStatus==500 |Alla IIS-loggposter med returstatus på 500. |
| W3CIISLog &#124; sammanfatta antal() med cIP |Antal IIS-loggposter efter klient-IP-adress. |
| W3CIISLog &#124; där csHost =="www\.contoso.com" &#124; sammanfattar antal() av csUriStem |Antal IIS-loggposter efter URL för\.värdens www contoso.com. |
| W3CIISLog &#124; sammanfatta summa (csBytes) av dator &#124; ta 500000 |Totalt antal byte som tas emot av varje IIS-dator. |

## <a name="next-steps"></a>Nästa steg
* Konfigurera Azure Monitor för att samla in andra [datakällor](agent-data-sources.md) för analys.
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar.
