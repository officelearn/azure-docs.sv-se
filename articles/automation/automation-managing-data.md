---
title: Hantera Azure Automation-data
description: Den här artikeln innehåller begrepp för data hantering i Azure Automation, inklusive data kvarhållning och säkerhets kopiering.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: de60ef31a39a698f9a797a5836546f9b75b67594
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835214"
---
# <a name="management-of-azure-automation-data"></a>Hantera Azure Automation-data

Den här artikeln innehåller flera ämnen för att hantera data i en Azure Automations miljö.

## <a name="data-retention"></a>Datakvarhållning

När du tar bort en resurs i Azure Automation sparas den i ett antal dagar för gransknings syfte innan permanent borttagning. Du kan inte se eller använda resursen under den här tiden. Den här principen gäller även för resurser som tillhör ett borttaget Automation-konto.

I följande tabell sammanfattas bevarande principen för olika resurser.

| Data | Policy |
|:--- |:--- |
| Konton |Ett konto tas bort permanent 30 dagar efter att användaren tagit bort det. |
| Tillgångar |En till gång tas bort permanent 30 dagar efter att användaren tagit bort den, eller 30 dagar efter att en användare har tagit bort ett konto som innehåller till gången. |
| DSC-noder |En DSC-nod tas bort permanent 30 dagar efter att ha avregistrerats från ett Automation-konto med hjälp av Azure Portal eller [unregister-AzAutomationDscNode-](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) cmdleten i Windows PowerShell. En nod tas också bort permanent 30 dagar efter att användaren tagit bort det konto som innehåller noden. |
| Jobb |Ett jobb tas bort och tas bort permanent 30 dagar efter ändringar, till exempel när jobbet har slutförts, stoppats eller har pausats. |
| Moduler |En modul tas bort permanent 30 dagar efter att användaren tagit bort den eller 30 dagar efter att användaren tagit bort det konto som innehåller modulen. |
| Nodkonfigurationer/MOF-filer |En gammal Node-konfiguration tas bort permanent 30 dagar efter att en ny nod har skapats. |
| Node-rapporter |En Node-rapport tas bort permanent 90 dagar efter att en ny rapport har genererats för noden. |
| Runbooks |En Runbook tas bort permanent 30 dagar efter att en användare tagit bort resursen, eller 30 dagar efter att användaren tagit bort det konto som innehåller resursen. |

Bevarande principen gäller för alla användare och kan för närvarande inte anpassas. Men om du behöver lagra data under en längre period kan du [vidarebefordra Azure Automation jobb data till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Säkerhets kopiering av data

När du tar bort ett Automation-konto i Azure raderas alla objekt i kontot. Objekten inkluderar Runbooks, moduler, konfigurationer, inställningar, jobb och till gångar. De kan inte återställas när kontot har tagits bort. Du kan använda följande information för att säkerhetskopiera innehållet i ditt Automation-konto innan du tar bort det.

### <a name="runbooks"></a>Runbooks

Du kan exportera dina runbooks till skriptfiler med antingen Azure Portal-eller [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) -cmdleten i Windows PowerShell. Du kan importera dessa skriptfiler till ett annat Automation-konto, enligt beskrivningen i [Hantera Runbooks i Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integreringsmoduler

Du kan inte exportera integrerings moduler från Azure Automation. Du måste göra dem tillgängliga utanför Automation-kontot.

### <a name="assets"></a>Tillgångar

Du kan inte exportera Azure Automation till gångar: certifikat, anslutningar, autentiseringsuppgifter, scheman och variabler. I stället kan du använda Azure Portal och Azure-cmdletar för att anteckna information om dessa till gångar. Använd sedan informationen för att skapa alla till gångar som används av Runbooks som du importerar till ett annat Automation-konto.

Du kan inte hämta värdena för krypterade variabler eller lösen ords fält för autentiseringsuppgifter med hjälp av cmdletar. Om du inte känner till dessa värden kan du hämta dem i en Runbook. Information om hur du hämtar variabel värden finns [i variabel till gångar i Azure Automation](shared-resources/variables.md). Om du vill veta mer om hur du hämtar autentiseringsuppgifter, se [inloggnings till gångar i Azure Automation](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>DSC-konfigurationer

Du kan exportera DSC-konfigurationerna till skriptfiler med antingen Azure Portal eller cmdleten [export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) i Windows PowerShell. Du kan importera och använda dessa konfigurationer i ett annat Automation-konto.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikering i Azure Automation

Geo-replikering är standard i Azure Automation-konton. Du väljer en primär region när du konfigurerar ditt konto. Den interna Automation-tjänsten för geo-replikering tilldelar kontot automatiskt en sekundär region. Tjänsten säkerhetskopierar sedan kontinuerligt konto data från den primära regionen till den sekundära regionen. Den fullständiga listan med primära och sekundära regioner finns i [verksamhets kontinuitet och haveri beredskap (BCDR): Azure-kopplade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Säkerhets kopian som skapats av tjänsten Automation geo-Replication är en fullständig kopia av Automation-tillgångar, konfigurationer och liknande. Den här säkerhets kopian kan användas om den primära regionen slutar fungera och förlorar data. Om det osannoliks att data för en primär region förloras försöker Microsoft återställa det. Om företaget inte kan återställa primära data använder den automatisk redundans och informerar dig om situationen genom din Azure-prenumeration. 

Tjänsten Automation geo-Replication är inte tillgänglig direkt för externa kunder om det uppstår ett regionalt haveri. Om du vill underhålla automatiserings konfiguration och Runbooks under regionala haverier:

1. Välj en sekundär region som du vill koppla till det geografiska området för ditt primära Automation-konto.

2. Skapa ett Automation-konto i den sekundära regionen.

3. Exportera dina runbooks som skriptfiler i det primära kontot.

4. Importera runbooks till ditt Automation-konto i den sekundära regionen.

## <a name="next-steps"></a>Nästa steg

* Mer information om säkra till gångar i Azure Automation finns i [kryptering av säkra till gångar i Azure Automation](automation-secure-asset-encryption.md).
* Mer information om geo-replikering finns i [skapa och använda aktiv geo-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).