---
title: "Samla in Azure PaaS resurs mått med Log Analytics | Microsoft Docs"
description: "Lär dig hur du aktiverar Azure PaaS mått resurssamling använder PowerShell för kvarhållning och analys i logganalys."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 83491c4902dabc6bab1e222551298cfaffbaecf4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Konfigurera Azure PaaS resurs mått samling med logganalys

Azure-plattformen som en tjänst (PaaS)-resurser, t.ex. Azure SQL och webbplatser (webbprogram) kan sända mått prestandadata internt till logganalys. Det här skriptet kan du aktivera mätvärden loggning för PaaS-resurser som redan har distribuerats i en viss resursgrupp eller i en hela prenumerationen. 

Idag är går det inte att aktivera mätvärden loggning för PaaS-resurser via Azure-portalen. Därför måste du använda ett PowerShell.skript. Den här interna mätvärden loggningsfunktioner tillsammans med logganalys övervakning, kan du övervaka Azure-resurser i större skala. 

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att du har följande Azure Resource Manager-moduler är installerade på datorn innan du fortsätter:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Vi rekommenderar att alla Azure Resource Manager-moduler är samma version för att säkerställa kompatibilitet när du kör kommandon för Azure Resource Manager från PowerShell.
>
Om du vill installera den senaste versionen av Azure Resource Manager-moduler på datorn, [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Aktivera Azure-diagnostik  
Konfigurera Azure-diagnostik för PaaS-resurser kan åstadkommas genom att köra skriptet **aktivera AzureRMDiagnostics.ps1**, som är tillgänglig från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  Skriptet har stöd för följande scenarier:
  
* Ange en resurs som är relaterade till en eller flera resursgrupper i en prenumeration  
* Ange en resurs som rör en viss resursgrupp i en prenumeration  
* Konfigurera om en resurs ska vidarebefordras till en annan arbetsyta

Endast resurser som stöd för att samla in mått med Azure-diagnostik och skicka direkt till Log Analytics stöds.  En detaljerad lista granska [samla in Azure tjänstloggar och mått för användning i logganalys](log-analytics-azure-storage.md) 

Utför följande steg om du vill hämta och köra skriptet.

1.  Ange från startskärmen i Windows **PowerShell** och högerklicka på PowerShell i sökresultatet.  Välj på menyn **kör som administratör**.   
2. Spara den **aktivera AzureRMDiagnostics.ps1** skriptfilen lokalt genom att köra följande kommando och ange en sökväg för att lagra skriptet.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Kör `Login-AzureRmAccount` att skapa en anslutning med Azure.   
4. Kör följande skript `.\Enable-AzureRmDiagnostics.ps1` utan några parametrar för att aktivera datainsamling från en viss resurs i din prenumeration eller med parametern `-ResourceGroup <myResourceGroup>` att ange en resurs i en viss resursgrupp.   
5. Välj lämplig prenumerationen i listan om du har fler än en, genom att ange rätt värde.<br><br> ![Välj prenumerationen som returneras av skript](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> Annars väljs automatiskt den enda tillgängliga prenumerationen.
6. Därefter returnerar skriptet en lista över logganalys arbetsytor registrerade i prenumerationen.  Välj en från listan.<br><br> ![Välj arbetsyta som returneras av skript](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Välj den Azure-resurs som du vill aktivera insamling från. Till exempel om du skriver 5 aktiverar du datainsamling för SQL Azure-databaser.<br><br> ![Välj resurstyp som returneras av skript](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Du kan endast välja resurser som har stöd för att samla in mått med Azure-diagnostik och skicka direkt till en Log Analytics.  Skriptet visar värdet **SANT** under den **mått** för listan över resurser som identifieras i din prenumeration eller angivna resursgruppen.    
8. Du uppmanas att bekräfta valet.  Ange **Y** att aktivera mätvärden loggning för alla markerade resurser för det omfång som har definierats, som i vårt exempel är alla SQL-databaser i prenumerationen.  

Skriptet körs mot varje resurs som matchar valda villkoren och aktivera insamling av mätvärden för dessa. När den är klar, visas ett meddelande om konfigurationen är klar.  

Strax efter slutförande startar du att se data från Azure PaaS-resurs i logganalys-databasen.  En post med typen `AzureMetrics` skapas och analysera dessa poster stöds av den [Azure SQL Analytics](log-analytics-azure-sql.md) och [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md) lösningar för hantering.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Uppdatera en resurs för att skicka data till en annan arbetsyta
Om du har en resurs som redan skickar data till logganalys-arbetsytan och du senare vill konfigurera om den för att referera till en annan arbetsyta, kan du köra skriptet med den `-Update` parameter.  

**Exempel:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Du uppmanas att besvara samma information som när du körde skript för att utföra den inledande konfigurationen.  

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar. 

* Använd [anpassade fält](log-analytics-custom-fields.md)(för att parsa händelseposter till enskilda fält.

* Granska [skapa en anpassad instrumentpanel för användning i logganalys](log-analytics-dashboards.md) att förstå hur du visualisera loggen söker på ett meningsfullt sätt för organisationen.