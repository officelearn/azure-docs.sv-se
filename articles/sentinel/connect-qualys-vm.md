---
title: Ansluta Qualys sårbarhet Management-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Qualys sårbarhets hanterings data till Azure Sentinel.
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
ms.openlocfilehash: 44002a8f4ab3b644e3530ee2d2fc06a7af271fbe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531966"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>Anslut din virtuella Qualys-dator till Azure Sentinel med Azure Function

Med anslutnings tjänsten Qualys sårbarhet Management (VM) kan du enkelt ansluta alla dina Qualys-säkerhets lösningar för [virtuella datorer](https://www.qualys.com/apps/vulnerability-management/) med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan Qualys VM och Azure Sentinel använder Azure Functions för att hämta logg data med hjälp av REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-qualys-vm"></a>Konfigurera och Anslut Qualys VM

Azure Functions kan integrera och hämta händelser och loggar direkt från Qualys VM och vidarebefordra dem till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **Qualys sårbarhet Management** Connector.

1. Välj **Öppna kopplings sida**.

1. Följ anvisningarna på sidan **Qualys sårbarhets hantering** .

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under tabellen **QualysHostDetection_CL** .

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Qualys VM till Azure Sentinel med Azure Function-appar. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
