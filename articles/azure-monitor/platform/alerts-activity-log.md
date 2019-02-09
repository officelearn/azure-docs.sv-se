---
title: Skapa, visa och hantera aviseringar för aktivitetsloggar i Azure Monitor
description: Hur du skapar varningar för aktivitetsloggar från Azure Portal, Resource-mall och PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: 17a7b0e0ee76490ef43fb869260dceef83cbd124
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984502"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Skapa, visa och hantera aviseringar för aktivitetsloggar med Azure Monitor  

## <a name="overview"></a>Översikt
Aktivitetsloggsaviseringar är de aviseringar som aktiveras när en ny händelse i aktivitetsloggen inträffar som matchar de villkor som anges i aviseringen.

Dessa aviseringar är för Azure-resurser kan skapas med hjälp av en Azure Resource Manager-mall. De kan också vara skapas, uppdateras eller tas bort i Azure-portalen. Normalt kan skapa du aviseringar för aktivitetsloggen att ta emot meddelanden när ändringar görs i resurser i din Azure-prenumeration som omfattar ofta enskilda resursgrupper eller resurs. Du kanske exempelvis vill meddelas när någon virtuell dator i (exempel resursgrupp) **myProductionResourceGroup** har tagits bort, eller kanske du vill få aviseringar om eventuella nya roller har tilldelats en användare i din prenumeration.

> [!IMPORTANT]
> Går inte att skapa aviseringar för Service Health-meddelande via gränssnittet för skapande av aktivitet log varning. Läs mer om att skapa och använda meddelanden om hälsostatus för tjänsten i [ta emot varningar för aktivitetsloggar för health tjänstmeddelanden](alerts-activity-log-service-notifications.md).

## <a name="azure-portal"></a>Azure Portal

> [!NOTE]

>  När du skapar varningsreglerna måste du kontrollera följande:

> - Prenumerationen i omfånget är inte skiljer sig från prenumerationen där aviseringen har skapats.
- Villkor måste vara nivå/status/anroparen / resursgrupp / resurs-id / resurstyp / händelsekategori som aviseringen konfigureras.
- Det finns ingen ”anyOf” villkor eller kapslade villkor i varningskonfigurationen JSON (i princip bara en allOf tillåts med inga ytterligare allOf/anyOf).
- När är kategorin ”administrativa”. Du måste ange minst en av de föregående kriterierna i aviseringen. Du kan inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.

### <a name="create-with-azure-portal"></a>Skapa med Azure portal

Följ dessa steg:

