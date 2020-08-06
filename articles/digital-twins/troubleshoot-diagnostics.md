---
title: Konfigurera diagnostik
titleSuffix: Azure Digital Twins
description: Se så här aktiverar du loggning med diagnostikinställningar.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 402eda92d5cc8e5c2566345d31c9ae625b82695c
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809414"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Felsöka Azure Digitals sammanflätade: diagnostikloggning

Azure Digitals flätar samlar in [Mät värden](troubleshoot-metrics.md) för din tjänst instans som ger information om statusen för dina resurser. Du kan använda dessa mått för att utvärdera den övergripande hälso statusen för Azure Digital-tjänster och de resurser som är anslutna till den. Den här användaren får hjälp att se vad som händer med Azures digitala dubbla, och hjälper till med rotor Saks analys av problem utan att behöva kontakta Azure-supporten.

Den här artikeln visar hur du aktiverar **diagnostikloggning** för dina mått data från din Azure Digital-instansen. Du kan använda dessa loggar för att hjälpa dig att felsöka service problem.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Aktivera diagnostikinställningar med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Välj **diagnostikinställningar** på menyn och **Lägg sedan till en diagnostisk inställning**.

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

3. Fyll i följande värden på sidan som följer:
     * **Namn på diagnos inställning**: ge diagnostikinställningar ett namn.
     * **Kategori information**: Välj vilka åtgärder som du vill övervaka och markera kryss rutorna för att aktivera diagnostik för dessa åtgärder. De åtgärder som diagnostikinställningar kan rapportera om är:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
     * **Mål information**: Välj var du vill skicka loggarna. Du kan välja en valfri kombination av de tre alternativen:
        - Skicka till Log Analytics
        - Arkivera till ett lagringskonto
        - Strömma till en händelsehubb

        Du kan bli ombedd att fylla i ytterligare information om de behövs för val av mål.  
    
4. Spara de nya inställningarna. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade målet tillbaka på sidan **diagnostikinställningar** för din instans. 

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar diagnostik finns i [*samla in och använda loggdata från dina Azure-resurser*](../azure-monitor/platform/platform-logs-overview.md).
* Information om Azure Digitals dubbla mått finns i [*fel sökning: Visa mått med Azure Monitor*](troubleshoot-metrics.md).
