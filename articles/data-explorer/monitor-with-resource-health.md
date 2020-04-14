---
title: Övervaka Azure Data Explorer med hjälp av resurshälsa
description: Använd Azure Resource Health för att övervaka Azure Data Explorer-resurser.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: ee42f45d8c96e908061142b5a8e6b2e5cee21993
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262068"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Övervaka Azure Data Explorer med hjälp av resurshälsa (förhandsversion)

[Resource Health](/azure/service-health/resource-health-overview) för Azure Data Explorer informerar dig om hälsotillståndet för din Azure Data Explorer-resurs och ger användbara rekommendationer för att felsöka problem. Resurshälsan uppdateras var 1-2 minut och rapporterar den aktuella och tidigare hälsan hos dina resurser. 

Resource Health avgör hälsotillståndet för din Azure Data Explorer-resurs genom att undersöka olika hälsotillståndskontroller, till exempel:
* Resurstillgänglighet
* Frågefel

## <a name="access-resource-health-reporting"></a>Åtkomst till hälsorapporter för resurs

1. Välj **Övervaka** från listan över tjänster i [Azure-portalen.](https://portal.azure.com/)
1. Välj**hälsa för hälsoresurs för** **tjänsthälsa** > .
1. Välj prenumeration i listrutan **Prenumeration.** Välj **Azure Data Explorer**i listrutan **Resurstyp** .
1. I den resulterande tabellen visas alla resurser i den valda prenumerationen. Varje resurs har en hälsotillståndsikon som anger resurshälsan.
1. Välj resursen om du vill visa [dess resurshälsostatus](#resource-health-status) och rekommendationer.

    ![Översikt](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Hälsostatus för resurser

Hälsotillståndet för en resurs visas med en av följande statusar, tillgänglig, otillgänglig och okänd.

### <a name="available"></a>Tillgängligt

En hälsostatus **för tillgänglig** anger att din Azure Data Explorer-resurs är felfri och inte har några problem.

![Tillgängligt](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Inte tillgänglig

En hälsostatus **för Otillgänglig** anger att det finns ett pågående problem med din Azure Data Explorer-resurs som gör att den inte är tillgänglig för frågor och inmatning. Noder i azure data explorer-resursen kan till exempel ha startats om oväntat. Om din Azure Data Explorer-resurs förblir i det här tillståndet under en längre tid kontaktar du supporten.

![Inte tillgänglig](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Okänt

En hälsostatus **för Okänd** anger att Resource **Health** inte har fått information om den här Azure Data Explorer-resursen på mer än 10 minuter. Den här statusen är inte en slutgiltig indikation på Azure Data Explorer-resursens hälsotillstånd, men är en viktig datapunkt i felsökningsprocessen. Om ditt Azure Data Explorer-kluster fungerar som förväntat ändras statusen till **Tillgänglig** inom några minuter. Den **okända** hälsostatusen kan tyda på att en händelse i plattformen påverkar resursen. 

> [!TIP]
> Azure Data Explorer klusterresurs hälsa kommer att vara **okänd** om det är i ett stoppat tillstånd.

![Okänt](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Historisk information

I fönstret **Resurshälsa** > **Hälsohistoriken**får du åtkomst till upp till fyra veckors information om resurshälsostatus. Välj pilen för ytterligare information om hälsohändelseproblem som rapporteras i det här fönstret. 

![Historik](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera resurshälsoaviseringar](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Självstudiekurs: Övr och frågar övervakningsdata i Azure Data Explorer](ingest-data-no-code.md)
* [Övervaka Prestanda, hälsotillstånd och användning i Azure Data Explorer med mått](using-metrics.md)