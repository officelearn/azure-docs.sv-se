---
title: Samla in Azure Activity-logg med diagnostikinställningar – Azure Monitor | Microsoft-dokument
description: Använd diagnostikinställningar för att vidarebefordra Azure Activity-loggar till Azure Monitor Logs, Azure Storage eller Azure Event Hubs.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096907"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Uppdatera till Azure Activity log insamling och export
[Azure Activity-loggen](platform-logs-overview.md) är en [plattformslogg](platform-logs-overview.md) som ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Metoden för att skicka aktivitetsloggposter till [ett händelsenav eller lagringskonto](activity-log-export.md) eller till en [Log Analytics-arbetsyta](activity-log-collect.md) har ändrats för att använda [diagnostikinställningar](diagnostic-settings.md). I den här artikeln beskrivs skillnaden mellan metoderna och hur du rensar äldre inställningar som förberedelse för att ändra till diagnostikinställningar.


## <a name="differences-between-methods"></a>Skillnader mellan metoder

### <a name="advantages"></a>Fördelar
Att använda diagnostikinställningar har följande fördelar jämfört med de aktuella metoderna:

- Konsekvent metod för att samla in alla plattformsloggar.
- Samla in aktivitetslogg över flera prenumerationer och klienter.
- Filtrera insamling för att endast samla in loggar för vissa kategorier.
- Samla in alla aktivitetsloggkategorier. Vissa kategorier samlas inte in med äldre metod.
- Snabbare svarstid för logga inmatning. Den tidigare metoden har ca 15 minuters latens medan diagnostikinställningarna lägger till endast ca 1 minut.

### <a name="considerations"></a>Överväganden
Överväg följande information om aktivitetsloggsamling med hjälp av diagnostikinställningar innan du aktiverar den här funktionen.

- Bevarandeinställningen för att samla in aktivitetsloggen till Azure-lagring har tagits bort, vilket innebär att data lagras på obestämd tid tills du tar bort den.
- För närvarande kan du bara skapa en diagnostikinställning för prenumerationsnivå med Azure-portalen. Om du vill använda andra metoder som PowerShell eller CLI kan du skapa en Resource Manager-mall.


### <a name="differences-in-data"></a>Skillnader i data
Diagnostikinställningar samlar in samma data som de tidigare metoderna som användes för att samla in aktivitetsloggen med följande aktuella skillnader:

Följande kolumner har tagits bort. Ersättningen för dessa kolumner är i ett annat format, så du kan behöva ändra loggfrågor som använder dem. Du kan fortfarande se borttagna kolumner i schemat, men de fylls inte med data.

| Borttagen kolumn | Ersättningskolumn |
|:---|:---|
| Aktivitetsstatus    | ActivityStatusValue    |
| AktivitetSubstatus | ActivitySubstatusVärdera |
| OperationName     | OperationNameVärde     |
| ResourceProvider  | ResursProviderVärde  |

Följande kolumn har lagts till:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> I vissa fall kan värdena i dessa kolumner vara i versaler. Om du har en fråga som innehåller dessa kolumner bör du använda [operatorn =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) för att göra en okänslig jämförelse.

## <a name="work-with-legacy-settings"></a>Arbeta med äldre inställningar
Äldre inställningar för att samla in aktivitetsloggen fortsätter att fungera om du inte väljer att ersätta med en diagnostikinställning. Använd följande metod för att hantera loggprofilen för en prenumeration.

1. Välj **Aktivitetslogg**på **Azure Monitor-menyn** i Azure-portalen .
3. Klicka på **Diagnostikinställningar**.

   ![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klicka på den lila banderollen för äldreupplevelsen.

    ![Äldre erfarenhet](media/diagnostic-settings-subscription/legacy-experience.png)


Mer information om hur du använder äldre insamlingsmetoder finns i följande artiklar.

- [Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsytan i Azure Monitor](activity-log-collect.md)
- [Samla in Azure Activity-loggar i Azure Monitor för Azure Active Directory-klienter](activity-log-collect-tenants.md)
- [Exportera Azure Activity-logg till lagrings- eller Azure-händelsehubbar](activity-log-export.md)

## <a name="disable-existing-settings"></a>Inaktivera befintliga inställningar
Du bör inaktivera befintlig samling av aktiviteten innan du aktiverar den med hjälp av diagnostikinställningar. Om båda har aktiverat kan det resultera i dubblettdata.

### <a name="disable-collection-into-log-analytics-workspace"></a>Inaktivera insamling i Log Analytics-arbetsyta

1. Öppna menyn **Logganalysarbetsytor** i Azure-portalen och välj arbetsytan för att samla in aktivitetsloggen.
2. I avsnittet **Datakällor på arbetsytan** på arbetsytans meny väljer du **Azure Activity log**.
3. Klicka på den prenumeration som du vill koppla från.
4. Klicka på **Koppla från** och sedan **Ja** när du blir ombedd att bekräfta ditt val.

### <a name="disable-log-profile"></a>Inaktivera loggprofil

1. Använd proceduren som beskrivs i [Arbeta med äldre inställningar](#work-with-legacy-settings) för att öppna äldre inställningar.
2. Inaktivera en aktuell samling till lagrings- eller händelsehubbar.



## <a name="activity-log-monitoring-solution"></a>Övervakningslösning för aktivitetslogg
Övervakningslösningen för Azure Log Analytics innehåller flera loggfrågor och vyer för att analysera aktivitetsloggposterna på din Log Analytics-arbetsyta. Den här lösningen använder loggdata som samlats in på en Log Analytics-arbetsyta och fortsätter att fungera utan några ändringar om du samlar in aktivitetsloggen med hjälp av diagnostikinställningar. Mer information om den här lösningen finns i [övervakningslösningen för Aktivitetsloggar](activity-log-collect.md#activity-logs-analytics-monitoring-solution) Analytics.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om aktivitetsloggen](../../azure-resource-manager/management/view-activity-logs.md)
* [Läs mer om diagnostikinställningar](diagnostic-settings.md)
