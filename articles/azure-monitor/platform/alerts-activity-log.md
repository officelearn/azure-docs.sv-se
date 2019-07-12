---
title: Skapa, visa och hantera aviseringar för aktivitetsloggar i Azure Monitor
description: Skapa aviseringar för aktivitetsloggen med hjälp av Azure-portalen, en Azure Resource Manager-mall och Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705284"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Skapa, visa och hantera aviseringar för aktivitetsloggar med hjälp av Azure Monitor  

## <a name="overview"></a>Översikt
Aktivitetsloggsaviseringar är de aviseringar som aktiveras när en ny händelse i aktivitetsloggen inträffar som matchar de villkor som anges i aviseringen.

Dessa aviseringar är för Azure-resurser och kan skapas med hjälp av en Azure Resource Manager-mall. De kan också vara skapas, uppdateras eller tas bort i Azure-portalen. Normalt kan skapa du aviseringar för aktivitetsloggen att ta emot meddelanden när specifika ändras till resurser i din Azure-prenumeration. Aviseringar är ofta begränsade till enskilda resursgrupper eller resurser. Du kanske exempelvis vill meddelas när någon virtuell dator i resursgruppen exempel **myProductionResourceGroup** tas bort. Eller så kanske du vill få aviseringar om eventuella nya roller har tilldelats en användare i din prenumeration.

> [!IMPORTANT]
> Aviseringar på aviseringen om tjänstens hälsa kan inte skapas via gränssnittet för skapande av aktivitet log varning. Läs mer om hur du skapar och använder meddelanden om hälsostatus för tjänsten i [ta emot varningar för aktivitetsloggar för health tjänstmeddelanden](alerts-activity-log-service-notifications.md).

När du skapar aviseringsregler kan du kontrollera följande:

- Prenumerationen i omfånget är inte skiljer sig från prenumerationen där aviseringen har skapats.
- Villkoren måste vara nivå, status, anroparen, resursgrupp, resurs-ID eller resurs typ händelsekategori som aviseringen konfigureras.
- Det finns inga ”anyOf” villkor eller kapslade villkor i varningskonfigurationen JSON. I princip tillåts endast ett ”allOf” villkor utan ytterligare ”allOf” eller ”anyOf” villkor.
- När kategorin är ”administrativa” måste du ange minst en av de föregående kriterierna i aviseringen. Du kan inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.


## <a name="azure-portal"></a>Azure Portal

Du kan använda Azure-portalen för att skapa och ändra aktivitetsloggsaviseringsregler. Den är integrerad med en Azure-aktivitetsloggen att säkerställa sömlös skapande av varning för specifika händelser av intresse.

### <a name="create-with-the-azure-portal"></a>Skapa med Azure portal

Följ anvisningarna nedan.

