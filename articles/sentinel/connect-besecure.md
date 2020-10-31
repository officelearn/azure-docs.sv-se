---
title: Ansluta bortom säkerhets säkra data till Azure Sentinel | Microsoft Docs
description: Lär dig mer om hur du använder säkerhets anslutningen för att skydda data på ett säkert sätt för att hämta säkra loggar till Azure Sentinel. Visa säkra data i arbets böcker, skapa aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103028"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Anslut din säkerhets efter säkerhet till Azure Sentinel

> [!IMPORTANT]
> Säkerheten för säker data anslutning i Azure Sentinel är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Utöver säkerhet säker anslutning kan du enkelt ansluta alla dina säkra säkerhets lösningar med din Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan säkert och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Konfigurera och anslut säkert

SÄKRA kan integreras med och exportera loggar direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **bortom säkerhet säker (för hands version)** och **öppnar sedan kopplings sidan** .

1. Följ stegen nedan för att konfigurera din säkra lösning för att skicka ut genomsöknings resultat, genomsöknings status och gransknings spårnings loggar till Azure Sentinel.

    **Öppna integrations menyn:**
    1. Klicka på meny alternativet Mer

    1. Välj server

    1. Välj integrering

    1. Aktivera Azure Sentinel

    **Ange säkra med Azure Sentinel-inställningar.**
      - Kopiera *arbetsyte-ID: t* och *primär nyckel* värden från sidan Azure Sentinel Connector, klistra in dem i den säkra konfigurationen och klicka på **ändra** .

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **CustomLogs** i en eller flera av följande tabeller:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Om du vill fråga efter säkra loggar i Log Analytics anger du ett av ovanstående tabell namn överst i frågefönstret.

## <a name="validate-connectivity"></a>Verifiera anslutning
Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter säkert till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
