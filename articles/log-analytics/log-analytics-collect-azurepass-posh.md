---
title: Samla in mätvärden för Azure PaaS-resurs med Log Analytics | Microsoft Docs
description: Lär dig hur du aktiverar Azure PaaS mått resurssamling använder PowerShell för kvarhållning och analyser i Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 681f0eeff0d93e6af341d2dddaf7da4475c13d9d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007203"
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Konfigurera insamling av mätvärden för Azure PaaS-resurs med Log Analytics

Azure-plattformen som en tjänst (PaaS)-resurser, t.ex. Azure SQL och webbplatser (Webbappar), kan generera mått prestandadata internt till Log Analytics. Det här skriptet kan du aktivera loggning för PaaS-resurser som redan har distribuerats i en specifik resursgrupp eller i en hela prenumerationen mätvärden. 

Idag är går det inte att aktivera mätvärden loggning för PaaS-resurser via Azure-portalen. Därför kan behöva du använda ett PowerShell-skript. Den här inbyggda mått loggningsfunktioner tillsammans med Log Analytics övervakning, kan du övervaka Azure-resurser i stor skala. 

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att du har följande Azure Resource Manager-moduler som installerats på datorn innan du fortsätter:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Vi rekommenderar att alla dina Azure Resource Manager-moduler är samma version för att garantera kompatibilitet när du kör Azure Resource Manager-kommandon från PowerShell.
>
Om du vill installera den senaste versionen av Azure Resource Manager-moduler på din dator, se [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Aktivera Azure-diagnostik  
Konfigurera Azure Diagnostics för PaaS-resurser åstadkoms genom att köra skriptet **aktivera AzureRMDiagnostics.ps1**, som är tillgängligt från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52).  Skriptet har stöd för följande scenarier:
  
* Ange en resurs som är relaterade till en eller flera resursgrupper i en prenumeration  
* Ange en resurs som är relaterade till en specifik resursgrupp i en prenumeration  
* Konfigurera om en resurs ska vidarebefordras till en annan arbetsyta

Endast resurser som stöd för insamling av mått med Azure-diagnostik och skicka direkt till Log Analytics som stöds.  För en detaljerad lista över [samla in Azure-tjänsteloggar och mått för användning i Log Analytics](log-analytics-azure-storage.md) 

Utför följande steg för att ladda ned och kör skriptet.

1.  Ange från startskärmen i Windows **PowerShell** och högerklicka på PowerShell från sökresultaten.  Välj på menyn **kör som administratör**.   
2. Spara den **aktivera AzureRMDiagnostics.ps1** skriptfilen lokalt genom att köra följande kommando och ange en sökväg för att lagra skriptet.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Kör `Connect-AzureRmAccount` för att skapa en anslutning med Azure.   
4. Kör följande skript `.\Enable-AzureRmDiagnostics.ps1` utan några parametrar för att aktivera insamling av data från en specifik resurs i din prenumeration eller med parametern `-ResourceGroup <myResourceGroup>` att ange en resurs i en specifik resursgrupp.   
5. Välj lämplig prenumeration i listan om du har fler än en, genom att ange rätt värde.<br><br> ![Välj en prenumeration som returneras av skript](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> Annars väljs automatiskt den enda prenumerationen som är tillgängliga.
6. Skriptet returnerar sedan en lista med Log Analytics-arbetsytor som är registrerade i prenumerationen.  Välj lämpligaste i listan.<br><br> ![Välj arbetsyta som returneras av skriptet](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Välj den Azure-resurs som du vill aktivera insamling från. Till exempel om du anger 5 kan aktivera du datainsamling för SQL Azure-databaser.<br><br> ![Välj resurstyp som returneras av skriptet](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Du kan bara välja resurser som har stöd för insamling av mått med Azure Diagnostics och skickas direkt till en Log Analytics.  Skriptet visar värdet **SANT** under den **mått** kolumn för listan över resurser som identifieras i din prenumeration eller den angivna resursgruppen.    
8. Du uppmanas att bekräfta valet.  Ange **Y** att aktivera loggning för mått för alla valda resurser för det omfång som har definierats, som i vårt exempel är alla SQL-databaser i prenumerationen.  

Skriptet körs mot varje resurs som matchar valda villkoren och aktivera insamling av mått för dem. När den har slutförts visas ett meddelande om konfigurationen är klar.  

Inom kort när du har slutfört börjar du se data från Azure PaaS-resurs i Log Analytics-lagringsplatsen.  En post med typen `AzureMetrics` skapas och analysera dessa poster stöds av den [Azure SQL Analytics](log-analytics-azure-sql.md) och [Azure Web Apps-analys](log-analytics-azure-web-apps-analytics.md) hanteringslösningar.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Uppdatera en resurs för att skicka data till en annan arbetsyta
Om du har en resurs som redan skickar data till Log Analytics-arbetsytan och du senare vill konfigurera om den för att referera till en annan arbetsyta, kan du köra skriptet med den `-Update` parametern.  

**Exempel:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Du uppmanas att svara på samma information som när du körde skriptet att köra den ursprungliga konfigurationen.  

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [loggsökningar](log-analytics-queries.md) att analysera data som samlas in från datakällor och lösningar. 

* Använd [anpassade fält](log-analytics-custom-fields.md)(för att parsa händelseposter till enskilda fält.

* Granska [skapa en anpassad instrumentpanel för användning i Log Analytics](log-analytics-dashboards.md) vill lära dig att visualisera din logg söker på ett meningsfullt sätt för organisationen.
