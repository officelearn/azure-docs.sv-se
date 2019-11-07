---
title: Samla in Azure aktivitets logg med diagnostikinställningar (för hands version) – Azure Monitor | Microsoft Docs
description: Använd diagnostikinställningar för att vidarebefordra Azure-aktivitets loggar till Azure Monitor loggar, Azure Storage eller Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 9f8783dc6d3c14b086364639b60273dbae626cee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587984"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>Samla in Azure aktivitets logg med diagnostikinställningar (förhands granskning)
[Azure aktivitets loggen](activity-logs-overview.md) är en [plattforms logg](platform-logs-overview.md) som ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Fram till nu har du skapat en logg profil för att skicka aktivitets logg poster till [ett händelsehubben eller lagrings konto](activity-log-export.md) och använde en koppling för att samla in dem i en [Log Analytics-arbetsyta](activity-log-collect.md).

Nu kan du konfigurera insamling av Azure aktivitets loggen med samma [diagnostikinställningar](diagnostic-settings.md) som används för att samla in [resurs loggar](resource-logs-overview.md). Att använda diagnostikinställningar har följande fördelar jämfört med nuvarande metoder:

- Konsekvent metod för att samla in alla plattforms loggar.
- Samla in aktivitets logg över flera prenumerationer och klienter.
- Filtrera samling för att endast samla in loggar för särskilda kategorier.

## <a name="considerations"></a>Överväganden
Överväg följande information om aktivitets loggs insamling med hjälp av diagnostikinställningar innan du aktiverar den här funktionen.

- Du bör inaktivera en befintlig samling av aktiviteten innan du aktiverar den med hjälp av diagnostikinställningar. Om båda är aktiverade kan duplicerade data uppstå.
- Inställningen kvarhållning för insamling av aktivitets loggen till Azure Storage har tagits bort, vilket innebär att data lagras oändligt tills du tar bort den.
- Du kan skicka aktivitets loggen från flera prenumerationer till en enda Log Analytics-arbetsyta. Du kan skicka aktivitets loggen från en enskild prenumeration till upp till fem Log Analytics arbets ytor.

## <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar
Använd följande procedur för att skapa en diagnostisk inställning i Azure Portal för att samla in Azure aktivitets loggen. Du kan för närvarande inte skapa en diagnostisk inställning på prenumerations nivå med andra metoder.

1. Inaktivera den befintliga Log Analytics arbets ytans samling för aktivitets loggen:
   1. Öppna menyn **Log Analytics arbets ytor** i Azure Portal och välj arbets ytan för att samla in aktivitets loggen.
   2. I avsnittet **data källor för arbets yta** på menyn för arbets ytan väljer du **Azure aktivitets logg**.
   3. Klicka på den prenumeration som du vill koppla från.
   4. Klicka på **Koppla från** och sedan **Ja** när du uppmanas att bekräfta ditt val.
2. Välj **aktivitets logg**på **Azure Monitor** -menyn i Azure Portal.
3. Klicka på **Diagnostikinställningar**.
   
   ![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Klicka på den lila banderollen om du vill ha en äldre upplevelse och inaktivera alla aktuella samlingar för lagring eller händelse nav. 

    ![Äldre miljö](media/diagnostic-settings-subscription/legacy-experience.png)

5. Följ procedurerna i [skapa diagnostikinställningar i Azure Portal](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) för att skapa en diagnostisk inställning. Se [Kategorier i aktivitets loggen](activity-logs-overview.md#categories-in-the-activity-log) för att få en förklaring av de kategorier som du kan använda för att filtrera händelser från aktivitets loggen. 


## <a name="differences-in-data"></a>Skillnader i data
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

## <a name="activity-log-monitoring-solution"></a>Lösning för övervakning av aktivitets logg
Azure Log Analytics Monitoring-lösningen innehåller flera logg frågor och vyer för att analysera aktivitets logg poster i Log Analytics-arbetsytan. Den här lösningen använder loggdata som samlas in på en Log Analytics arbets yta och fortsätter att fungera utan några ändringar om du samlar in aktivitets loggen med hjälp av diagnostikinställningar. Mer information om den här lösningen finns i avsnittet [aktivitets loggar analys övervakning](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Nästa steg

* [Läs mer om aktivitets loggen](../../azure-resource-manager/resource-group-audit.md)
* [Läs mer om diagnostikinställningar](diagnostic-settings.md)
