---
title: Övervakning av användning och de uppskattade kostnaderna i Azure-Monitor
description: Översikt över processen med att använda Azure-Monitor användning och uppskattade kostnaderna sida
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.component: ''
ms.openlocfilehash: edfcc244105403ae33251777c560d4cc21dfe5cb
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264290"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Övervakning av användning och de uppskattade kostnaderna

I övervakaren hubb för Azure-portalen på **användning och de uppskattade kostnaderna** sidan beskriver användningen av grundläggande övervakningsfunktioner som [avisering, mått, meddelanden](https://azure.microsoft.com/pricing/details/monitor/), [Azure logganalys ](https://azure.microsoft.com/pricing/details/log-analytics/), och [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Detta omfattar även logganalys användning som köpts via insikter för kunder på prisnivå planerna som är tillgängliga innan April 2018 och Analytics erbjuder.

På den här sidan kan användare visa sina resursanvändningen under de senaste 31 dagarna samman per prenumeration. Ökad-moduler Visa användningstrender över 31 dagar. Stora mängder data samlas för denna beräkning, så måste du ha tålamod när sidan läses in.

Det här exemplet visar användning av övervakning och en uppskattning av kostnaderna för resulterande:

![Användnings- och uppskattade kostnaderna portal skärmbild](./media/monitoring-usage-and-estimated-costs/001.png)

Välj länken i kolumnen månatliga användningsdata för att öppna ett diagram som visar användningstrender över de senaste 31 dagarna:

![Ingår per nod liggande diagram skärmbild](./media/monitoring-usage-and-estimated-costs/002.png)

Här är en annan liknande användning och kostnader sammanfattning. Det här exemplet visar en prenumeration i nya April 2018 förbrukningsbaserad priserna. Observera bristen på någon nod-baserade fakturering. Datapåfyllning och lagring för Log Analytics och Application Insights rapporteras på en ny vanliga mätaren.

![Användnings- och uppskattade kostnaderna portal skärmbild - April 2018 priser](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Ny prissättningsmodell

I April 2018, en [nya övervakning prismodellen släpptes](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Detta funktioner molnet eget, förbrukningsbaserad priser. Du betalar bara för det du använder, utan åtaganden baserat på noden. Information om nya priserna är tillgängliga för [avisering, mått, meddelanden](https://azure.microsoft.com/pricing/details/monitor/), [logganalys](https://azure.microsoft.com/pricing/details/log-analytics/) och [Programinsikter](https://azure.microsoft.com/pricing/details/application-insights/). 

För kunder att logganalys eller Application Insights efter den 2 April 2018 är nya priserna det enda alternativet. För kunder som redan använder dessa tjänster, flyttar till nya priserna är valfritt.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Utvärdera effekten av nya priserna
Ny prissättningsmodell har olika påverkan på varje kund baserat på deras övervakning användningsmönster. För kunder som använder logganalys eller Application Insights innan den 2 April 2018 den **användnings- och uppskattade kostnaden** sida i Azure-övervakaren beräknar ändringar i kostnaderna om de flyttar till nya priserna. Det ger sätt att flytta en prenumeration till den nya modellen. För de flesta kunder blir de nya priserna fördelaktigt. För kunder med särskilt stor data användningsmönster eller högre kostnad regioner, kan detta inte är fallet.

Få en uppskattning av kostnaderna för prenumerationerna som du valde på den **användning och de uppskattade kostnaderna** väljer blå listen längst upp på sidan. Det är bäst att göra detta en prenumeration samtidigt, eftersom det är den nivå där nya priserna kan antas.

![Övervaka användning och de uppskattade kostnaderna i nya prisnivå modellen skärmbild](./media/monitoring-usage-and-estimated-costs/004.png)

Den nya sidan visas en liknande version av föregående sida med en grön banderoll:

![Övervaka användning och de uppskattade kostnaderna i aktuell prisnivå modellen skärmbild](./media/monitoring-usage-and-estimated-costs/005.png)

Sidan visar också en annan uppsättning mätare som motsvarar nya priserna. Den här listan är ett exempel:

- Insikt och Analytics\Overage per nod
- Insikt och Analytics\Included per nod
- Överförbrukning programdata för Insights\Basic
- Insights\Included programdata

Ny prissättningsmodell saknar nod-baserade inkluderade data allokeringar. Därför kan dessa data införandet mätare kombineras till en ny gemensamma data införandet mätare kallas **delade Services\Data införandet**. 

Det finns en annan ändring av data som inhämtas i logganalys- eller Application Insights i områden med högre kostnader. Data för regionerna hög kostnad visas med de nya nationella mätare. Ett exempel är **Datapåfyllning (oss Väst Central)**.

> [!NOTE]
> Den uppskattade kostnaderna inte factor i kontonivå per nod rättigheter för prenumerationen Operations Management Suite (OMS) per prenumeration. Kontakta din kontorepresentant för en mer detaljerad beskrivning av nya priserna i det här fallet.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nya priser modell och rättigheter för Operations Management Suite-prenumeration

Kunder som har köpt Microsoft Operations Management Suite E1 och E2 är berättigad till per nod data införandet rättigheter för [logganalys](https://www.microsoft.com/cloud-platform/operations-management-suite) och [Programinsikter](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). Ta emot dessa rättigheter för Log Analytics arbetsytor eller Application Insights-resurser i en viss prenumeration: 

- Prenumerationens prismodellen måste vara i pre-April 2018 modellen.
- Log Analytics arbetsytorna bör använda den ”Per nod (OMS)” prisnivån.
- Application Insights-resurser ska använda ”Enterprise”-priser plan.

Beroende på antalet noder i sviten som din organisation har köpt flytta några prenumerationer nya priserna kan vara fördelaktigt, men detta kräver noggrant övervägande. I allmänhet rekommenderas bara för att hålla pre-April 2018 modellen enligt beskrivningen ovan.

> [!WARNING]
> Om din organisation har köpt Microsoft Operations Management Suite E1 och E2, är det oftast bäst att hålla dina prenumerationer i pre-April 2018 priserna. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Ändras när du flyttar till en ny prissättningsmodell

Ny prissättningsmodell förenklar logganalys och Application Insights priser alternativ för att bara en enda nivå (eller planerar). Flytta en prenumeration till den nya prisnivå modellen skapar:

- Ändra prisnivån för varje logganalys till en ny Per GB-nivå (kallas ”pergb2018” i Azure Resource Manager)
- Application Insights resurser i Enterprise-planen har ändrats till den grundläggande planen.

Uppskattningen se effekten av ändringarna.

> [!WARNING]
> Här viktigt om du använder Azure Resource Manager eller PowerShell för att distribuera [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) eller [Programinsikter](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) för en prenumeration som du har flyttat till den nya prismodell. Om du anger en nivå/prisavtal än ”pergb2018” för Log Analytics eller ”Basic” för Application Insights i stället misslyckas distributionen på grund av att ange ett ogiltigt priser nivå/planen om det lyckas **men använder den enda giltiga priser för nivån /-planen** (Detta gäller inte till Log Analytics kostnadsfria nivån var ett ogiltigt meddelande för prisnivå nivån skapas).
>

## <a name="moving-to-the-new-pricing-model"></a>Flytta till ny prissättningsmodell

Om du har valt att införa nya priserna för en prenumeration väljer du den **priser vald modell** alternativet överst i den **användning och de uppskattade kostnaderna** sidan:

![Övervaka användning och de uppskattade kostnaderna i nya prisnivå modellen skärmbild](./media/monitoring-usage-and-estimated-costs/006.png)

Den **priser vald modell** öppnas. Den visar en lista över alla prenumerationer som du har visat på föregående sida:

![Priser för modellen val av skärmbild](./media/monitoring-usage-and-estimated-costs/007.png)

Om du vill flytta en prenumeration till nya priserna, markera kryssrutan och välj sedan **spara**. Du kan flytta tillbaka till äldre priserna på samma sätt. Tänk på att prenumerationsägaren eller deltagarbehörighet krävs för att ändra prisnivå modellen.

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatisera flyttar till en ny prissättningsmodell

Skripten nedan kräver Azure PowerShell-modulen. Kontrollera om du har den senaste versionen finns [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.1.0).

När du har den senaste versionen av Azure PowerShell kan du först behöver köra ``Connect-AzureRmAccount``.

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Ett resultat av True under isGrandFatherableSubscription anger att den här prenumerationen prismodellen kan flyttas mellan prissättningsmodeller. Saknas ett värde under optedInDate innebär den här prenumerationen är inställd på att gamla priserna.

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

Om du vill migrera den här prenumerationen till nya priserna kör:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

Bekräfta att ändringen har lyckats kör:

```PowerShell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

Om migreringen lyckades, bör resultatet nu se ut som:

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

OptInDate innehåller nu en tidsstämpel när den här prenumerationen valts i nya priserna.

Om du behöver återgå till den gamla prismodellen kör du:

```PowerShell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

Om du kör sedan det föregående skript som har ``-Action listmigrationdate``, bör nu visas en tom optedInDate värde som anger din prenumeration har returnerats till äldre prismodell.

Om du har flera prenumerationer som du vill migrera som finns under samma klient kan du skapa egna variant med delar av följande skript:

```PowerShell
#Query tenant and create an array comprised of all of your tenants subscription ids
$TenantId = <Your-tenant-id>
$Tenant =Get-AzureRMSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

Om du vill kontrollera om alla prenumerationer i din klient är berättigad till nya priserna, kan du köra:

```PowerShell
Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
}
```

Skriptet kan anpassas ytterligare genom att skapa ett skript som genererar tre matriser. En matris består av alla prenumerations-id's som har ```isGrandFatherableSubscription``` anges till True och optedInDate inte har ett värde. En andra matris med några prenumerationer för närvarande på nya priserna. Och en tredje matris endast med prenumerations-ID: n i din klient som inte är tillämplig för nya priserna:

```PowerShell
[System.Collections.ArrayList]$Eligible= @{}
[System.Collections.ArrayList]$NewPricingEnabled = @{}
[System.Collections.ArrayList]$NotEligible = @{}

Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
$Result= Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force

     if ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $False)
     {
     $Eligible.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $True)
     {
     $NewPricingEnabled.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $False)
     {
     $NotEligible.add($id)
     }
}
```

> [!NOTE]
> Skriptet ovan kan ta lite tid att köra beroende på antalet prenumerationer. Användning av metoden .add() echo PowerShell-fönstret ökar värdena som objekt läggs till varje matris.

Nu när du har dina prenumerationer uppdelat i tre matriser bör du granska resultatet noggrant. Du kanske vill göra en säkerhetskopia av innehållet i matriserna så att du kan enkelt återställa dina ändringar om det behövs i framtiden. Om du valt som du vill konvertera alla berättigade prenumerationer som för närvarande på gamla priserna till den nya Prismodell den här uppgiften kan nu uppnås med:

```PowerShell
Foreach ($id in $Eligible)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzureRmResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
}

```