---
title: IIS-loggar i Azure Log Analytics | Microsoft Docs
description: Internet Information Services (IIS) lagrar användaraktivitet i loggfiler som kan samlas in av logganalys.  Den här artikeln beskriver hur du konfigurerar insamling av IIS-loggar och information om poster skapas i logganalys-arbetsytan.
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
ms.comopnent: na
ms.openlocfilehash: 65320e7d3cc97a3d53fd1a00fbbeab5559c02fce
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133137"
---
# <a name="iis-logs-in-log-analytics"></a>IIS-loggar i logganalys
Internet Information Services (IIS) lagrar användaraktivitet i loggfiler som kan samlas in av logganalys.  

![IIS-loggar](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurera IIS-loggar
Logganalys samlar in poster från loggfiler skapas i IIS, så du måste [konfigurera IIS för att logga](https://technet.microsoft.com/library/hh831775.aspx).

Logganalys endast har stöd för IIS-loggfiler i W3C-format och stöder inte anpassade fält eller avancerade IIS-loggningen.  
Logganalys samlar inte in loggar i NCSA eller IIS native-format.

Konfigurera IIS-loggar i logganalys från den [Data-menyn i logganalys-inställningar](log-analytics-data-sources.md#configuring-data-sources).  Det finns ingen konfiguration krävs för andra än att välja **samla in W3C format IIS-loggfiler**.


## <a name="data-collection"></a>Datainsamling
Logganalys samlar in IIS-loggposter från varje agent varje gång loggen stängs och en ny skapas. Denna frekvens styrs av den **Log File förnyelse schema** för IIS-webbplatsen som är en gång per dag som standard. Om inställningarna är till exempel **timvis**, och sedan logganalys samlar in loggen varje timme.  Om inställningen är **dagliga**, och sedan logganalys samlar in loggen var 24: e timme.


## <a name="iis-log-record-properties"></a>Egenskaper för IIS-post
IIS-loggposter har en typ av **W3CIISLog** och ha egenskaper i följande tabell:

| Egenskap  | Beskrivning |
|:--- |:--- |
| Dator |Namnet på den dator som händelsen samlats in från. |
| cIP |IP-adressen för klienten. |
| csMethod |Metod för begäran som GET eller POST. |
| csReferer |Plats att användaren har följt en länk från den aktuella platsen. |
| csUserAgent |Webbläsartyp av klienten. |
| csUserName |Namnet på den autentiserade användare som kan komma åt servern. Anonyma användare anges med ett bindestreck. |
| csUriStem |Mål för begäran som en webbsida. |
| csUriQuery |Fråga, om sådan finns, som klienten försökte utföra. |
| ManagementGroupName |Namnet på hanteringsgruppen för Operations Manager-agenter.  För andra agenter är AOI -\<arbetsyte-ID\> |
| RemoteIPCountry |Land där IP-adressen för klienten. |
| RemoteIPLatitude |Latitud för klientens IP-adress. |
| RemoteIPLongitude |Longituden för klientens IP-adress. |
| scStatus |HTTP-statuskod. |
| scSubStatus |Understatus. |
| scWin32Status |Windows-statuskod. |
| sIP |IP-adressen för webbservern. |
| SourceSystem |OpsMgr |
| sPort |Porten på servern och klienten är ansluten till. |
| sSiteName |Namnet på IIS-webbplatsen. |
| TimeGenerated |Datum och tid transaktionen loggades. |
| TimeTaken |Lång tid att bearbeta begäran i millisekunder. |

## <a name="log-searches-with-iis-logs"></a>Loggen sökningar med IIS-loggar
Följande tabell innehåller olika exempel på loggen frågor som hämtar IIS-loggposter.

| Fråga | Beskrivning |
|:--- |:--- |
| W3CIISLog |Alla IIS-loggposter. |
| W3CIISLog &#124; där scStatus == 500 |Alla IIS-loggposter med returstatus 500. |
| W3CIISLog &#124; sammanfatta count() av cIP |Antal av IIS-loggposter av klientens IP-adress. |
| W3CIISLog &#124; där csHost == ”www.contoso.com” &#124; sammanfatta count() av csUriStem |Antal IIS loggposter URL för värden www.contoso.com. |
| W3CIISLog &#124; sammanfatta sum(csBytes) per dator &#124; ta 500000 |Totalt antal byte som tagits emot av varje IIS-dator. |

## <a name="next-steps"></a>Nästa steg
* Konfigurera Log Analytics för att samla in andra [datakällor](log-analytics-data-sources.md) för analys.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.
* Konfigurera aviseringar i logganalys att proaktivt meddela dig om viktiga villkor finns i IIS-loggar.
