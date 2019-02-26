---
title: Hantera Azure Automation-data
description: Den här artikeln innehåller flera avsnitt för att hantera en Azure Automation-miljö.  För närvarande innehåller kvarhållning av Data och säkerhetskopiera Azure Automation-katastrofåterställning i Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3e217e0e3367c6e1200567f589749fec9e626da8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817464"
---
# <a name="managing-azure-automation-data"></a>Hantera Azure Automation-data
Den här artikeln innehåller flera avsnitt för att hantera en Azure Automation-miljö.

## <a name="data-retention"></a>Datakvarhållning
När du tar bort en resurs i Azure Automation finns den kvar i 90 dagar för granskning innan de tas bort permanent.  Du kan inte se eller använda resursen under den här tiden.  Den här principen gäller även för resurser som tillhör ett automation-konto som har tagits bort.

Azure Automation tar automatiskt bort och tar permanent bort jobb som är äldre än 90 dagar.

I följande tabell sammanfattas bevarandeprincipen för olika resurser.

| Data | Princip |
|:--- |:--- |
| Konton |Bort permanent 90 dagar efter att kontot har tagits bort av en användare. |
| Tillgångar |Bort permanent 90 dagar efter tillgången har tagits bort av en användare eller 90 dagar efter det konto som innehåller tillgången har tagits bort av en användare. |
| Moduler |Bort permanent 90 dagar efter att modulen tas bort av en användare eller 90 dagar efter det konto som innehåller modulen tas bort av en användare. |
| Runbooks |Bort permanent 90 dagar när resursen har tagits bort av en användare eller 90 dagar efter det konto som innehåller resursen tas bort av en användare. |
| Jobb |Borttagna och har tagits bort permanent 90 dagar efter senaste som ska ändras. Detta kan vara när jobbet har slutförts, stoppas eller pausas. |
| Noden konfigurationer/MOF-filer |Gamla nodkonfiguration bort permanent 90 dagar efter att en ny nodkonfiguration genereras. |
| DSC-noder |Bort permanent 90 dagar efter att noden avregistreras från Automation-konto med hjälp av Azure portal eller [avregistrera AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) cmdlet i Windows PowerShell. Noder tas även permanent bort 90 dagar efter det konto som innehåller noden tas bort av en användare. |
| Noden rapporter |Bort permanent 90 dagar efter att en ny rapport skapas för noden |

Bevarandeprincipen gäller för alla användare och för närvarande kan inte anpassas.

Men om du vill behålla data under en längre tidsperiod kan du skicka vidare runbook Azure Monitor-loggar i jobbloggfilerna.  Mer information, [vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Säkerhetskopiera Azure Automation
När du tar bort ett automation-konto i Microsoft Azure kan tas alla objekt i kontot bort, inklusive runbooks, moduler, konfigurationer, inställningar, jobb och tillgångar. Objekten kan inte återställas när kontot har tagits bort.  Du kan använda följande information för att säkerhetskopiera innehållet i ditt automation-konto innan de tas bort. 

### <a name="runbooks"></a>Runbooks
Du kan exportera runbooks till skriptfiler med Azure portal eller [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet i Windows PowerShell.  Dessa filer kan importeras till ett annat automation-konto som beskrivs i [skapa eller importera en Runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Integreringsmoduler
Du kan inte exportera integreringsmoduler från Azure Automation.  Du måste se till att de är tillgängliga utanför automation-kontot.

### <a name="assets"></a>Tillgångar
Du kan inte exportera [tillgångar](https://msdn.microsoft.com/library/dn939988.aspx) från Azure Automation.  Med Azure-portalen, måste du Observera information om variabler, autentiseringsuppgifter, certifikat, anslutningar och scheman.  Därefter måste du manuellt skapa tillgångar som används av runbooks som importeras till en annan automation.

Du kan använda [Azure-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) att hämta information om okrypterade tillgångar och antingen spara dem för framtida bruk eller skapa motsvarande tillgångar i en annan automation-konto.

Du kan inte hämta värdet för krypterade variabler eller lösenordsfältet av autentiseringsuppgifter med hjälp av cmdlet: ar.  Om du inte vet dessa värden, så du kan hämta dem från en runbook med hjälp av den [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) och [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) aktiviteter.

Du kan inte exportera certifikat från Azure Automation.  Du måste se till att alla certifikat är tillgängliga utanför Azure.

### <a name="dsc-configurations"></a>DSC-konfigurationer
Du kan exportera dina konfigurationer till skriptfiler med Azure portal eller [Export AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) cmdlet i Windows PowerShell. De här konfigurationerna kan importeras och används i ett annat automation-konto.

## <a name="geo-replication-in-azure-automation"></a>GEO-replikering i Azure Automation
GEO-replikering, som standard i Azure Automation-konton, säkerhetskopierar kontodata till en annan geografisk region för redundans. Du kan välja en primär region när du konfigurerar ditt konto och en sekundär region tilldelas till den automatiskt. De sekundära data som kopieras från den primära regionen, uppdateras kontinuerligt vid dataförlust.  

I följande tabell visas tillgängliga primära och sekundära regionskopplingar.

| Primär | Sekundär |
| --- | --- |
| Södra centrala USA |Norra centrala USA |
| USA, östra 2 |Centrala USA |
| Västra Europa |Norra Europa |
| Sydostasien |Östasien |
| Östra Japan |Västra Japan |

Microsoft försöker återställa den det osannolika att en primär regiondata går förlorad. Om den primära data inte kan återställas, sedan geo-replikeringsedundans utförs är och berörda kunder kommer att meddelas om detta via sin prenumeration.


