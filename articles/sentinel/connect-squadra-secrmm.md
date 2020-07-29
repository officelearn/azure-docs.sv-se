---
title: Anslut Squadra Technologies secRMM data till Azure Sentinel | Microsoft Docs
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588118"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Anslut dina Squadra Technologies secRMM-data till Azure Sentinel 

> [!IMPORTANT]
> Squadra Technologies Security Removable Media Manager (secRMM) data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Med Squadra Technologies secRMM Connector kan du enkelt ansluta Squadra Technologies secRMM säkerhets lösnings loggar med Azure Sentinel. Du kan visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Den här anslutnings tjänsten ger dig insikter om händelser för flyttbara USB-lagring. Integreringen mellan Squadra Technologies secRMM och Azure Sentinel använder REST API.


> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurera och Anslut Squadra Technologies secRMM 

Squadra Technologies secRMM kan integrera och exportera loggar direkt till Azure Sentinel.
1. I Azure Sentinel-portalen klickar du på data kopplingar och väljer Squadra Technologies secRMM och öppnar sedan kopplings sidan.

2. Följ stegen som beskrivs i [onboarding-guiden för Squadra Technologies för Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) för att hämta Squadra secRMM-data i Azure Sentinel.   


## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under CustomLogs secRMM_CL.
Om du vill använda det relevanta schemat i Log Analytics för Squadra Technologies secRMM söker du efter secRMM_CL.

## <a name="validate-connectivity"></a>Verifiera anslutning
Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Squadra Technologies secRMM till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

