---
title: Konfigurera inställningar för Azure Application Insights smart identifiering regeln med Azure Resource Manager-mallar | Microsoft Docs
description: Automatisera hantering och konfiguration av regler för smart identifiering av Azure Application Insights med Azure Resource Manager-mallar
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/19/2018
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: b1f4d278079b81b4a224dc4712426d1f078de110
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020412"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Hantera regler för smart identifiering för Application Insights med hjälp av Azure Resource Manager-mallar

Regler för smart identifiering i Application Insights kan hanteras och konfigureras med [Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md).
Den här metoden kan användas när du distribuerar nya Application Insights-resurser med Azure Resource Manager automation eller för att ändra inställningarna för befintliga resurser.

## <a name="smart-detection-rule-configuration"></a>Regelkonfiguration för smart identifiering

Du kan konfigurera följande inställningar för en regel för smart identifiering:
- Om regeln är aktiverad (standard är **SANT**.)
- Om e-postmeddelanden ska skickas till prenumerationsägare, deltagare och läsare när en identifiering hittas (standardinställningen är **SANT**.)
- Eventuella ytterligare e-mottagare som ska få ett meddelande när en identifiering hittas.

För att konfigurera regelinställningar för via Azure Resource Manager måste regelkonfigurationen för smart identifiering är nu tillgänglig som en inre resurs i Application Insights-resurs med namnet **ProactiveDetectionConfigs**.
Varje regel för smart identifiering kan konfigureras med unika meddelandeinställningar för maximal flexibilitet.

## <a name="examples"></a>Exempel

Nedan visas några exempel som visar hur du konfigurerar inställningarna för regler för smart identifiering med hjälp av Azure Resource Manager-mallar.
Alla exempel referera till en Application Insights-resurs med namnet _”myApplication”_, och för ”långa beroende varaktighet smart identifiering regeln”, internt namngiven _”longdependencyduration”_.
Se till att ersätta resursnamnet för Application Insights och för att ange det interna namnet för relevanta smart identifiering-regel. Kontrollera i tabellen nedan för en lista över de motsvarande interna Azure Resource Manager-namnen för varje regel för smart identifiering.

### <a name="disable-a-smart-detection-rule"></a>Inaktivera en regel för smart identifiering

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Inaktivera skicka e-postmeddelanden för en regel för smart identifiering

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Lägg till ytterligare e-postmottagare för en regel för smart identifiering

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

## <a name="smart-detection-rule-names"></a>Namn på smart identifiering

Nedan visas en tabell med smart identifiering namn som de visas i portalen, tillsammans med sina interna namn som ska användas i Azure Resource Manager-mallen.

> [!NOTE]
> Regler för smart identifiering markerats som förhandsversion inte stöder e-postmeddelanden. Du kan därför bara ange egenskapen enabled för dessa regler. 

| Azure portal Regelnamn | Internt namn
|:---|:---|
| Lång sidinläsningstid | slowpageloadtime |
| Lång serversvarstid | slowserverresponsetime |
| Lång beroendevaraktighet | longdependencyduration |
| Försämrad serversvarstid | degradationinserverresponsetime |
| Degradering av beroendets varaktighet | degradationindependencyduration |
| Försämring i spårningen allvarlighetsgrad-förhållande (förhandsversion) | extension_traceseveritydetector |
| Onormal uppgång av undantagsvolym (förhandsversion) | extension_exceptionchangeextension |
| Potentiell minnesläcka har identifierats (förhandsversion) | extension_memoryleakextension |
| Potentiella säkerhetsproblem har identifierats (förhandsversion) | extension_securityextensionspackage |
| Resursen användning problem har identifierats (förhandsversion) | extension_resourceutilizationextensionspackage |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att automatiskt identifiera:

- [Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Minnesläckor](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)