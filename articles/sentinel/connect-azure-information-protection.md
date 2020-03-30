---
title: Ansluta Azure-informationsskydd till Azure Sentinel
description: Lär dig hur du ansluter Azure Information Protection-data i Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588560"
---
# <a name="connect-data-from-azure-information-protection"></a>Ansluta data från Azure Information Protection

> [!IMPORTANT]
> Azure Information Protection-dataanslutningen i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggningsinformation från [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) till Azure Sentinel genom att konfigurera Azure Information Protection-dataappen. Azure Information Protection hjälper dig att kontrollera och skydda dina känsliga data, oavsett om de lagras i molnet eller lokalt.

Om [central rapportering för Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) redan är konfigurerad så att loggningsinformation från den här tjänsten lagras på samma Log Analytics-arbetsyta som du för närvarande har valt för Azure Sentinel, kan du hoppa över konfigurationen av den här dataanslutningen. Loggningsinformationen från Azure Information Protection är redan tillgänglig för Azure Sentinel.

Men om loggningsinformation från Azure Information Protection går till en annan Log Analytics-arbetsyta än den du för närvarande har valt för Azure Sentinel gör du något av följande:

- Ändra arbetsytan som valts i Azure Sentinel.

- Ändra arbetsytan för Azure Information Protection, vilket du kan göra genom att konfigurera den här dataanslutningen.
    
    Om du ändrar arbetsytan lagras nu nya rapporteringsdata för Azure Information Protection på arbetsytan som du använder för Azure Sentinel och historiska data är inte tillgängliga för Azure Sentinel. Om den tidigare arbetsytan är konfigurerad för anpassade frågor, aviseringar eller REST-API:er måste dessa dessutom konfigureras om för Azure Sentinel-arbetsytan om du vill fortsätta använda dem för Azure Information Protection. Ingen omkonfiguration behövs för klienter och tjänster som använder Azure Information Protection.

## <a name="prerequisites"></a>Krav

- En av följande Azure AD-administratörsroller för din klient: 
    - Administratör för Azure-informationsskydd
    - Säkerhetsadministratör
    - Administratör för efterlevnad
    - Administratör för efterlevnadsdata
    - Global administratör
    
    > [!NOTE]
    > Du kan inte använda administratörsrollen för Azure Information Protection om din klient är på den [enhetliga etikettplattformen](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Dessa administratörsroller krävs endast för att konfigurera Azure Information Protection-anslutningen och krävs inte när Azure Sentinel är anslutet till Azure Information Protection.

- Behörigheter att läsa och skriva till log analytics-arbetsytan som du använder för Azure Sentinel och Azure Information Protection.

- Azure Information Protection har lagts till i Azure-portalen. Om du behöver hjälp med det här steget läser du [Lägga till Azure-informationsskydd i Azure-portalen](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Ansluta till Azure-informationsskydd

Använd följande instruktioner om du inte har konfigurerat en Log Analytics-arbetsyta för Azure Information Protection, eller om du behöver ändra arbetsytan som lagrar loggningsinformationen för Azure Information Protection.

1. I Azure Sentinel väljer du **Data-anslutningsappar**och sedan **Azure Information Protection (Preview).**

2. Välj **Öppna kopplingssida**.

3. På bladet **Konfigurera analys (förhandsversion)** väljer du den arbetsyta som du för närvarande använder för Azure Sentinel. Om du väljer en annan arbetsyta är rapportdata från Azure Information Protection inte tillgängliga för Azure Sentinel.

4. När du har valt en arbetsyta väljer du **OK** och kopplingen **STATUS** ska nu ändras till **Ansluten**.

5. Rapportdata från Azure Information Protection lagras i **tabellen InformationProtectionLogs_CL** inom den valda arbetsytan. 
    
    Om du vill använda det relevanta schemat i Azure Monitor för dessa rapporteringsdata söker du efter **InformationProtectionEvents**. Information om dessa händelsefunktioner finns i avsnittet [Friendly schemareferens för händelsefunktioner](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) i Azure Information Protection-dokumentationen.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Information Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
