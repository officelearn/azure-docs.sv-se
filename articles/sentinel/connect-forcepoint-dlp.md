---
title: Anslut Forcepoint DLP till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Forcepoint DLP till Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588254"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Ansluta din Forcepoint DLP till Azure Sentinel

> [!IMPORTANT]
> DLP-dataanslutningen (Forcepoint Data Loss Prevention) i Azure Sentinel är för närvarande i offentlig förhandsversion. Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Med Forcepoint DLP-kopplingen kan du automatiskt exportera DLP-incidentdata till Azure Sentinel. Du kan använda den för att få insyn i användaraktiviteter och dataförlustincidenter. Det möjliggör också korrelationer med data från Azure-arbetsbelastningar och andra feeds och förbättrar övervakningsfunktionen med arbetsböcker i Azure Sentinel.

> [!NOTE]
> Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Konfigurera och ansluta Forcepoint DLP

Konfigurera Forcepoint DLP för att vidarebefordra incidentdata i JSON-format till din Azure-arbetsyta via REST API enligt beskrivningen i [Forcepoint DLP-integrationsguiden](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Hitta dina data

När Forcepoint DLP-kopplingen har konfigurerats visas data i Log Analytics under CustomLogs **ForcepointDLPEvents_CL**.


Om du vill använda det relevanta schemat i Log Analytics for Forcepoint DLP söker du efter **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta uppemot 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Forcepoint DLP till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.