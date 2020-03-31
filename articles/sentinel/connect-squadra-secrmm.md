---
title: Anslut SecRMM-data för Squadra Technologies till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Squadra Technologies secRMM-data till Azure Sentinel.
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
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588118"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Anslut dina SecRMM-data för Squadra Technologies till Azure Sentinel 

> [!IMPORTANT]
> Dataanslutningsappen För Squadra Technologies Security Removable Media Manager (secRMM) i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Med Squadra Technologies secRMM-kontakten kan du enkelt ansluta dina Squadra Technologies secRMM-säkerhetslösningsloggar med Azure Sentinel. Det låter dig visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Den här kontakten ger dig insikter om usb-flyttbara lagringshändelser. Integration mellan Squadra Technologies secRMM och Azure Sentinel använder SIG av REST API.


> [!NOTE]
> Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurera och ansluta Squadra Technologies secRMM 

Squadra Technologies secRMM kan integrera och exportera loggar direkt till Azure Sentinel.
1. I Azure Sentinel-portalen klickar du på Datakopplingar och väljer Squadra Technologies secRMM och sedan öppna kopplingssidan.

2. Följ stegen som beskrivs i [Inboarding-guiden för Squadra Technologies för Azure Sentinel för](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) att hämta Squadra secRMM-data i Azure Sentinel.   


## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under CustomLogs secRMM_CL.
Om du vill använda det relevanta schemat i Log Analytics for the Squadra Technologies secRMM söker du efter secRMM_CL.

## <a name="validate-connectivity"></a>Verifiera anslutning
Det kan ta uppemot 20 minuter innan loggarna börjar visas i Log Analytics. 


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Squadra Technologies secRMM till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.