1. I Azure-portalen väljer du **övervakaren** > **aviseringar**.
2. Välj **ny aviseringsregel** i det övre vänstra hörnet av den **aviseringar** fönster.

     ![Ny aviseringsregel](media/alerts-activity-log/AlertsPreviewOption.png)

     Den **skapa regeln** fönster visas.

      ![nya alternativ för varningsregel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Under **definiera aviseringsvillkoret**, ange följande information och välj **klar**:

   - **Aviseringsmål:** Om du vill visa och välja målet för den nya aviseringen, använda **filtrera efter prenumeration** / **filtrera efter resurstyp**. Välj en resurs eller resursgrupp i listan som visas.

     > [!NOTE]
     > 
     > Du kan välja en resurs, resursgrupp eller en hela prenumerationen för en aktivitetsloggsignal.

     **Aviseringsmål exempelvy**

     ![Välja mål](media/alerts-activity-log/select-target.png)

   - Under **rikta kriterier**väljer **lägga till villkor**. Alla tillgängliga signaler för mål är visas, som innehåller de från olika kategorier av **aktivitetsloggen**. Kategorinamnet läggs till i **Monitor Service** namn.

   - Välj signalen från listan som visas på olika åtgärder som möjligt för typen **aktivitetsloggen**.

     Du kan välja log historik tidslinje och motsvarande avisering logiken för signalen mål:

     **Lägg till kriterier skärm**

     ![Lägg till villkor](media/alerts-activity-log/add-criteria.png)

     - **Historik över tid**: Händelser som är tillgängliga för den valda åtgärden kan ritas över de senaste 6, 12 eller 24 timmarna eller under den senaste veckan.

     - **Avisera logic**:

       - **Händelsenivå**: Allvarlighetsgrad för händelsen: _Utförlig_, _endast i informationssyfte_, _varning_, _fel_, eller _kritiska_.
       - **Status**: Status för händelsen: _Igång_, _misslyckades_, eller _lyckades_.
       - **Händelsen initierad av**: Även känt som anroparen. E-postadress eller Azure Active Directory-ID för användaren som utförde åtgärden.

       Det här exemplet signalen diagrammet har aviseringen logiken tillämpas:

       ![kriterier som har valts](media/alerts-activity-log/criteria-selected.png)

4. Under **definiera Aviseringsinformationen**, anger du följande information:

    - **Namn på aviseringsregel**: Namnet på den nya aviseringsregeln.
    - **Beskrivning**: Beskrivning för den nya aviseringsregeln.
    - **Spara aviseringen till resursgruppen**: Välj den resursgrupp där du vill spara den nya regeln.

5. Under **åtgärdsgrupp**, ange den grupp som du vill tilldela till den här nya aviseringsregeln från den nedrullningsbara menyn. Eller, [skapa en ny åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) och tilldela den till den nya regeln. Om du vill skapa en ny grupp, Välj **+ ny grupp**.

6. Om du vill aktivera regler när du har skapat, Välj **Ja** för den **aktivera regeln vid skapande** alternativet.
7. Välj **skapa varningsregel**.

    Den nya aviseringsregeln för aktivitetsloggen har skapats och ett bekräftelsemeddelande visas i det övre högra hörnet i fönstret.

    Du kan aktivera, inaktivera, redigera eller ta bort en regel. Läs mer om hur du hanterar aktivitet log regler.


En enkel liknelse för förstå villkor som Varningsregler kan skapas i en aktivitetslogg är att utforska eller Filtrera händelser via den [aktivitetsloggen i Azure-portalen](activity-log-view.md#azure-portal). I den **Azure Monitor - aktivitetsloggen** skärmen, du kan filtrera eller hitta händelsen som behövs och sedan skapa en avisering med hjälp av den **Lägg till aktivitetsloggavisering** knappen. Följ steg 4 till 7 som tidigare visas.
    
 ![Lägg till avisering från aktivitetsloggen](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visa och hantera i Azure portal

1. I Azure-portalen väljer du **övervakaren** > **aviseringar**. Välj **hantera Varningsregler** i det övre vänstra hörnet i fönstret.

    ![Hantera Varningsregler](media/alerts-activity-log/manage-alert-rules.png)

    Lista över tillgängliga regler visas.

2. Söka efter log-regel aktiviteten för att ändra.

    ![Sök aktivitetsloggsaviseringsregler](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Du kan använda de tillgängliga filtren _prenumeration_, _resursgrupp_, _Resource_, _signalera typ_, eller _Status_ , för att hitta aktiviteten regeln som du vill redigera.

   > [!NOTE]
   > 
   > Du kan bara redigera **beskrivning**, **rikta kriterier**, och **åtgärdsgrupper**.

3. Väljer du regeln och dubbelklicka för att redigera regelalternativ för. Gör nödvändiga ändringar och välj sedan **spara**.

   ![Hantera Varningsregler](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Du kan aktivera, inaktivera eller ta bort en regel. Välj lämpligt alternativ överst i fönstret när du har valt regeln enligt beskrivningen i steg 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Om du vill skapa en aktivitetsloggavisering med en Azure Resource Manager-mall kan du skapa en resurs av typen `microsoft.insights/activityLogAlerts`. Du fylla i alla relaterade egenskaper. Här är en mall som skapar en aktivitetsloggavisering:

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
I föregående exempel JSON kan sparas som till exempel sampleActivityLogAlert.json i den här genomgången och kan distribueras med hjälp av [Azure Resource Manager i Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Det kan ta upp till 5 minuter för den nya aktivitetsloggsaviseringsregel att bli aktiv.

## <a name="rest-api"></a>REST-API 
Den [Azure Monitor aktivitet Log aviseringar API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) är ett REST-API. Det är helt kompatibel med Azure Resource Manager REST API. Den kan användas via PowerShell genom att använda cmdleten Resource Manager eller Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Distribuera Resource Manager-mall med PowerShell
Du använder PowerShell för att distribuera exempelmallen för Resource Manager visas i föregående [Azure Resource Manager-mall](#azure-resource-manager-template) använder du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

sampleActivityLogAlert.parameters.json innehåller där de angivna värdena för de parametrar som behövs för att skapa en aviseringsregel.

### <a name="use-activity-log-powershell-cmdlets"></a>Använda aktivitetslogg PowerShell-cmdletar

Aktivitetsloggaviseringar ha dedikerade PowerShell-cmdlets som är tillgängliga:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Skapar en ny aktivitetsloggavisering eller uppdaterar en befintlig aktivitetsloggavisering.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Hämtar en eller flera aktivitet log aviseringsresurserna.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Gör det möjligt för en befintlig aktivitetsloggavisering och anger dess taggar.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Inaktiverar en befintlig aktivitetsloggavisering och anger dess taggar.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Tar bort en aktivitetsloggavisering.

## <a name="azure-cli"></a>Azure CLI

Dedikerade Azure CLI-kommandon under uppsättningen [az monitor-aktivitetsloggaviseringen](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) är tillgängliga för att hantera aktivitetsloggsaviseringsregler.

Om du vill skapa en ny aviseringsregeln för aktivitetsloggen, använder du följande kommandon i den här ordningen:

1. [Skapa AZ monitor-aktivitetsloggaviseringen](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Skapa en ny aktivitet log varningsregel resurs.
1. [AZ monitor aktivitetsloggen avisering omfång](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Lägg till omfattning för skapade aktivitetsloggsaviseringsregel.
1. [AZ monitor aktivitetsloggen avisering-åtgärdsgrupp](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Lägg till en åtgärdsgrupp att aviseringsregeln för aktivitetsloggen.

Använd Azure CLI-kommando för att hämta en aktivitet varningsregel loggresurs, [az monitor-aktivitetsloggen alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Du kan visa alla aktivitet log varningsregel resurser i en resursgrupp [az monitor-aktivitetsloggen Aviseringslista](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Aktivitet log varningsregel resurser kan tas bort med hjälp av Azure CLI-kommando [az monitor-aktivitetsloggen borttagning](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [webhook-schema aktivitetsloggar](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Läs en [översikt över aktivitetsloggar](../../azure-monitor/platform/activity-log-alerts.md).
- Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).  
- Lär dig mer om [service health meddelanden](../../azure-monitor/platform/service-notifications.md).
