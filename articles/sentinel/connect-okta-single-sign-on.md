---
title: Anslut okta-data för enkel inloggning till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter okta-data för enkel inloggning till Azure Sentinel.
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
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532034"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Anslut din okta enkel inloggning till Azure Sentinel med Azure Function

Med okta för enkel inloggning (SSO) kan du enkelt ansluta alla dina [okta för enkel inloggning (SSO)](https://www.okta.com/products/single-sign-on/) med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan okta enkel inloggning och Azure Sentinel använder Azure Functions för att hämta logg data med hjälp av REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-okta-single-sign-on"></a>Konfigurera och Anslut okta-enkel inloggning

Azure Functions kan integrera och hämta händelser och loggar direkt från okta enkel inloggning och vidarebefordra dem till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **okta-anslutning för enkel inloggning** .

1. Välj **Öppna kopplings sida**.

1. Följ anvisningarna på sidan **okta enkel inloggning** .

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under tabellen **Okta_CL** .

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter okta enkel inloggning till Azure Sentinel med Azure Function-appar. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

