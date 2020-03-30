---
title: Skapa, visa och hantera aktivitetsloggaviseringar i Azure Monitor
description: Skapa aktivitetsloggaviseringar med hjälp av Azure-portalen, en Azure Resource Manager-mall och Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132394"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Skapa, visa och hantera aktivitetsloggaviseringar med hjälp av Azure Monitor  

## <a name="overview"></a>Översikt

Aktivitetsloggaviseringar är de aviseringar som aktiveras när en ny aktivitetslogghändelse inträffar som matchar de villkor som anges i aviseringen.

Dessa aviseringar är för Azure-resurser och kan skapas med hjälp av en Azure Resource Manager-mall. De kan också skapas, uppdateras eller tas bort i Azure-portalen. Vanligtvis skapar du aktivitetsloggaviseringar för att ta emot meddelanden när specifika ändringar sker på resurser i din Azure-prenumeration. Aviseringar begränsas ofta till särskilda resursgrupper eller resurser. Du kanske till exempel vill bli meddelad när en virtuell dator i exempelresursgruppen **myProductionResourceGroup** tas bort. Du kanske vill få ett meddelande om några nya roller tilldelas en användare i din prenumeration.

> [!IMPORTANT]
> Aviseringar om tjänsthälsomeddelanden kan inte skapas via gränssnittet för att skapa aktivitetsloggen. Mer information om hur du skapar och använder hälsomeddelanden för tjänsten finns i [Ta emot aktivitetsloggaviseringar för hälsomeddelanden för tjänster](alerts-activity-log-service-notifications.md).

När du skapar varningsregler kontrollerar du följande:

- Prenumerationen i omfånget skiljer sig inte från prenumerationen där aviseringen skapas.
- Villkoren måste vara den händelsekategori för nivå, status, anropare, resursgrupp, resurs-ID eller resurstyp som aviseringen har konfigurerats på.
- Det finns inget "anyOf"-villkor eller kapslade villkor i aviseringskonfigurationen JSON. I grund och botten är endast en "allOf" villkor tillåtet med några ytterligare "allOf" eller "anyOf" villkor.
- När kategorin är "administrativ" måste du ange minst ett av ovanstående villkor i aviseringen. Du får inte skapa en avisering som aktiveras varje gång en händelse skapas i aktivitetsloggarna.

## <a name="azure-portal"></a>Azure Portal

Du kan använda Azure-portalen för att skapa och ändra aktivitetsloggaviseringsregler. Upplevelsen är integrerad med en Azure-aktivitetslogg för att säkerställa sömlösa aviseringar för specifika händelser av intresse.

### <a name="create-with-the-azure-portal"></a>Skapa med Azure-portalen

Använd följande procedur.