1. Azure-portalen väljer du **övervakaren** > **aviseringar**
2. Klicka på **ny Aviseringsregel** överst i den **aviseringar** fönster.

     ![Ny aviseringsregel](media/alerts-activity-log/AlertsPreviewOption.png)

     Den **skapa regeln** fönster visas.

      ![nya alternativ för varningsregel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **Definiera avisering villkor** anger följande information och klickar på **klar**.

    - **Aviseringsmål:** Om du vill visa och välja målet för den nya aviseringen, använda **filtrera efter prenumeration** / **filtrera efter resurstyp** och välj en resurs eller resursgrupp från listan som visas.

    > [!NOTE]

    > Du kan välja en resurs, resursgrupp eller en hel aktivitetsloggsignal-prenumeration.

    **Avisera target exempelvy** ![Välj mål](media/alerts-activity-log/select-target.png)

    - Under **Target kriterier**, klickar du på **lägga till villkor** och alla tillgängliga signaler för målet visas även de från olika kategorier av **aktivitetsloggen**; Kategorinamn sist i **Monitor Service** namn.

    - Välj signalen från listan som visas på olika åtgärder som möjligt för typen **aktivitetsloggen**.

    Du kan välja log historik tidslinje och motsvarande avisering logiken för signalen mål:

    **Lägg till kriterier skärm**

    ![Lägg till villkor](media/alerts-activity-log/add-criteria.png)

    **Historik över tid**: Händelser som är tillgängliga för valda åtgärden är kan ritas över de senaste 6/12/24 timmar (eller) under den senaste veckan.

    **Avisera logic**:

     - **Händelsenivå**-allvarlighetsgrad för händelsen. _Utförlig_, _endast i informationssyfte_, _varning_, _fel_, eller _kritiska_.
     - **Status**: Status för händelsen. _Igång_, _misslyckades_, eller _lyckades_.
     - **Händelsen initierad av**: Även känt som anroparen; E-postadress eller Azure Active Directory-ID för användaren som utförde åtgärden.

        Exemplet signalen diagram med aviseringslogiken tillämpas:

        ![ kriterier som har valts](media/alerts-activity-log/criteria-selected.png)

4. Under **definiera Varningsregler information**, anger du följande information:

    - **Namn på aviseringsregel** – namn för den nya aviseringsregeln
    - **Beskrivning av** – beskrivning för den nya aviseringsregeln
    - **Spara aviseringen till resursgruppen** – Välj den resursgrupp där du vill spara den nya regeln.

5. Under **åtgärdsgrupp**, ange den grupp som du vill tilldela till den här nya aviseringsregeln från den nedrullningsbara menyn. Du kan också [skapa en ny åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) och tilldela den nya regeln. Om du vill skapa en ny grupp, klickar du på **+ ny grupp**.

6. Om du vill aktivera regler när du har skapat, klickar du på **Ja** för **aktivera regeln vid skapande** alternativet.
7. Klicka på **skapa varningsregel**.

    Den nya aviseringsregeln för aktivitetsloggen har skapats och ett bekräftelsemeddelande visas längst upp höger i fönstret.

    Du kan aktivera, inaktivera, redigera eller ta bort en regel. Läs mer om hur du hanterar aktivitet log regler.


Du kan också en enkel liknelse för förstå villkor som Varningsregler kan skapas på aktivitetslogg, är att utforska eller Filtrera händelser via [aktivitetsloggen i Azure-portalen](../../azure-monitor/platform/activity-logs-overview.md#query-the-activity-log-in-the-azure-portal). I Azure Monitor - aktivitetslogg, en filtrera eller hitta nödvändiga evenemang och sedan skapa en avisering med hjälp av den **Lägg till aktivitetsloggavisering** knappen, Följ steg 4 och senare enligt informationen i självstudien ovan.
    
 ![ Lägg till avisering från aktivitetsloggen](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Visa och hantera Azure-portalen

1. Från Azure-portalen klickar du på **övervakaren** > **aviseringar** och klicka på **hantera regler** på upp till vänster i fönstret.

    ![ Hantera Varningsregler](media/alerts-activity-log/manage-alert-rules.png)

    Lista över tillgängliga regler visas.

2. Sök log-regel aktiviteten för att ändra.

    ![ Sök aktivitetsloggsaviseringsregler](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Du kan använda de tillgängliga filtren - _prenumeration_, _resursgrupp_, _Resource_, _signaltyp_, eller _Status_  att hitta aktiviteten regeln som du vill redigera.

    > [!NOTE]

    > Du kan bara redigera **beskrivning** , **rikta kriterier** och **åtgärdsgrupper**.

3.  Väljer du regeln och dubbelklicka för att redigera regelalternativ för. Gör nödvändiga ändringar och klicka sedan på **spara**.

    ![ Hantera Varningsregler](media/alerts-activity-log/activity-log-rule-edit-page.png)

4.  Du kan inaktivera, aktivera eller ta bort en regel. Välj lämpligt alternativ överst i fönstret när du har valt regeln som beskrivs i steg 2.


## <a name="azure-resource-template"></a>Azure Resource Template
Om du vill skapa en aktivitetsloggavisering med en Resource Manager-mall kan du skapa en resurs av typen `microsoft.insights/activityLogAlerts`. Du fylla i alla relaterade egenskaper. Här är en mall som skapar en aktivitetsloggavisering.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Exempel-json ovan kan sparas som (exempelvis) sampleActivityLogAlert.json i den här genomgången och kan distribueras med hjälp av [Azure Resource Manager i Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Det kan ta upp till 5 minuter för den en ny aktivitetsloggsaviseringsregel att bli aktiv

## <a name="rest-api"></a>REST-API 
[Azure Monitor - aktivitet Log aviseringar API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) är en REST-API och helt kompatibla med Azure Resource Manager REST API. Därför kan den användas via Powershell med Resource Manager-cmdlet som Azure CLI.

## <a name="powershell"></a>PowerShell

Aktivitetsloggaviseringar ha dedikerade PowerShell-cmdlets som är tillgängliga:

- [Set-AzureRmActivityLogAlert](https://docs.microsoft.com/powershell/module/azurerm.insights/set-azurermactivitylogalert): Att skapa en ny eller uppdatera befintlig aktivitet varningsregel loggresurs
- [Get-AzureRmActivityLogAlert](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermactivitylogalert): Att hämta en eller flera aktivitet log varningsregel resurser
- [Remove-AzureRmActivityLogAlert](https://docs.microsoft.com/powershell/module/azurerm.insights/remove-azurermactivitylogalert): Att ta bort aktivitet varningsregel loggresurs med Användarbekräftelse
- [Enable-AzureRmActivityLogAlert](https://docs.microsoft.com/powershell/module/azurerm.insights/enable-azurermactivitylogalert): Så här aktiverar du en befintlig aktivitet log varningsregel resurs
- [Disable-AzureRmActivityLogAlert](https://docs.microsoft.com/powershell/module/azurerm.insights/disable-azurermactivitylogalert): Inaktivera en befintlig aktivitet log varningsregel resurs

## <a name="cli"></a>CLI

Dedikerade Azure CLI-kommandon under uppsättningen [az monitor-aktivitetsloggaviseringen](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) är tillgängliga för att hantera aktivitetsloggsaviseringsregler.

Om du vill skapa en ny aktivitetsloggsaviseringsregel att använda i den här ordningen:

1. [Skapa AZ monitor-aktivitetsloggaviseringen](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Skapa ny aktivitet varningsregel loggresurs
1. [AZ monitor aktivitetsloggen avisering omfång](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Lägg till omfattning för skapade aktivitetsloggsaviseringsregel
1. [AZ monitor aktivitetsloggen avisering-åtgärdsgrupp](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Lägg till åtgärdsgrupp aviseringsregeln för aktivitetsloggen

Att hämta en enda aktivitet log varningsregel resurs, Azure CLI-kommando [az monitor-aktivitetsloggen alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) kan användas. Och för att visa alla aktivitet varningsregel loggresurs i en resursgrupp, använda [az monitor-aktivitetsloggen Aviseringslista](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Aktivitet log varningsregel resurser kan tas bort med hjälp av Azure CLI-kommando [az monitor-aktivitetsloggen borttagning](https://docs.microsoft.com/en-us/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Nästa steg

- [Webhook-schema aktivitetsloggar](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Översikt över aktivitetsloggar](../../azure-monitor/platform/activity-log-alerts.md) 
- Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).  
- Lär dig mer om [service health meddelanden](../../azure-monitor/platform/service-notifications.md).
