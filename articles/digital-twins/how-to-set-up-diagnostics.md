---
title: Konfigurera diagnostik
titleSuffix: Azure Digital Twins
description: Se så här aktiverar du loggning med diagnostikinställningar.
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374571"
---
# <a name="enable-logging-with-diagnostics-settings"></a>Aktivera loggning med diagnostikinställningar

Du kan välja att skicka Mät data till [Log Analytics](../azure-monitor/log-query/get-started-portal.md), en [Event Hubs](../event-hubs/event-hubs-about.md) -slutpunkt eller [Azure Storage](../storage/blobs/storage-blobs-overview.md) genom att aktivera loggning med diagnostikinställningar för din instans.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Aktivera diagnostikinställningar med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Välj **diagnostikinställningar** på menyn och **Lägg sedan till en diagnostisk inställning**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

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

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade målet tillbaka på sidan **diagnostikinställningar** för din instans. 

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar diagnostik finns i [*samla in och använda loggdata från dina Azure-resurser*](../azure-monitor/platform/platform-logs-overview.md).
* Information om Azure Digitals dubbla mått finns i [ *instruktion: Visa mått med Azure Monitor*](how-to-view-metrics.md)