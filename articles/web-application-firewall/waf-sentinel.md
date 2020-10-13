---
title: Använda Azure Sentinel med Azure Web Application-brandvägg
description: Den här artikeln visar hur du använder Azure Sentinel med brand vägg för Azure Web Application (WAF)
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 525ec334e73ca010d319b40ab864d08dae32f493
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997382"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Använda Azure Sentinel med Azure Web Application-brandvägg

Azure brand vägg för webbaserade program (WAF) kombinerat med Azure Sentinel kan tillhandahålla säkerhets informations händelse hantering för WAF-resurser. Azure Sentinel tillhandahåller säkerhets analys med Log Analytics, vilket gör att du enkelt kan dela upp och Visa dina WAF-data. Med kontroll kan du komma åt färdiga arbets böcker och ändra dem så att de passar organisationens behov bäst. Arbets boken kan visa analyser för WAF på Azure Content Delivery Network (CDN), WAF på Azures front dörr och WAF på Application Gateway över flera prenumerationer och arbets ytor.

## <a name="waf-log-analytics-categories"></a>WAF Log Analytics-kategorier

WAF Log Analytics delas upp i följande kategorier:  

- Alla WAF-åtgärder som vidtagits 
- De 40 främsta blockerade begär ande URI-adresserna 
- Topp 50 händelse utlösare,  
- Meddelanden över tid 
- Fullständig meddelande information 
- Attack händelser per meddelanden  
- Attack händelser över tid 
- Filtret spårnings-ID 
- Spåra ID-meddelanden 
- 10 främsta angripna IP-adresser 
- Attack meddelanden för IP-adresser 

## <a name="waf-workbook-examples"></a>Exempel på WAF-arbetsböcker

Följande exempel på WAF-arbets böcker visar exempel data:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="Filter för WAF-åtgärder":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Filter för WAF-åtgärder":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Filter för WAF-åtgärder":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="Filter för WAF-åtgärder":::

## <a name="launch-a-waf-workbook"></a>Starta en WAF-arbetsbok

WAF-arbetsboken fungerar för alla Azure-frontend-, Application Gateway-och CDN-WAF. Innan du ansluter data från dessa resurser måste Log Analytics vara aktiverat på din resurs. 

Om du vill aktivera Log Analytics för varje resurs går du till din specifika Azure-frontend, Application Gateway eller CDN-resurs:

1. Välj **diagnostikinställningar**.
2. Välj **+ Lägg till diagnostisk inställning**. 
3. På sidan diagnostisk inställning:
   1. Skriv ett namn. 
   1. Välj **Skicka till Log Analytics**. 
   1. Välj mål arbets ytan logg. 
   1. Välj de logg typer som du vill analysera:
      1. Application Gateway: ' ApplicationGatewayAccessLog ' och ' ApplicationGatewayFirewallLog '
      1. Azure-front dörr: "FrontDoorAccessLog" och "FrontDoorFirewallLog"
      1. CDN: ' AzureCdnAccessLog '
   1. Välj **Spara**.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Filter för WAF-åtgärder":::

4. På Azures start sida skriver du **Azure Sentinel** i Sök fältet och väljer **Azure Sentinel** -resursen. 
2. Välj en redan aktiv arbets yta eller skapa en ny arbets yta i Sentinel. 
3. På den vänstra sidan under **konfiguration** väljer du **data anslutningar**.
4. Sök efter **brand vägg för Microsoft-webbprogram** och välj **Microsoft WebApplication-brandvägg (WAF)**. Välj **Öppna kopplings** sida längst ned till höger.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Filter för WAF-åtgärder":::

8. Följ anvisningarna under **konfiguration** för varje WAF-resurs som du vill ska logga analys data för om du inte har gjort det tidigare.
6. När du har konfigurerat enskilda WAF-resurser väljer du fliken **Nästa steg** . Välj en av de rekommenderade arbets böckerna. Den här arbets boken kommer att använda alla logg analys data som har Aktiver ATS tidigare. En arbets WAF arbets bok bör nu finnas för dina WAF-resurser.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="Filter för WAF-åtgärder":::


## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Sentinel](../sentinel/overview.md)
- [Läs mer om Azure Monitor arbets böcker](../azure-monitor/platform/workbooks-overview.md)
