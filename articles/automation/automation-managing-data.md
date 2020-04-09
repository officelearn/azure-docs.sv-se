---
title: Hantera Azure Automation-data
description: Den här artikeln innehåller flera avsnitt för hantering av en Azure Automation-miljö.  Innehåller för närvarande datalagring och säkerhetskopiering av Azure Automation Disaster Recovery i Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984665"
---
# <a name="managing-azure-automation-data"></a>Hantera Azure Automation-data

Den här artikeln innehåller flera avsnitt för hantering av data i en Azure Automation-miljö.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="data-retention"></a>Datakvarhållning

När du tar bort en resurs i Azure Automation behålls den i ett antal dagar för granskning innan den tas bort permanent. Du kan inte se eller använda resursen under den här tiden. Den här principen gäller även resurser som tillhör ett borttaget Automation-konto.

I följande tabell sammanfattas bevarandeprincipen för olika resurser.

| Data | Princip |
|:--- |:--- |
| Konton |Ett konto tas bort permanent 30 dagar efter att en användare har tagit bort det. |
| Tillgångar |En tillgång tas bort permanent 30 dagar efter att en användare har tagit bort den, eller 30 dagar efter att en användare har tagit bort ett konto som innehåller tillgången. |
| DSC-noder |En DSC-nod tas bort permanent 30 dagar efter att den har avregistrerats från ett Automation-konto med Azure-portalen eller [Cmdlet Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) i Windows PowerShell. En nod tas också bort permanent 30 dagar efter att en användare har tagit bort kontot som innehåller noden. |
| Jobb |Ett jobb tas bort och tas bort permanent 30 dagar efter ändringen, till exempel när jobbet har slutförts, har stoppats eller pausats. |
| Moduler |En modul tas bort permanent 30 dagar efter att en användare har tagit bort den, eller 30 dagar efter att en användare har tagit bort kontot som innehåller modulen. |
| Nodkonfigurationer/MOF-filer |En gammal nodkonfiguration tas bort permanent 30 dagar efter att en ny nodkonfiguration har genererats. |
| Nod rapporter |En nodrapport tas bort permanent 90 dagar efter att en ny rapport har genererats för den noden. |
| Runbooks |En runbook tas bort permanent 30 dagar efter att en användare har tagit bort resursen, eller 30 dagar efter att en användare har tagit bort kontot som innehåller resursen. |

Bevarandeprincipen gäller för alla användare och kan för närvarande inte anpassas. Om du behöver behålla data under en längre period kan du [vidarebefordra Azure Automation-jobbdata till Azure Monitor-loggar](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Säkerhetskopiering av data

När du tar bort ett Automation-konto i Azure tas alla objekt i kontot bort. Objekten innehåller runbooks, moduler, konfigurationer, inställningar, jobb och tillgångar. De kan inte återställas när kontot har tagits bort. Du kan använda följande information för att säkerhetskopiera innehållet i ditt Automation-konto innan du tar bort det.

### <a name="runbooks"></a>Runbooks

Du kan exportera dina runbooks till skriptfiler med antingen Azure-portalen eller cmdleten [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) i Windows PowerShell. Du kan importera dessa skriptfiler till ett annat Automation-konto, som beskrivs i [Hantera runbooks i Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integreringsmoduler

Du kan inte exportera integrationsmoduler från Azure Automation. Du måste göra dem tillgängliga utanför Automation-kontot.

### <a name="assets"></a>Tillgångar

Du kan inte exportera Azure Automation-resurser: certifikat, anslutningar, autentiseringsuppgifter, scheman och variabler. I stället kan du använda Azure-portalen och Azure-cmdlets för att notera information om dessa tillgångar. Använd sedan den här informationen för att skapa alla tillgångar som används av runbooks som du importerar till ett annat Automation-konto.

Du kan inte hämta värdena för krypterade variabler eller lösenordsfälten för autentiseringsuppgifter med cmdletar. Om du inte känner till dessa värden kan du hämta dem i en runbook. Information om hur du hämtar variabla värden finns [i Variabla resurser i Azure Automation](shared-resources/variables.md). Mer information om hur du hämtar autentiseringsuppgifter finns [i Autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>DSC-konfigurationer

Du kan exportera DSC-konfigurationer till skriptfiler med antingen Azure-portalen eller [cmdleten Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) i Windows PowerShell. Du kan importera och använda dessa konfigurationer i ett annat Automation-konto.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikering i Azure Automation

Geo-replikering är standard i Azure Automation-konton. Du väljer en primär region när du konfigurerar ditt konto. Den interna tjänsten Automation geo-replication tilldelar kontot en sekundär region automatiskt. Tjänsten säkerhetskopierar sedan kontinuerligt kontodata från den primära regionen till den sekundära regionen. Den fullständiga listan över primära och sekundära regioner finns på [Business Continuity and disaster recovery (BCDR): Azure Paired Regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Säkerhetskopian som skapas av tjänsten Automation geo-replication är en komplett kopia av Automation-tillgångar, konfigurationer och liknande. Den här säkerhetskopian kan användas om den primära regionen går ner och förlorar data. I den osannolika händelsen att data för en primär region går förlorade försöker Microsoft återställa den. Om företaget inte kan återställa de primära data, använder det automatisk redundans och informerar dig om situationen via din Azure-prenumeration. 

Tjänsten Automations georeplikering är inte direkt tillgänglig för externa kunder om det uppstår ett regionalt fel. Om du vill behålla Automation-konfiguration och runbooks vid regionala fel:

1. Välj en sekundär region som ska paras ihop med det geografiska området i ditt primära Automation-konto.

2. Skapa ett Automation-konto i den sekundära regionen.

3. I det primära kontot exporterar du runbooks som skriptfiler.

4. Importera runbooks till ditt Automation-konto i den sekundära regionen.

## <a name="next-steps"></a>Nästa steg

* Mer information om säkra tillgångar i Azure Automation finns i [Kryptera säkra resurser i Azure Automation](automation-secure-asset-encryption.md).

* Mer information om georeplikering finns i [Skapa och använda aktiv georeplikering](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).