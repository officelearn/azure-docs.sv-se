---
title: Ansluta Azure Information Protection till Azure Sentinel
description: Lär dig hur du ansluter Azure Information Protection data i Azure Sentinel.
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
# <a name="connect-data-from-azure-information-protection"></a>Anslut data från Azure Information Protection

> [!IMPORTANT]
> Azure Information Protection data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggnings information från [Azure information Protection](https://azure.microsoft.com/services/information-protection/) till Azure Sentinel genom att konfigurera Azure information Protection data Connector. Azure Information Protection hjälper dig att kontrol lera och skydda känsliga data, oavsett om de lagras i molnet eller lokalt.

Om [Central rapportering för Azure information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) redan har kon figurer ATS så att loggnings information från den här tjänsten lagras i samma Log Analytics arbets yta som du för närvarande har valt för Azure Sentinel, kan du hoppa över konfigurationen av data anslutningen. Loggnings informationen från Azure Information Protection är redan tillgänglig för Azure Sentinel.

Men om loggnings information från Azure Information Protection går till en annan Log Analytics arbets yta än den som du har valt för Azure Sentinel, gör du något av följande:

- Ändra arbets ytan som valts i Azure Sentinel.

- Ändra arbets ytan för Azure Information Protection, som du kan göra genom att konfigurera data kopplingen.
    
    Om du ändrar arbets ytan lagras nya rapport data för Azure Information Protection nu i arbets ytan du använder för Azure Sentinel, och historiska data är inte tillgängliga för Azure Sentinel. Om den föregående arbets ytan har kon figurer ATS för anpassade frågor, aviseringar eller REST-API: er måste de konfigureras om för Azure Sentinel-arbetsytan om du vill kunna använda dem för Azure Information Protection. Ingen omkonfiguration krävs för klienter och tjänster som använder Azure Information Protection.

## <a name="prerequisites"></a>Krav

- En av följande Azure AD-administratörs roller för din klient organisation: 
    - Azure Information Protection administratör
    - Säkerhetsadministratör
    - Administratör för efterlevnad
    - Data administratör för efterlevnad
    - Global administratör
    
    > [!NOTE]
    > Du kan inte använda rollen Azure Information Protection administratör om klienten finns på den [enhetliga etiketterande plattformen](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Dessa administratörs roller krävs endast för att konfigurera Azure Information Protection-anslutningen och krävs inte när Azure Sentinel är anslutet till Azure Information Protection.

- Behörighet att läsa och skriva till arbets ytan Log Analytics som du använder för Azure Sentinel och Azure Information Protection.

- Azure Information Protection har lagts till i Azure Portal. Om du behöver hjälp med det här steget går [du till Lägg till Azure information Protection i Azure Portal](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Anslut till Azure Information Protection

Använd följande instruktioner om du inte har konfigurerat en Log Analytics arbets yta för Azure Information Protection eller om du behöver ändra arbets ytan som lagrar Azure Information Protection loggnings information.

1. I Azure Sentinel väljer du **data kopplingar**och **Azure information Protection (för hands version)**.

2. Välj **Öppna kopplings sida**.

3. På bladet **Konfigurera Analytics (för hands version)** väljer du den arbets yta som du för närvarande använder för Azure Sentinel. Om du väljer en annan arbets yta är inte rapporterings data från Azure Information Protection tillgängliga för Azure Sentinel.

4. När du har valt en arbets yta väljer du **OK** och anslutnings **statusen** ska nu ändras till **ansluten**.

5. Rapporterings data från Azure Information Protection lagras i **InformationProtectionLogs_CLs** tabellen på den valda arbets ytan. 
    
    Om du vill använda det relevanta schemat i Azure Monitor för den här rapport informationen söker du efter **InformationProtectionEvents**. Information om dessa händelse funktioner finns i avsnittet [egna schema referenser för Event Functions](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) i Azure information Protection-dokumentationen.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure Information Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
