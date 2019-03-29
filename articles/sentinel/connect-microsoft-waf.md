---
title: Samla in Microsoft web programdata brandväggen i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Lär dig mer om att samla in Microsoft web application firewall data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 2238060acb60b1be0d06b81f62fb45a7f1c7a9b6
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580605"
---
# <a name="collect-data-from-microsoft-web-application-firewall"></a>Samla in data från Microsoft-Brandvägg för webbaserade program

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från Azure-Programgateway Microsoft waf (WAF). Den här WAF skyddar dina program mot vanliga säkerhetsrisker som SQL-inmatning och cross site scripting och du kan anpassa regler för att minska antalet falska positiva identifieringar. Följ dessa instruktioner för att strömma brandväggen för Microsoft Web programloggar till Azure Sentinel.


## <a name="prerequisites"></a>Förutsättningar

- Resurs för en befintlig Programgateway

## <a name="connect-to-microsoft-web-application-firewall"></a>Ansluta till Microsoft-Brandvägg för webbaserade program

Om du redan har Microsoft Brandvägg för webbaserade program, kontrollera att du har en befintlig gateway-resurs.
När din Microsoft waf har distribuerats och hämtar data aviseringsdata kan enkelt strömmas till Sentinel-Azure.
    
5. I Sentinel-Azure-portalen väljer **datakopplingar**.
5. På sidan Data kopplingar, Välj den **WAF** panelen.
1. Gå till [Application Gateway-resursen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) och välj din WAF.
    1. Välj **diagnostikinställningar**.
    1. Välj **+ Lägg till diagnostikinställning** under tabellen.
    1. I den **diagnostikinställningar** anger du en **namn** och välj **skicka till Log Analytics**.
    1. Under **Log Analytics-arbetsytan** Välj Sentinel-Azure-arbetsyta.
    1. Välj loggtyper som du vill analysera. Vi rekommenderar att: ApplicationGatewayAccessLog och ApplicationGatewayFirewallLog.
6. Om du vill använda relevanta schema i Log Analytics för Microsoft web application firewall varningar, Sök efter **AzureDiagnostics**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Brandvägg för webbaserade program till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
