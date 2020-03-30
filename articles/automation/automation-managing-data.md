---
title: Hantera Azure Automation-data
description: Den här artikeln innehåller flera avsnitt för hantering av en Azure Automation-miljö.  Innehåller för närvarande datalagring och säkerhetskopiering av Azure Automation Disaster Recovery i Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132884"
---
# <a name="managing-azure-automation-data"></a>Hantera Azure Automation-data

Den här artikeln innehåller flera avsnitt för hantering av en Azure Automation-miljö.

## <a name="data-retention"></a>Datakvarhållning

När du tar bort en resurs i Azure Automation behålls den i 30 dagar för granskning innan den tas bort permanent. Du kan inte se eller använda resursen under den här tiden. Den här principen gäller även resurser som tillhör ett automatiseringskonto som tas bort.

Azure Automation tar automatiskt bort och tar permanent bort jobb som är äldre än 30 dagar.

I följande tabell sammanfattas bevarandeprincipen för olika resurser.

| Data | Princip |
|:--- |:--- |
| Konton |Tas bort permanent 30 dagar efter att kontot har tagits bort av en användare. |
| Tillgångar |Tas bort permanent 30 dagar efter att tillgången har tagits bort av en användare, eller 30 dagar efter att kontot som innehar tillgången har tagits bort av en användare. |
| Moduler |Tas bort permanent 30 dagar efter att modulen har tagits bort av en användare, eller 30 dagar efter att kontot som innehåller modulen tas bort av en användare. |
| Runbooks |Tas bort permanent 30 dagar efter att resursen har tagits bort av en användare, eller 30 dagar efter att kontot som innehåller resursen har tagits bort av en användare. |
| Jobb |Borttagen och permanent bort 30 dagar efter att senast ändrats. Detta kan vara när jobbet har slutförts, stoppats eller pausats. |
| Nodkonfigurationer/MOF-filer |Den gamla nodkonfigurationen tas bort permanent 30 dagar efter att en ny nodkonfiguration har genererats. |
| DSC-noder |Permanent borttagen 30 dagar efter att noden är oregistrerad från Automation-konto med Azure-portalen eller [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) cmdlet i Windows PowerShell. Noder tas också bort permanent 30 dagar efter att kontot som innehåller noden har tagits bort av en användare. |
| Nod rapporter |Tas bort permanent 90 dagar efter att en ny rapport har genererats för den noden |

Bevarandeprincipen gäller för alla användare och kan för närvarande inte anpassas.

Men om du behöver behålla data under en längre tid kan du vidarebefordra runbook-jobbloggar till Azure Monitor-loggar. Mer information finns i [vidarebefordra Azure Automation-jobbdata till Azure Monitor-loggar](automation-manage-send-joblogs-log-analytics.md).

## <a name="backing-up-azure-automation"></a>Säkerhetskopiera Azure Automation

När du tar bort ett automatiseringskonto i Microsoft Azure tas alla objekt i kontot bort, inklusive runbooks, moduler, konfigurationer, inställningar, jobb och resurser. Det går inte att återställa objekten när kontot har tagits bort.  Du kan använda följande information för att säkerhetskopiera innehållet i ditt automationskonto innan du tar bort det.

### <a name="runbooks"></a>Runbooks

Du kan exportera dina runbooks till skriptfiler med antingen Azure-portalen eller cmdleten [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) i Windows PowerShell. Dessa skriptfiler kan importeras till ett annat automatiseringskonto som beskrivs i [Skapa eller importera en runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Integreringsmoduler

Du kan inte exportera integrationsmoduler från Azure Automation. Du måste se till att de är tillgängliga utanför automationskontot.

### <a name="assets"></a>Tillgångar

Du kan inte exportera [resurser](/previous-versions/azure/dn939988(v=azure.100)) från Azure Automation.  Med hjälp av Azure-portalen måste du notera information om variabler, autentiseringsuppgifter, certifikat, anslutningar och scheman.  Du måste sedan manuellt skapa alla resurser som används av runbooks som du importerar till en annan automatisering.

Du kan använda [Azure-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) för att hämta information om okrypterade tillgångar och antingen spara dem för framtida referens eller skapa motsvarande tillgångar i ett annat automatiseringskonto.

Du kan inte hämta värdet för krypterade variabler eller lösenordsfältet för autentiseringsuppgifter med cmdlets. Om du inte känner till dessa värden kan du hämta dem från en runbook med aktiviteterna [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) och [Get-AutomationPSCredential.](/previous-versions/azure/dn940015(v=azure.100))

Du kan inte exportera certifikat från Azure Automation. Du måste se till att alla certifikat är tillgängliga utanför Azure.

### <a name="dsc-configurations"></a>DSC-konfigurationer

Du kan exportera dina konfigurationer till skriptfiler med antingen Azure-portalen eller [cmdleten Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) i Windows PowerShell. Dessa konfigurationer kan importeras och användas i ett annat automationskonto.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikering i Azure Automation

Geo-replikering, standard i Azure Automation-konton, säkerhetskopierar kontodata till en annan geografisk region för redundans. Du kan välja en primär region när du konfigurerar ditt konto och sedan tilldelas en sekundär region automatiskt. De sekundära data, kopieras från den primära regionen, uppdateras kontinuerligt i händelse av dataförlust.  

I följande tabell visas tillgängliga parapar för primära och sekundära regioner.

| Primär | Sekundär |
| --- | --- |
| USA, södra centrala |USA, norra centrala |
| USA, östra 2 |USA, centrala |
| Europa, västra |Europa, norra |
| Sydostasien |Asien, östra |
| Japan, östra |Japan, västra |

I den osannolika händelsen att en primär regiondata går förlorad försöker Microsoft återställa den. Om primärdata inte kan återställas utförs geo-redundans och de berörda kunderna meddelas om detta via sin prenumeration.
