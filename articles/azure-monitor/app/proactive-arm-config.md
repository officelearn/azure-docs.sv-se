---
title: Inställningar för smart identifieringsregel - Azure Application Insights
description: Automatisera hantering och konfiguration av smarta identifieringsregler för Azure Application Insights med Azure Resource Manager-mallar
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7ca4df620739b2ab55b8ba986031cc48fe87f1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294908"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Hantera smarta identifieringsregler för Program insights med Azure Resource Manager-mallar

Smarta identifieringsregler i Application Insights kan hanteras och konfigureras med Azure [Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md).
Den här metoden kan användas när du distribuerar nya Application Insights-resurser med Azure Resource Manager-automatisering eller för att ändra inställningarna för befintliga resurser.

## <a name="smart-detection-rule-configuration"></a>Konfiguration av smart identifieringsregel

Du kan konfigurera följande inställningar för en regel för smart identifiering:
- Om regeln är aktiverad (standardvärdet är **sant**.)
- Om e-postmeddelanden ska skickas till användare som är associerade till prenumerationens [roller övervakningsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) och [övervakningsdeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) när en identifiering hittas (standardvärdet är **sant**.)
- Eventuella ytterligare e-postmottagare som ska få ett meddelande när en identifiering hittas.
    -  E-postkonfiguration är inte tillgänglig för smarta identifieringsregler som markerats som _förhandsgranskning_.

Om du vill tillåta konfiguration av regelinställningarna via Azure Resource Manager är konfigurationen av smart identifiering nu tillgänglig som en inre resurs i application insights-resursen, med namnet **ProactiveDetectionConfigs**.
För maximal flexibilitet kan varje smart identifieringsregel konfigureras med unika meddelandeinställningar.

## <a name="examples"></a>Exempel

Nedan följer några exempel som visar hur du konfigurerar inställningarna för smarta identifieringsregler med Hjälp av Azure Resource Manager-mallar.
Alla exempel refererar till en Application Insights-resurs med namnet _"myApplication"_ och till den "långa regeln för smart identifiering av beroendevaraktighet", som internt heter _"longdependencyduration"._
Se till att ersätta application insights-resursnamnet och ange det interna namnet för den interna namn som är tillämplig för smart identifiering. I tabellen nedan finns en lista över motsvarande interna Azure Resource Manager-namn för varje smart identifieringsregel.

### <a name="disable-a-smart-detection-rule"></a>Inaktivera en smart identifieringsregel

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Inaktivera att skicka e-postmeddelanden för en smart identifieringsregel

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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Lägga till ytterligare e-postmottagare för en smart identifieringsregel

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


## <a name="smart-detection-rule-names"></a>Namn på smarta identifieringsregeler

Nedan visas en tabell med smarta identifieringsregelnamn som de visas i portalen, tillsammans med deras interna namn, som ska användas i Azure Resource Manager-mallen.

> [!NOTE]
> Smarta identifieringsregler som markerats som _förhandsversion_ stöder inte e-postmeddelanden. Därför kan du bara ange den _aktiverade_ egenskapen för dessa regler. 

| Namn på Azure-portalregel | Internt namn
|:---|:---|
| Långsam inläsningstid för sidan | långsam körningsladdningstid |
| Långsam svarstid för servern | långsamserversvartid |
| Lång beroendevaraktighet | långberoenden |
| Försämring av serverns svarstid | försämras på serverns svarstid |
| Försämring av beroendevaraktighet | försämrad oberoende |
| Nedbrytning i spår allvarlighetsgrad (förhandsgranskning) | extension_traceseveritydetector |
| Onormal ökning av undantagsvolymen (förhandsgranskning) | extension_exceptionchangeextension |
| Potentiell minnesläcka har upptäckts (förhandsgranskning) | extension_memoryleakextension |
| Potentiella säkerhetsproblem har upptäckts (förhandsgranskning) | extension_securityextensionspackage |
| Onormal ökning av den dagliga datavolymen (förhandsgranskning) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Varningsregel för avvikelser vid fel

Den här Azure Resource Manager-mallen visar att konfigurera en aviseringsregel för felavvikelser med en allvarlighetsgrad på 2. Den här nya versionen av aviseringsregeln Felavvikelser är en del av den nya Azure-aviseringsplattformen och ersätter den klassiska versionen som dras tillbaka som en del av den [klassiska aviseringsprocessen](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

> [!NOTE]
> Felavvikelser är en global tjänst därför regelplats skapas på den globala platsen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
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
> Den här Azure Resource Manager-mallen är unik för aviseringsregeln Felavvikelser och skiljer sig från de andra klassiska smarta identifieringsreglerna som beskrivs i den här artikeln. Om du vill hantera felavvikelser manuellt görs detta i Azure Monitor Alerts medan alla andra smarta identifieringsregler hanteras i fönstret Smart identifiering i användargränssnittet.

## <a name="next-steps"></a>Efterföljande moment

Läs mer om att automatiskt identifiera:

- [Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Minnesläckor](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md)
