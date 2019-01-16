---
title: VM uppdatering och hantering med Azure Stack | Microsoft Docs
description: Lär dig mer om att använda lösningar för uppdateringshantering, ändringsspårning och inventering i Azure Automation för att hantera Windows- och Linux-datorer som distribueras i Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.openlocfilehash: b86a9a0cff397148b0632b3108f58a1977b518e9
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332514"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack-VM-uppdatering och hantering
Du kan använda följande funktioner i Azure Automation-lösningen för att hantera Windows- och Linux-datorer som distribueras med hjälp av Azure Stack:

- **[Hantering av uppdateringar](https://docs.microsoft.com/azure/automation/automation-update-management)**. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera processen för att installera nödvändiga uppdateringar för dessa Windows och Linux-datorer med lösningen för uppdateringshantering.

- **[Ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. Ändringar av installerad programvara, tjänster för Windows, Windows-registret och filer och Linux-Daemon på de övervakade servrarna skickas till Log Analytics-tjänsten i molnet för bearbetning. Logiken tillämpas på den mottagna data och Molntjänsten innehåller data. Du kan enkelt se ändringar som gjorts i din serverinfrastruktur med hjälp av informationen på instrumentpanelen för ändringsspårning.

- **[Inventering](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Inventeringssamling för en virtuell dator i Azure Stack ger ett webbläsarbaserat användargränssnitt för att installera och konfigurera inventeringssamling. 

> [!IMPORTANT]
> Dessa lösningar är samma som de som används för att hantera virtuella Azure-datorer. Både Azure och Azure Stack-datorer hanteras på samma sätt, från samma gränssnitt med samma verktyg. De virtuella datorer i Azure Stack är också samma pris som virtuella Azure-datorer när du använder uppdateringshantering, ändringsspårning och inventering lösningar med Azure Stack.

## <a name="prerequisites"></a>Förutsättningar
Flera förutsättningar måste vara uppfyllda innan du använder dessa funktioner för att uppdatera och hantera virtuella datorer i Azure Stack. Dessa inkluderar steg som måste vidtas i Azure portal samt Azure Stack-administrationsportalen.

### <a name="in-the-azure-portal"></a>I Azure portal
För att använda inventering, ändringsspårning och uppdatering av Azure automation-funktioner för virtuella datorer i Azure Stack, måste du först aktivera dessa lösningar i Azure.

> [!TIP]
> Om du redan har dessa funktioner som aktiverats för virtuella Azure-datorer kan använda du dina befintliga LogAnalytics arbetsyta-autentiseringsuppgifter. Om du redan har en LogAnalytics WorkspaceID och den primärnyckeln som du vill använda kan gå vidare till [nästa avsnitt](./vm-update-management.md#in-the-azure-stack-administration-portal). I annat fall Fortsätt i det här avsnittet för att skapa ett nytt LogAnalytics arbetsytan och automation-konto.

Det första steget i aktiveringen av dessa lösningar är att [skapa en arbetsyta för LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) i Azure-prenumerationen. En Log Analytics-arbetsyta är en unik Log Analytics-miljö med en egen databas, datakällor och lösningar. När du har skapat en arbetsyta, Observera WorkspaceID och nyckel. Gå till bladet för arbetsytan om du vill visa den här informationen, klickar du på **avancerade inställningar**, och granska de **arbetsyte-ID** och **primärnyckel** värden. 

Därefter måste du [skapa ett Automation-konto](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Ett Automation-konto är en behållare för dina Azure Automation-resurser. Det är ett sätt att avgränsa dina miljöer eller att ytterligare ordna dina automatiseringsarbetsflöden och resurser. När automation-kontot har skapats kan behöver du aktivera lagret, spårning och uppdatera hanteringsfunktioner. Följ dessa steg om du vill aktivera varje funktion för att göra detta:

1. Gå till Automation-kontot som du vill använda i Azure-portalen.

2. Välj lösning för att aktivera (antingen **inventering**, **ändringsspårning**, eller **uppdateringshantering**).

3. Använd den **Välj arbetsyta...**  listrutan att välja Log Analytics-arbetsytan att använda.

4. Kontrollera att alla återstående information är korrekt och klicka sedan på **aktivera** att aktivera lösningen.

5. Upprepa steg 2 – 4 för att aktivera alla tre lösningar. 

   [![](media/vm-update-management/1-sm.PNG "Aktivera funktioner för automation-konto")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="in-the-azure-stack-administration-portal"></a>I Azure Stack-administrationsportalen
När du har aktiverat Azure Automation-lösningar i Azure-portalen, därefter måste du logga in på administrationsportalen för Azure Stack som en administratör för moln och ladda ned den **Azure-uppdateringen och konfigurationshantering** och  **Azure uppdatering och konfigurationshantering för Linux** tillägget Azure Stack marketplace-objekt. 

   ![Azure uppdatering och konfiguration av tillägget marketplace-objekt](media/vm-update-management/2.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Aktivera uppdateringshantering för virtuella datorer i Azure Stack
Följ dessa steg om du vill aktivera uppdateringshantering för virtuella datorer i Azure Stack.

1. Logga in på användarportalen för Azure Stack.

2. I Azure Stack-användarportalen, går du till bladet tillägg för virtuella datorer som du vill aktivera dessa lösningar, klickar du på **+ Lägg till**väljer den **Azure-uppdateringen och konfigurationshantering** tillägget och klickar på **skapa**:

   [![](media/vm-update-management/3-sm.PNG "Bladet för VM-tillägg")](media/vm-update-management/3-lg.PNG#lightbox)

3. Ange den tidigare skapade WorkspaceID och den primärnyckeln för att länka agenten med arbetsytan LogAnalytics och klicka på **OK** att distribuera tillägget.

   [![](media/vm-update-management/4-sm.PNG "Att tillhandahålla WorkspaceID och nyckel")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Mer information finns i den [dokumentation om automation update management](https://docs.microsoft.com/azure/automation/automation-update-management), måste du aktivera lösningen för uppdateringshantering för varje virtuell dator som du vill hantera. Välj för att aktivera lösningen för alla virtuella datorer som rapporterar till arbetsytan **uppdateringshantering**, klickar du på **hantera datorer**, och välj sedan den **aktivera på alla tillgängliga och framtida datorer** alternativet.

   [![](media/vm-update-management/5-sm.PNG "Att tillhandahålla WorkspaceID och nyckel")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Upprepa detta steg om du vill aktivera varje lösning för Azure Stack virtuella datorer som rapporterar till arbetsytan. 
  
När tillägget för Azure-uppdateringen och konfigurationshantering är aktiverat, utförs en genomsökning två gånger per dag för varje hanterad virtuell dator. API: et anropas varje kvart att fråga efter den senaste uppdateringstiden att fastställa om statusen har ändrats. Om statusen har ändrats, har en fullständig genomsökning initierats.

När de virtuella datorerna har genomsökts visas de i Azure Automation-konto i lösningen för uppdateringshantering: 

   [![](media/vm-update-management/6-sm.PNG "Att tillhandahålla WorkspaceID och nyckel")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> Det kan ta mellan 30 minuter och 6 timmar innan instrumentpanelen visar uppdaterade data från hanterade datorer.

De virtuella datorer i Azure Stack ingår nu i schemalagda uppdateringsdistributioner tillsammans med virtuella Azure-datorer.

## <a name="enable-update-management-using-a-resource-manager-template"></a>Aktivera uppdateringshantering med en Resource Manager-mall
Om du har ett stort antal virtuella datorer i Azure Stack kan du använda [Azure Resource Manager-mallen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) att enkelt distribuera lösningen på virtuella datorer. Mallen distribuerar tillägget Microsoft Monitoring Agent till en befintlig Azure Stack VM och lägger till den till en befintlig Azure LogAnalytics arbetsyta.
 
## <a name="next-steps"></a>Nästa steg
[Optimera prestanda för SQL Server](azure-stack-sql-server-vm-considerations.md)
