---
title: Samla in Azure aktivitets logg med diagnostikinställningar (för hands version) – Azure Monitor | Microsoft Docs
description: Använd diagnostikinställningar för att vidarebefordra Azure-aktivitets loggar till Azure Monitor loggar, Azure Storage eller Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 12/20/2019
ms.openlocfilehash: e3a8048d8bcb8bb737c511a8b8bb6b57f9e81403
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750972"
---
# <a name="collect-azure-activity-log-with-legacy-settings"></a>Samla in Azure aktivitets logg med äldre inställningar
[Azure aktivitets loggen](platform-logs-overview.md) är en [plattforms logg](platform-logs-overview.md) som ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Tills nyligen har du skapat en logg profil för att skicka aktivitets logg poster till [ett händelsehubben eller lagrings konto](activity-log-export.md) och använde en koppling för att samla in dem i en [Log Analytics-arbetsyta](activity-log-collect.md). I den här artikeln beskrivs skillnaden mellan metoderna, hur du arbetar med befintliga äldre inställningar och hur du rensar äldre inställningar som förberedelse för diagnostikinställningar.


## <a name="differences-between-methods"></a>Skillnader mellan metoder

### <a name="advantages"></a>Fördelar
Att använda diagnostikinställningar har följande fördelar jämfört med nuvarande metoder:

- Konsekvent metod för att samla in alla plattforms loggar.
- Samla in aktivitets logg över flera prenumerationer och klienter.
- Filtrera samling för att endast samla in loggar för särskilda kategorier.
- Samla in alla aktivitets logg kategorier. Vissa kategorier samlas inte in med hjälp av en äldre metod.
- Snabbare svars tid för logg inmatning. Den föregående metoden har ungefär 15 minuters fördröjning medan diagnostikinställningar bara lägger till ungefär 1 minut.
  
### <a name="considerations"></a>Överväganden
Överväg följande information om aktivitets loggs insamling med hjälp av diagnostikinställningar innan du aktiverar den här funktionen.

- Inställningen kvarhållning för insamling av aktivitets loggen till Azure Storage har tagits bort, vilket innebär att data lagras oändligt tills du tar bort den.
- För närvarande kan du bara skapa en diagnostisk inställning på prenumerations nivå med hjälp av Azure Portal. Om du vill använda andra metoder, till exempel PowerShell eller CLI, kan du skapa en Resource Manager-mall.


### <a name="differences-in-data"></a>Skillnader i data
Diagnostikinställningar samlar in samma data som föregående metoder som används för att samla in aktivitets loggen med följande aktuella skillnader:

Följande egenskaper har tagits bort:

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

Följande egenskaper har lagts till:

- Authorization_d
- Claims_d
- Properties_d

## <a name="work-with-legacy-settings"></a>Arbeta med äldre inställningar
Äldre inställningar för att samla in aktivitets loggen fortsätter att fungera om du inte väljer att ersätta med en diagnostisk inställning. Använd följande metod för att hantera logg profilen för en prenumeration.

1. Välj **aktivitets logg**på **Azure Monitor** -menyn i Azure Portal.
3. Klicka på **Diagnostikinställningar**.
   
   ![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Klicka på den lila banderollen för den äldre upplevelsen. 

    ![Äldre miljö](media/diagnostic-settings-subscription/legacy-experience.png)


I följande artiklar finns information om hur du använder äldre samlings metoder.

- [Samla in och analysera Azure aktivitets loggar i Log Analytics arbets yta i Azure Monitor](activity-log-collect.md)
- [Samla in Azure-aktivitets loggar i Azure Monitor över Azure Active Directory klienter](activity-log-collect-tenants.md)
- [Exportera Azure aktivitets logg till lagring eller Azure Event Hubs](activity-log-export.md)

## <a name="disable-existing-settings"></a>Inaktivera befintliga inställningar
Du bör inaktivera en befintlig samling av aktiviteten innan du aktiverar den med hjälp av diagnostikinställningar. Om båda är aktiverade kan duplicerade data uppstå.

### <a name="disable-collection-into-log-analytics-workspace"></a>Inaktivera insamling i Log Analytics-arbetsyta

1. Öppna menyn **Log Analytics arbets ytor** i Azure Portal och välj arbets ytan för att samla in aktivitets loggen.
2. I avsnittet **data källor för arbets yta** på menyn för arbets ytan väljer du **Azure aktivitets logg**.
3. Klicka på den prenumeration som du vill koppla från.
4. Klicka på **Koppla från** och sedan **Ja** när du uppmanas att bekräfta ditt val.

### <a name="disable-log-profile"></a>Inaktivera logg profil

1. Använd proceduren som beskrivs i [arbeta med äldre inställningar](#work-with-legacy-settings) för att öppna äldre inställningar.
2. Inaktivera alla aktuella samlingar till lagrings-eller händelse nav. 



## <a name="activity-log-monitoring-solution"></a>Lösning för övervakning av aktivitets logg
Azure Log Analytics Monitoring-lösningen innehåller flera logg frågor och vyer för att analysera aktivitets logg poster i Log Analytics-arbetsytan. Den här lösningen använder loggdata som samlas in på en Log Analytics arbets yta och fortsätter att fungera utan några ändringar om du samlar in aktivitets loggen med hjälp av diagnostikinställningar. Mer information om den här lösningen finns i avsnittet [aktivitets loggar analys övervakning](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Nästa steg

* [Läs mer om aktivitets loggen](../../azure-resource-manager/resource-group-audit.md)
* [Läs mer om diagnostikinställningar](diagnostic-settings.md)
