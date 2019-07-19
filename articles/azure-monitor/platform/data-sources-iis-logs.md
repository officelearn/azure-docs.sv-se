---
title: IIS-loggar i Azure Monitor | Microsoft Docs
description: Internet Information Services (IIS) lagrar användar aktivitet i loggfiler som kan samlas in av Azure Monitor.  Den här artikeln beskriver hur du konfigurerar insamling av IIS-loggar och information om de poster som de skapar i Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 05d9dc8f676589dcb301c19b0a2e80e9fd4c1fa0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249746"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Samla in IIS-loggar i Azure Monitor
Internet Information Services (IIS) lagrar användar aktivitet i loggfiler som kan samlas in av Azure Monitor och lagras som [loggdata](data-platform.md).

![IIS-loggar](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurera IIS-loggar
Azure Monitor samlar in poster från loggfiler som skapats av IIS, så du måste [Konfigurera IIS för loggning](https://technet.microsoft.com/library/hh831775.aspx).

Azure Monitor stöder endast IIS-loggfiler som lagras i W3C-format och inte stöder anpassade fält eller avancerad IIS-loggning. De samlar inte in loggar i NCSA eller IIS-ursprungligt format.

Konfigurera IIS-loggar i Azure Monitor från [menyn Avancerade inställningar](agent-data-sources.md#configuring-data-sources).  Ingen konfiguration krävs förutom att välja **samla in W3C-format IIS**-loggfiler.


## <a name="data-collection"></a>Datainsamling
Azure Monitor samlar in IIS-loggfiler från varje agent varje gången loggens tidsstämpel ändras eller när en ny fil skapas. Loggen läses var 5: e minut. Frekvensen för att skapa nya filer styrs av inställningen för **förnyelse av logg filen** för IIS-platsen, vilket är en gång om dagen som standard. Om IIS inte uppdaterar tidsstämpeln före förnyelse tiden, om inställningen är **per timme**, Azure Monitor samla in loggen varje timme. Om inställningen är **dagligen**samlar Azure monitor in loggen var 24: e timme.


## <a name="iis-log-record-properties"></a>Egenskaper för logg poster i IIS
Poster i IIS-loggen har en typ av **W3CIISLog** och har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Computer |Namnet på datorn som händelsen samlades in från. |
| cIP |Klientens IP-adress. |
| csMethod |Metod för begäran, till exempel GET eller POST. |
| csReferer |Platsen som användaren följt av en länk från till den aktuella platsen. |
| csUserAgent |Webbläsarens typ av klient. |
| csUserName |Namnet på den autentiserade användare som har åtkomst till servern. Anonyma användare anges med ett bindestreck. |
| csUriStem |Målet för begäran, till exempel en webb sida. |
| csUriQuery |Fråga, om det finns, som klienten försökte utföra. |
| ManagementGroupName |Namnet på hanterings gruppen för Operations Managers agenter.  För andra agenter är detta AOI -\<arbetsyte-ID\> |
| RemoteIPCountry |Land/region för klientens IP-adress. |
| RemoteIPLatitude |Latitud för klientens IP-adress. |
| RemoteIPLongitude |Longitud för klientens IP-adress. |
| scStatus |HTTP-statuskod. |
| scSubStatus |Felkod för under status. |
| scWin32Status |Windows-statuskod. |
| sIP |IP-adressen för webb servern. |
| SourceSystem |OpsMgr |
| sPort |Port på den server som klienten är ansluten till. |
| sSiteName |Namnet på IIS-webbplatsen. |
| TimeGenerated |Datum och tid då posten loggades. |
| TimeTaken |Tids längd för att bearbeta begäran i millisekunder. |

## <a name="log-queries-with-iis-logs"></a>Logga frågor med IIS-loggar
Följande tabell innehåller olika exempel på logg frågor som hämtar poster i IIS-loggen.

| Söka i data | Beskrivning |
|:--- |:--- |
| W3CIISLog |Alla logg poster i IIS. |
| W3CIISLog &#124; där scStatus = = 500 |Alla poster i IIS-loggen med retur status 500. |
| W3CIISLog &#124; summerings antal () efter cIP |Antal IIS-loggfiler efter klient-IP-adress. |
| W3CIISLog &#124; där csHost = = "www\.contoso.com" &#124; sammanfatta antal () av csUriStem |Antal IIS-loggfiler efter URL för värd-www\.-contoso.com. |
| W3CIISLog &#124; sammanfatta sum (csBytes) per dator &#124; tar 500000 |Totalt antal byte som tagits emot av varje IIS-dator. |

## <a name="next-steps"></a>Nästa steg
* Konfigurera Azure Monitor för att samla in andra [data källor](agent-data-sources.md) för analys.
* Lär dig mer om [logga frågor](../log-query/log-query-overview.md) att analysera data som samlas in från datakällor och lösningar.
