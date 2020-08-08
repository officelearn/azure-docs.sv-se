---
title: Konfigurera diagnostik
titleSuffix: Azure Digital Twins
description: Se så här aktiverar du loggning med diagnostikinställningar.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: d47bb7cc868c5733c6e36290f097fec783764cd3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003589"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Felsöka Azure Digitals sammanflätade: diagnostikloggning

Azure Digitals flätar samlar in [Mät värden](troubleshoot-metrics.md) för din tjänst instans som ger information om statusen för dina resurser. Du kan använda dessa mått för att utvärdera den övergripande hälso statusen för Azure Digital-tjänster och de resurser som är anslutna till den. Den här användaren får hjälp att se vad som händer med Azures digitala dubbla, och hjälper till med rotor Saks analys av problem utan att behöva kontakta Azure-supporten.

Den här artikeln visar hur du aktiverar **diagnostikloggning** för dina mått data från din Azure Digital-instansen. Du kan använda dessa loggar för att hjälpa dig att felsöka service problem och konfigurera diagnostikinställningar för att skicka Azure Digitals mått till olika mål. Du kan läsa mer om de här inställningarna i [*skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Aktivera diagnostikinställningar med Azure Portal

Så här aktiverar du diagnostikinställningar för din Azure Digital-instansen:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Välj **diagnostikinställningar** på menyn och **Lägg sedan till en diagnostisk inställning**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

3. Fyll i följande värden på sidan som följer:
     * **Namn på diagnos inställning**: ge diagnostikinställningar ett namn.
     * **Kategori information**: Välj vilka åtgärder som du vill övervaka och markera kryss rutorna för att aktivera diagnostik för dessa åtgärder. De åtgärder som diagnostikinställningar kan rapportera om är:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Mer information om de här alternativen finns i avsnittet [*kategori information*](#category-details) nedan.
     * **Mål information**: Välj var du vill skicka loggarna. Du kan välja en valfri kombination av de tre alternativen:
        - Skicka till Log Analytics
        - Arkivera till ett lagringskonto
        - Strömma till en händelsehubb

        Du kan bli ombedd att fylla i ytterligare information om de behövs för val av mål.  
    
4. Spara de nya inställningarna. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Skärm bild som visar sidan diagnostikinställningar och knappen som ska läggas till":::

Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade målet tillbaka på sidan **diagnostikinställningar** för din instans. 

## <a name="category-details"></a>Kategori information

Här finns mer information om de logg kategorier som kan väljas under **kategori information** när du ställer in diagnostikinställningar.

| Loggkategori | Beskrivning |
| --- | --- |
| ADTModelsOperation | Logga alla API-anrop som rör modeller |
| ADTQueryOperation | Logga alla API-anrop som rör frågor |
| ADTEventRoutesOperation | Logga alla API-anrop som hör till händelse vägar samt utgående av händelser från Azure Digitals dubbla till en slut punkts tjänst som Event Grid, Event Hubs och Service Bus |
| ADTDigitalTwinsOperation | Logga alla API-anrop som hör till Azure Digitals dubbla |

Varje logg kategori består av åtgärder av Skriv-, Läs-, borttagnings-och åtgärds åtgärder.  Dessa mappar till REST API samtal på följande sätt:

| Händelsetyp | REST API åtgärder |
| --- | --- |
| Skriva | PLACERING och korrigering |
| Läsa | GET |
| Ta bort | DELETE |
| Action | POST |

Här är en omfattande lista över åtgärderna och motsvarande [Azure Digital-meddelanden REST API samtal](https://docs.microsoft.com/rest/api/azure-digitaltwins/) som loggas i varje kategori. 

>[!NOTE]
> Varje logg kategori innehåller flera åtgärder/REST API-anrop. I tabellen nedan mappas varje logg kategori till alla åtgärder/REST API samtal under den tills nästa logg kategori visas. 

| Loggkategori | Åtgärd | REST API-anrop och andra händelser |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modeller/Skriv | Uppdaterings-API för digitala dubbla modeller |
|  | Microsoft. DigitalTwins/modeller/läsa | Digitala dubbla modeller får av ID-och list-API: er |
|  | Microsoft. DigitalTwins/Models/Delete | Digitala dubbla modeller ta bort API |
|  | Microsoft. DigitalTwins/modeller/åtgärd | Digitala dubbla modeller Lägg till API |
| ADTQueryOperation | Microsoft. DigitalTwins/fråga/åtgärd | Frågans dubbla API |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Händelse vägar Lägg till API |
|  | Microsoft. DigitalTwins/eventroutes/Read | Händelse vägar hämtas efter ID och lista API: er |
|  | Microsoft. DigitalTwins/eventroutes/Delete | Borttagnings-API för händelse vägar |
|  | Microsoft. DigitalTwins/eventroutes/Action | Utgående en händelse till en slut punkts tjänst (inte ett API-anrop) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digitals, Lägg till, Lägg till relation, uppdatera, uppdatera komponent |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Digitals, Hämta efter ID, Hämta komponent, Hämta relation efter ID, lista inkommande relationer, list relationer |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Digitals ta bort, ta bort relation |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Digitala dubblare skicka telemetri för komponent, skicka telemetri |

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar diagnostik finns i [*samla in och använda loggdata från dina Azure-resurser*](../azure-monitor/platform/platform-logs-overview.md).
* Information om Azure Digitals dubbla mått finns i [*fel sökning: Visa mått med Azure Monitor*](troubleshoot-metrics.md).
* Information om hur du aktiverar aviseringar för dina mått finns i [*fel sökning: Konfigurera aviseringar*](troubleshoot-alerts.md).
