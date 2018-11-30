---
title: IIS-loggar i Azure Log Analytics | Microsoft Docs
description: Internet Information Services (IIS) lagrar användarnas aktivitet i loggfiler som kan samlas in av Log Analytics.  Den här artikeln beskriver hur du konfigurerar insamling av IIS-loggar och information om de poster som skapas i Log Analytics-arbetsytan.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: bwren
ms.comopnent: ''
ms.openlocfilehash: e33c30f9de56c4c5dd5f898a6a5136bbcef36c18
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336914"
---
# <a name="iis-logs-in-log-analytics"></a>IIS-loggar i Log Analytics
Internet Information Services (IIS) lagrar användarnas aktivitet i loggfiler som kan samlas in av Log Analytics.  

![IIS-loggar](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurera IIS-loggar
Log Analytics samlar in poster från loggfiler som skapas i IIS, så du måste [konfigurera IIS för att logga](https://technet.microsoft.com/library/hh831775.aspx).

Log Analytics kan du endast har stöd för IIS-loggfiler i W3C-format och stöder inte anpassade fält eller avancerade IIS-loggningen.  
Log Analytics samlar inte in loggar i ursprungligt format för NCSA eller IIS.

Konfigurera IIS-loggar i Log Analytics från den [Data-menyn i logganalys-inställningar](agent-data-sources.md#configuring-data-sources).  Det finns ingen konfiguration behövs är att välja **samla in W3C-format IIS-loggfiler**.


## <a name="data-collection"></a>Datainsamling
Log Analytics samlar in IIS-loggposter från varje agent som skapas varje gång loggen stängs och ett nytt lösenord. Denna frekvens styrs av den **Log File förnya schema** för IIS-webbplats som är en gång per dag som standard. Om inställningarna är till exempel **per timme**, och sedan Log Analytics samlar in loggen varje timme.  Om inställningen är **dagliga**, och sedan Log Analytics samlar in loggen var 24: e timme.


## <a name="iis-log-record-properties"></a>Egenskaper för IIS-post
IIS-loggposter har en typ av **W3CIISLog** och har egenskaperna i följande tabell:

| Egenskap  | Beskrivning |
|:--- |:--- |
| Dator |Namnet på den dator som händelsen har samlats in från. |
| cIP |IP-adressen för klienten. |
| csMethod |Metod för begäran, till exempel GET eller POST. |
| csReferer |Plats att användaren följt en länk från den aktuella platsen. |
| csUserAgent |Webbläsartyp av klienten. |
| csUserName |Namnet på den autentiserade användare som kan komma åt servern. Anonyma användare anges med ett bindestreck. |
| csUriStem |Mål för begäran, till exempel en webbsida. |
| csUriQuery |Fråga, om sådana finns, som klienten försökte utföra. |
| ManagementGroupName |Namnet på hanteringsgruppen för Operations Manager-agenter.  För andra agenter är detta AOI -\<arbetsyte-ID\> |
| RemoteIPCountry |Land där IP-adressen för klienten. |
| RemoteIPLatitude |Latitud för klientens IP-adress. |
| RemoteIPLongitude |Longitud för klientens IP-adress. |
| scStatus |HTTP-statuskod. |
| scSubStatus |Understatus. |
| scWin32Status |Windows-statuskoden. |
| sIP |IP-adressen för webbservern. |
| SourceSystem |OpsMgr |
| sPort |Porten på servern är ansluten till klienten. |
| sSiteName |Namnet på den IIS-webbplatsen. |
| TimeGenerated |Datum och tid i posten loggades. |
| timeTaken |Lång tid att bearbeta begäran i millisekunder. |

## <a name="log-searches-with-iis-logs"></a>Loggsökningar med IIS-loggar
I följande tabell innehåller olika exempel på loggfrågor som hämtar IIS-loggposter.

| Söka i data | Beskrivning |
|:--- |:--- |
| W3CIISLog |Alla IIS-loggposter. |
| W3CIISLog &#124; där scStatus == 500 |Alla IIS-loggposter med returstatus 500. |
| W3CIISLog &#124; summera antal() efter cIP |Antal IIS-logg poster av klientens IP-adress. |
| W3CIISLog &#124; där csHost == ”www.contoso.com” &#124; summera antal() efter csUriStem |Antal IIS loggposter per Webbadress för värden www.contoso.com. |
| W3CIISLog &#124; sammanfatta sum(csBytes) per dator &#124; ta 500000 |Totalt antal byte mottaget för varje IIS-dator. |

## <a name="next-steps"></a>Nästa steg
* Konfigurera Log Analytics för att samla in andra [datakällor](agent-data-sources.md) för analys.
* Lär dig mer om [loggsökningar](../../log-analytics/log-analytics-queries.md) att analysera data som samlas in från datakällor och lösningar.
* Konfigurera aviseringar i Log Analytics för att informera dig om viktiga villkor finns i IIS-loggar.
