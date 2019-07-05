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
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477868"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Hantera regler för smart identifiering för Application Insights med hjälp av Azure Resource Manager-mallar

Regler för smart identifiering i Application Insights kan hanteras och konfigureras med [Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md).
Den här metoden kan användas när du distribuerar nya Application Insights-resurser med Azure Resource Manager automation eller för att ändra inställningarna för befintliga resurser.

## <a name="smart-detection-rule-configuration"></a>Regelkonfiguration för smart identifiering

Du kan konfigurera följande inställningar för en regel för smart identifiering:
- Om regeln är aktiverad (standard är **SANT**.)
- Om e-postmeddelanden ska skickas till användare som är kopplad till prenumerationens [övervakning läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) och [övervakning deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) roller när en identifiering hittas (standardinställningen är **SANT**.)
- Eventuella ytterligare e-mottagare som ska få ett meddelande när en identifiering hittas.
    -  E-postkonfigurationen är inte tillgänglig för regler för Smart identifiering märkta _förhandsversion_.

För att konfigurera regelinställningar för via Azure Resource Manager måste regelkonfigurationen för smart identifiering är nu tillgänglig som en inre resurs i Application Insights-resurs med namnet **ProactiveDetectionConfigs**.
Varje regel för smart identifiering kan konfigureras med unika meddelandeinställningar för maximal flexibilitet.

## 

## <a name="examples"></a>Exempel

Nedan visas några exempel som visar hur du konfigurerar inställningarna för regler för smart identifiering med hjälp av Azure Resource Manager-mallar.
Alla exempel referera till en Application Insights-resurs med namnet _”myApplication”_ , och för ”långa beroende varaktighet smart identifiering regeln”, internt namngiven _”longdependencyduration”_ .
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

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Varningsregel för misslyckat avvikelser v2 (inte klassisk)

Azure Resource Manager-mallen visar konfigurera en aviseringsregel för Felavvikelser-v2 med en allvarlighetsgrad 2. Den här nya versionen av Felavvikelser varningsregeln är en del av den nya Azure avisering plattform och ersätter den klassiska versionen som dras som en del av den [klassisk aviseringar tillbakadragande processen](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Azure Resource Manager-mallen är unika för varningsregeln Felavvikelser v2 och skiljer sig från de andra klassiska reglerna för Smart identifiering beskrivs i den här artikeln.   

## <a name="smart-detection-rule-names"></a>Namn på smart identifiering

Nedan visas en tabell med smart identifiering namn som de visas i portalen, tillsammans med sina interna namn som ska användas i Azure Resource Manager-mallen.

> [!NOTE]
> Regler för smart identifiering märkta _förhandsversion_ stöder inte e-postmeddelanden. Därför kan du kan bara ange den _aktiverat_ -egenskapen för de här reglerna. 

| Azure portal Regelnamn | Internt namn
|:---|:---|
| Lång sidinläsningstid | slowpageloadtime |
| Lång serversvarstid | slowserverresponsetime |
| Lång beroendevaraktighet | longdependencyduration |
| Försämring i svarstid för servern | degradationinserverresponsetime |
| Beroendets varaktighet | degradationindependencyduration |
| Försämring i spårningen allvarlighetsgrad-förhållande (förhandsversion) | extension_traceseveritydetector |
| Onormal uppgång av undantagsvolym (förhandsversion) | extension_exceptionchangeextension |
| Potentiell minnesläcka har identifierats (förhandsversion) | extension_memoryleakextension |
| Potentiella säkerhetsproblem har identifierats (förhandsversion) | extension_securityextensionspackage |
| Onormal uppgång av dagliga datavolymen (förhandsversion) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att automatiskt identifiera:

- [Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Minnesläckor](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)