1. I Azure-portalen väljer du > **Övervakarvarningar**. **Monitor**
2. Välj **Ny varningsregel** i det övre vänstra hörnet i **fönstret Aviseringar.**

     ![Ny varningsregel](media/alerts-activity-log/AlertsPreviewOption.png)

     Fönstret **Skapa regel** visas.

      ![Nya alternativ för varningsregel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Under **Definiera varningsvillkor**anger du följande information och väljer **Klar:**

   - **Varningsmål:** Om du vill visa och välja målet för den nya aviseringen använder du **Filtrera efter prenumerationsfilter** / **efter resurstyp**. Välj resurs- eller resursgruppen i listan som visas.

     > [!NOTE]
     > 
     > Du kan bara välja [Azure Resource Manager](../../azure-resource-manager/management/overview.md) spårad resurs, resursgrupp eller en hel prenumeration för en aktivitetsloggsignal. 

     **Exempelvy för varningsmål**

     ![Välja mål](media/alerts-activity-log/select-target.png)

   - Under **Målvillkor**väljer du **Lägg till villkor**. Alla tillgängliga signaler för målet visas, vilket inkluderar de från olika kategorier av **aktivitetslogg .** Kategorinamnet läggs till i **övervakartjänstens** namn.

   - Välj signalen i listan som visas för olika åtgärder som är möjliga för typen **Aktivitetslogg**.

     Du kan välja tidslinjen för logghistorik och motsvarande varningslogik för den här målsignalen:

     **Skärmen Lägg till villkor**

     ![Lägg till villkor](media/alerts-activity-log/add-criteria.png)

     - **Historiktid**: Händelser som är tillgängliga för den valda åtgärden kan ritas under de senaste 6, 12 eller 24 timmarna eller under den senaste veckan.

     - **Varningslogik:**

       - **Händelsenivå**: Allvarlighetsgraden för händelsen: _Utförlig_, _Informations-,_ _Varnings-,_ _fel-_ eller _kritisk_.
       - **Status**: Status för händelsen: _Startat,_ _Misslyckats_eller _Lyckades_.
       - **Händelse initierad av**: Kallas även den som ringer. E-postadressen eller Azure Active Directory-identifieraren för den användare som utförde åtgärden.

       Det här exempelsignaldiagrammet har varningslogiken tillämpad:

       ![Valda villkor](media/alerts-activity-log/criteria-selected.png)

4. Ange följande information under **Definiera aviseringsinformation:**

    - **Varningsregelnamn**: Namnet på den nya aviseringsregeln.
    - **Beskrivning**: Beskrivningen för den nya varningsregeln.
    - **Spara avisering i resursgruppen**: Välj den resursgrupp där du vill spara den nya regeln.

5. Ange **Action group**den åtgärdsgrupp som du vill tilldela den nya aviseringsregeln under Åtgärdsgrupp. Du [kan också skapa en ny åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) och tilldela den till den nya regeln. Om du vill skapa en ny grupp väljer du **+ Ny grupp**.

6. Om du vill aktivera reglerna när du har skapat dem väljer du **Ja** för alternativet Aktivera när du **skapar.**
7. Välj **Skapa aviseringsregel**.

    Den nya aviseringsregeln för aktivitetsloggen skapas och ett bekräftelsemeddelande visas i det övre högra hörnet av fönstret.

    Du kan aktivera, inaktivera, redigera eller ta bort en regel. Läs mer om hur du hanterar aktivitetsloggregler.


En enkel analogi för att förstå villkor för vilka varningsregler kan skapas i en aktivitetslogg är att utforska eller filtrera händelser via [aktivitetsloggen i Azure-portalen](activity-log-view.md#azure-portal). På loggskärmen **för Azure Monitor - Aktivitet** kan du filtrera eller hitta den nödvändiga händelsen och sedan skapa en avisering med hjälp av knappen Lägg till **aktivitetslogg.** Följ sedan steg 4 till 7 som tidigare visats.
    
 ![Lägga till avisering från aktivitetsloggen](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visa och hantera i Azure-portalen

1. I Azure-portalen väljer du > **Övervakarvarningar**. **Monitor** Välj **Hantera varningsregler** i fönstrets övre vänstra hörn.

    ![Hantera aviseringsregler](media/alerts-activity-log/manage-alert-rules.png)

    Listan över tillgängliga regler visas.

2. Sök efter aktivitetsloggregeln som ska ändras.

    ![Varningsregler för sökaktivitetslogg](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Du kan använda tillgängliga filter, _Prenumeration,_ _Resursgrupp,_ _Resurs,_ _Signaltyp_eller _Status_för att hitta den aktivitetsregel som du vill redigera.

   > [!NOTE]
   > 
   > Du kan bara redigera **grupper för beskrivning,** **mål och** **åtgärd**.

3. Markera regeln och dubbelklicka för att redigera regelalternativen. Gör de nödvändiga ändringarna och välj sedan **Spara**.

   ![Hantera aviseringsregler](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Du kan aktivera, inaktivera eller ta bort en regel. Välj lämpligt alternativ högst upp i fönstret när du har valt regeln enligt beskrivningen i steg 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Om du vill skapa en aviseringsregel för aktivitetslogger `microsoft.insights/activityLogAlerts`med hjälp av en Azure Resource Manager-mall skapar du en resurs av typen . Sedan fyller du i alla relaterade egenskaper. Här är en mall som skapar en aviseringsregel för aktivitetslogg:

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
Det tidigare exemplet JSON kan sparas som exempel på ExempelvisActivityLogAlert.json i den här genomsläpningen och kan distribueras med hjälp av [Azure Resource Manager i Azure-portalen](../../azure-resource-manager/templates/deploy-portal.md).

Följande fält är de alternativ som du kan använda i Azure Resource Manager-mallen för villkorsfälten: Observera att "Resurshälsa", "Rådgivare" och "Tjänsthälsa" har extra egenskaper fält för sina speciella fält. 
1. resourceId: Resurs-ID för den påverkade resursen i aktivitetslogghändelsen som aviseringen ska genereras på.
2. kategori: Kategorin i aktivitetslogghändelsen. Till exempel: Administration, ServiceHealth, ResourceHealth, Autoscale, Säkerhet, Rekommendation, Policy.
3. anropare: E-postadressen eller Azure Active Directory-identifieraren för den användare som utförde åtgärden för aktivitetslogghändelsen.
4. nivå: Aktivitetsnivå i aktivitetslogghändelsen som aviseringen ska genereras på. Till exempel: Kritiskt, Fel, Varning, Information, Utförlig.
5. operationName: Namnet på åtgärden i aktivitetslogghändelsen. Till exempel: Microsoft.Resources/deployments/write
6. resourceGroup: Namn på resursgruppen för den påverkade resursen i aktivitetslogghändelsen.
7. resourceProvider: [Azure-resursleverantörer och typer förklaring](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). En lista som mappar resursleverantörer till Azure-tjänster finns i [Resursleverantörer för Azure-tjänster](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: Sträng som beskriver status för åtgärden i aktivitetshändelsen. Till exempel: Startat, Pågår, lyckades, Misslyckades, Aktiv, Löst
9. underStatus: Vanligtvis HTTP-statuskoden för motsvarande REST-anrop, men kan även innehålla andra strängar som beskriver en understatus.   Till exempel: OK (HTTP-statuskod: 200), Skapad (HTTP-statuskod: 201), Accepterad (HTTP-statuskod: 202), Inget innehåll (HTTP-statuskod: 204), Felaktig begäran (HTTP-statuskod: 400), Hittades inte (HTTP-statuskod: 404), Konflikt (HTTP-statuskod: 409), Intern server Fel (HTTP-statuskod: 500), Tjänsten är inte tillgänglig (HTTP-statuskod: 503), TIMEOUT för gateway (HTTP-statuskod: 504).
10. resourceType: Den typ av resurs som påverkades av händelsen. Till exempel: Microsoft.Resources/deployments

Ett exempel:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Mer information om fälten aktivitetslogg hittar du [här](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Det kan ta upp till 5 minuter innan den nya aktivitetsloggaviseringsregeln blir aktiv.

## <a name="rest-api"></a>REST API 
[API:et för Azure Monitor-aktivitetsloggaviseringar](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) är ett REST API. Det är helt kompatibelt med REST-API:et för Azure Resource Manager. Den kan användas via PowerShell med hjälp av Resource Manager cmdlet eller Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Distribuera Resource Manager-mallen med PowerShell
Om du vill använda PowerShell för att distribuera exempelmallen Resource Manager som visas i föregående [Azure Resource Manager-mallavsnitt](#azure-resource-manager-template) använder du följande kommando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

där exempletActivityLogAlert.parameters.json innehåller de värden som anges för de parametrar som behövs för att skapa varningsregeln.

### <a name="use-activity-log-powershell-cmdlets"></a>Använda PowerShell-cmdlets i aktivitetsloggen

Aktivitetsloggvarningar har dedikerade PowerShell-cmdlets tillgängliga:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Skapar en ny aktivitetsloggavisering eller uppdaterar en befintlig aktivitetsloggavisering.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Hämtar en eller flera aktivitetsloggaviseringsresurser.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Aktiverar en befintlig aktivitetsloggavisering och anger dess taggar.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Inaktiverar en befintlig aktivitetsloggavisering och anger dess taggar.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Tar bort en aktivitetsloggavisering.

## <a name="azure-cli"></a>Azure CLI

Dedikerade Azure CLI-kommandon under den inställda [az övervaka aktivitetsloggen är](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) tillgängliga för hantering av aktivitetsloggvarningsregler.

Om du vill skapa en ny aktivitetsloggvarningsregel använder du följande kommandon i den här ordningen:

1. [az övervaka aktivitetsloggvarning skapa:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)Skapa en ny aktivitetslogg varningsregel resurs.
1. [az övervaka aktivitetsloggens aviseringsomfattning:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)Lägg till omfång för den skapade aktivitetsloggvarningsregeln.
1. [az övervaka åtgärdsgrupp för aktivitetslogg:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)Lägg till en åtgärdsgrupp i aktivitetsloggvarningsregeln.

Om du vill hämta en resurs för aktivitetsloggaviseringsregel använder du Azure [CLI-kommandot az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Om du vill visa alla aktivitetsloggaviseringsregelresurser i en resursgrupp använder du [varningslistan för aktivitetsloggen](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)för övervakare .
Aviseringsregelresurser för aktivitetslogg kan tas bort med kommandot Azure CLI [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [webhook-schema för aktivitetsloggar](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Läs en [översikt över aktivitetsloggar](../../azure-monitor/platform/activity-log-alerts.md).
- Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).  
- Läs mer om [hälsomeddelanden](../../azure-monitor/platform/service-notifications.md)för tjänster .
