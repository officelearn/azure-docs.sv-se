---
title: Hantera Azure Automation-data
description: Den här artikeln innehåller flera ämnen för att hantera en Azure Automations miljö.  Omfattar för närvarande datakvarhållning och säkerhets kopiering Azure Automation haveri beredskap i Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: daa5bab7c8d4cbe98ffe9a8a8a4b66da029fef5c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421896"
---
# <a name="managing-azure-automation-data"></a>Hantera Azure Automation-data
Den här artikeln innehåller flera ämnen för att hantera en Azure Automations miljö.

## <a name="data-retention"></a>Datakvarhållning
När du tar bort en resurs i Azure Automation behålls den i 90 dagar för gransknings syfte innan den tas bort permanent.  Du kan inte se eller använda resursen under den här tiden.  Den här principen gäller även för resurser som tillhör ett Automation-konto som har tagits bort.

Azure Automation automatiskt tar bort och tar bort jobb som är äldre än 90 dagar.

I följande tabell sammanfattas bevarande principen för olika resurser.

| Data | Princip |
|:--- |:--- |
| Konton |Permanent borttagna 90 dagar efter att kontot har tagits bort av en användare. |
| Tillgångar |Permanent borttagna 90 dagar efter att till gången har tagits bort av en användare, eller 90 dagar efter det att kontot som innehar till gången tas bort av en användare. |
| Moduler |Permanent borttagna 90 dagar efter att modulen har tagits bort av en användare, eller 90 dagar efter det att kontot som innehåller modulen tas bort av en användare. |
| Runbooks |Permanent borttagna 90 dagar efter att resursen har tagits bort av en användare, eller 90 dagar efter det att kontot som innehåller resursen tas bort av en användare. |
| Jobb |Tog bort och tog bort permanent 90 dagar efter att den senast ändrades. Detta kan bero på att jobbet har slutförts, stoppats eller pausats. |
| Nodkonfigurationer/MOF-filer |Den gamla nodens konfiguration tas bort permanent 90 dagar efter att en ny nod har skapats. |
| DSC-noder |Permanent borttagna 90 dagar efter att noden har avregistrerats från Automation-kontot med hjälp av Azure Portal [-eller unregister-AzureRMAutomationDscNode-](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) cmdlet: en i Windows PowerShell. Noderna tas också bort permanent 90 dagar efter det att kontot som innehåller noden tas bort av en användare. |
| Node-rapporter |Permanent borttagna 90 dagar efter att en ny rapport har genererats för noden |

Bevarande principen gäller för alla användare och kan för närvarande inte anpassas.

Men om du behöver spara data under en längre tids period kan du vidarebefordra Runbook-jobbets loggar till Azure Monitor loggar.  Mer information hittar [du i vidarebefordra Azure Automation jobb data till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Säkerhetskopiera Azure Automation
När du tar bort ett Automation-konto i Microsoft Azure raderas alla objekt i kontot inklusive Runbooks, moduler, konfigurationer, inställningar, jobb och till gångar. Det går inte att återställa objekten när kontot har tagits bort.  Du kan använda följande information för att säkerhetskopiera innehållet i ditt Automation-konto innan du tar bort det. 

### <a name="runbooks"></a>Runbooks
Du kan exportera dina runbooks till skriptfiler med antingen Azure Portal-eller [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) -cmdleten i Windows PowerShell.  Dessa skriptfiler kan importeras till ett annat Automation-konto som beskrivs i [skapa eller importera en Runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Integreringsmoduler
Det går inte att exportera integrerings moduler från Azure Automation.  Du måste se till att de är tillgängliga utanför Automation-kontot.

### <a name="assets"></a>Tillgångar
Du kan inte exportera [till gångar](/previous-versions/azure/dn939988(v=azure.100)) från Azure Automation.  Med hjälp av Azure Portal måste du anteckna information om variabler, autentiseringsuppgifter, certifikat, anslutningar och scheman.  Du måste sedan manuellt skapa alla till gångar som används av Runbooks som du importerar till en annan automatisering.

Du kan använda [Azure-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) för att hämta information om okrypterade till gångar och antingen spara dem för framtida referens eller skapa likvärdiga till gångar i ett annat Automation-konto.

Det går inte att hämta värdet för krypterade variabler eller lösen ords fältet för autentiseringsuppgifter med hjälp av cmdletar.  Om du inte känner till dessa värden kan du hämta dem från en Runbook med hjälp av åtgärderna [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) och [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) .

Du kan inte exportera certifikat från Azure Automation.  Du måste se till att alla certifikat är tillgängliga utanför Azure.

### <a name="dsc-configurations"></a>DSC-konfigurationer
Du kan exportera dina konfigurationer till skriptfiler med antingen Azure Portal eller cmdleten [export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) i Windows PowerShell. Dessa konfigurationer kan importeras och användas i ett annat Automation-konto.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikering i Azure Automation
Geo-replikering, standard i Azure Automation-konton, säkerhetskopierar konto data till en annan geografisk region för redundans. Du kan välja en primär region när du konfigurerar ditt konto och sedan tilldelas en sekundär region automatiskt. De sekundära data som kopieras från den primära regionen uppdateras kontinuerligt i händelse av data förlust.  

I följande tabell visas tillgängliga primära och sekundära regions par.

| Primär | Sekundär |
| --- | --- |
| USA, södra centrala |USA, norra centrala |
| USA, östra 2 |USA, centrala |
| Europa, västra |Europa, norra |
| Sydostasien |Asien, östra |
| Japan, östra |Japan, västra |

Om det osannolika skulle uppstå att en primär regions information förloras försöker Microsoft återställa den. Om primär data inte kan återställas utförs GEO-redundans och de berörda kunderna meddelas om detta via prenumerationen.


