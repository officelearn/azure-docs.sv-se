---
title: Ansluta Orca-aviseringar till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Orca säkerhets aviserings data till Azure Sentinel, om du vill visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 1f2f67281079142f0c4fe4985738dc48332a107d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532264"
---
# <a name="connect-your-orca-alerts-to-azure-sentinel"></a>Anslut dina Orca-aviseringar till Azure Sentinel 

Med Orca Security Alerts Connector kan du enkelt få aviseringar om [Orca-aviseringar](https://orca.security/) om säkerhetslösningar i Azure Sentinel, så att du kan visa dem i arbets böcker, använda dem för att skapa anpassade aviseringar och lägga till dem för att förbättra undersökningen. Integreringen mellan Orca säkerhets aviseringar och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-orca-security-alerts"></a>Konfigurera och Anslut Orca säkerhets aviseringar

Orca säkerhets aviseringar kan integrera och exportera loggar direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **Orca säkerhets aviseringar** och **öppnar sedan kopplings sidan**.

2. Se https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration för att slutföra integreringen från Orca-plattformen.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under **CustomLogs** i tabellen **OrcaAlerts_CL** .
Om du vill använda det relevanta schemat i Log Analytics för Orca-aviseringar söker du efter `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Verifiera anslutning
Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Orca säkerhets aviseringar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

