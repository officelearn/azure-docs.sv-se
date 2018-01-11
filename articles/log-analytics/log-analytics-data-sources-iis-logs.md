---
title: IIS-loggar i Azure Log Analytics | Microsoft Docs
description: "Internet Information Services (IIS) lagrar användaraktivitet i loggfiler som kan samlas in av logganalys.  Den här artikeln beskriver hur du konfigurerar insamling av IIS-loggar och information om poster skapas i logganalys-arbetsytan."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: b8ce4e6fe6e12aa3edb81abad1589924e3e121e4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="iis-logs-in-log-analytics"></a>IIS-loggar i logganalys
Internet Information Services (IIS) lagrar användaraktivitet i loggfiler som kan samlas in av logganalys.  

![IIS-loggar](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurera IIS-loggar
Logganalys samlar in poster från loggfiler skapas i IIS, så du måste [konfigurera IIS för att logga](https://technet.microsoft.com/library/hh831775.aspx).

Logganalys endast har stöd för IIS-loggfiler i W3C-format och stöder inte anpassade fält eller avancerade IIS-loggningen.  
Logganalys samlar inte in loggar i NCSA eller IIS native-format.

Konfigurera IIS-loggar i logganalys från den [Data-menyn i logganalys-inställningar](log-analytics-data-sources.md#configuring-data-sources).  Det finns ingen konfiguration krävs för andra än att välja **samla in W3C format IIS-loggfiler**.

Vi rekommenderar att du ska konfigurera inställningen för IIS logg förnyelse på varje server när du aktiverar IIS Logginsamling.

## <a name="data-collection"></a>Datainsamling
Logganalys samlar in en IIS-loggposter från varje anslutna källa ungefär var 15: e minut.  Agenten registrerar dess plats i varje händelseloggen som samlas in från.  Om agenten tas offline, sedan logganalys samlar in händelser från där den senast slutade, även om de händelserna som skapades när agenten var offline.

## <a name="iis-log-record-properties"></a>Egenskaper för IIS-post
IIS-loggposter har en typ av **W3CIISLog** och ha egenskaper i följande tabell:

| Egenskap | Beskrivning |
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
| timeTaken |Lång tid att bearbeta begäran i millisekunder. |

## <a name="log-searches-with-iis-logs"></a>Loggen sökningar med IIS-loggar
Följande tabell innehåller olika exempel på loggen frågor som hämtar IIS-loggposter.

| Fråga | Beskrivning |
|:--- |:--- |
| W3CIISLog |Alla IIS-loggposter. |
| W3CIISLog &#124; där scStatus == 500 |Alla IIS-loggposter med returstatus 500. |
| W3CIISLog &#124; Sammanfatta count() av cIP |Antal av IIS-loggposter av klientens IP-adress. |
| W3CIISLog &#124; där csHost == ”www.contoso.com” &#124; Sammanfatta count() av csUriStem |Antal IIS loggposter URL för värden www.contoso.com. |
| W3CIISLog &#124; Sammanfatta sum(csBytes) av datorn &#124; ta 500000 |Totalt antal byte som tagits emot av varje IIS-dator. |

## <a name="next-steps"></a>Nästa steg
* Konfigurera Log Analytics för att samla in andra [datakällor](log-analytics-data-sources.md) för analys.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.
* Konfigurera aviseringar i logganalys att proaktivt meddela dig om viktiga villkor finns i IIS-loggar.
