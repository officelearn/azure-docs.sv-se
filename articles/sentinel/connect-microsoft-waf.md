---
title: Ansluta brandväggsdata för webbprogram till Azure Sentinel
description: Lär dig hur du ansluter brandväggsdata för Microsoft-webbprogram till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588186"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Ansluta data från Brandvägg för Microsoft-webbprogram



Du kan strömma loggar från Azure Application Gateways brandvägg för Microsoft-webbprogram (WAF). Den här WAF skyddar dina program från vanliga webbsårbarheter som SQL-insprutning och skript på flera webbplatser, och du kan anpassa regler för att minska falska positiva identifieringar. Följ de här anvisningarna för att strömma brandväggsloggarna för Microsoft-webbprogrammet till Azure Sentinel.


## <a name="prerequisites"></a>Krav

- En befintlig programgatewayresurs

## <a name="connect-to-microsoft-web-application-firewall"></a>Ansluta till Microsofts brandvägg för webbprogram

Om du redan har Brandvägg för Microsoft-webbprogram kontrollerar du att du har en befintlig gatewayresurs.
När brandväggen för ditt Microsoft-webbprogram har distribuerats och hämtat data kan varningsdata enkelt strömmas till Azure Sentinel.
    
1. I Azure Sentinel-portalen väljer du **Datakopplingar**.
1. Välj **WAF-panelen** på sidan Datakopplingar.
1. Gå till [Application Gateway-resursen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) och välj din WAF.
    1. Välj **Diagnostikinställningar**.
    1. Välj **+ Lägg till diagnostikinställning** under tabellen.
    1. På sidan **Diagnostikinställningar** skriver du ett **namn** och väljer **Skicka till Logganalys**.
    1. Välj arbetsytan Azure Sentinel under **Logganalysarbetsyta.**
    1. Välj de loggtyper som du vill analysera. Vi rekommenderade: ApplicationGatewayAccessLog och ApplicationGatewayFirewallLog.
1. Om du vill använda det relevanta schemat i Log Analytics för brandväggsaviseringarna för Microsoft-webbprogrammet söker du efter **AzureDiagnostics**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du lära dig hur du ansluter Microsofts brandvägg för webbprogram till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
