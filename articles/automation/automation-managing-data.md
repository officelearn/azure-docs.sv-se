---
title: Hantera Azure Automation data | Microsoft Docs
description: "Den här artikeln innehåller flera avsnitt för att hantera en Azure Automation-miljö.  För närvarande innehåller datalagring och säkerhetskopiering av Azure Automation-katastrofåterställning i Azure Automation."
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: 2896f129-82e3-43ce-b9ee-a3860be0423a
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/02/201
ms.author: magoedte;bwren;sngun
ms.openlocfilehash: e4a90f47167cfa2497e1ad5ae9db025d2d7d91dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-automation-data"></a>Hantera Azure Automation-data
Den här artikeln innehåller flera avsnitt för att hantera en Azure Automation-miljö.

## <a name="data-retention"></a>Datakvarhållning
När du tar bort en resurs i Azure Automation finns den kvar i 90 dagar för granskning innan den tas bort permanent.  Du kan inte se eller använda resursen under denna tid.  Den här principen gäller även för resurser som tillhör ett automation-konto som har tagits bort.

Azure Automation tas bort automatiskt och tar permanent bort jobb som är äldre än 90 dagar.

I följande tabell sammanfattas bevarandeprincipen för olika resurser.

| Data | Princip |
|:--- |:--- |
| Konton |Tas bort permanent 90 dagar efter att användaren ta bort kontot. |
| Tillgångar |Tas bort permanent 90 dagar efter tillgången har tagits bort av en användare eller 90 dagar efter det konto som innehåller tillgången tas bort av en användare. |
| Moduler |Tas bort permanent 90 dagar efter att modulen tas bort av en användare eller 90 dagar efter det konto som innehåller modulen tas bort av en användare. |
| Runbooks |Tas bort permanent 90 dagar efter resursen har tagits bort av en användare eller 90 dagar efter det konto som innehåller resursen tas bort av en användare. |
| Jobb |Borttagna och tas bort permanent 90 dagar efter det sista som ska ändras. Detta kan vara när jobbet har slutförts, stoppas eller pausas. |
| Noden konfigurationer/MOF-filer |Gamla nodkonfiguration bort permanent 90 dagar efter att en ny konfiguration av noden genereras. |
| DSC-noder |Bort permanent 90 dagar efter att noden har avregistrerats för Automation-konto med hjälp av Azure portal eller [Unregister-AzureRMAutomationDscNode](https://msdn.microsoft.com/library/mt603500.aspx) cmdlet i Windows PowerShell. Noder tas bort 90 dagar efter det konto som innehåller noden tas bort av en användare även permanent. |
| Noden rapporter |Bort permanent 90 dagar efter att en ny rapport skapas för noden |

Bevarandeprincipen gäller för alla användare och för närvarande inte kan anpassas.

Men om du vill behålla data under en längre tidsperiod, kan du vidarebefordra runbook paketjobbsloggarna till logganalys.  Mer information finns [vidarebefordra Azure Automation jobbdata till OMS logganalys](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Säkerhetskopiera Azure Automation
När du tar bort ett automation-konto i Microsoft Azure tas alla objekt i kontot bort inklusive runbooks, moduler, konfigurationer, inställningar, jobb och tillgångar. Objekten kan inte återställas när kontot har tagits bort.  Du kan använda följande information för att säkerhetskopiera innehållet i ditt automation-konto innan den tas bort. 

### <a name="runbooks"></a>Runbooks
Du kan exportera dina runbooks till filer med hjälp av antingen Azure-hanteringsportalen eller [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) cmdlet i Windows PowerShell.  Dessa filer kan importeras till en annan automation-konto som beskrivs i [skapa eller importera en Runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Integreringsmoduler
Du kan inte exportera integreringsmoduler från Azure Automation.  Du måste se till att de är tillgängliga utanför automation-kontot.

### <a name="assets"></a>Tillgångar
Du kan inte exportera [tillgångar](https://msdn.microsoft.com/library/dn939988.aspx) från Azure Automation.  Med hjälp av Azure-hanteringsportalen, måste du Observera information om variabler, autentiseringsuppgifter, certifikat, anslutningar och scheman.  Därefter måste du manuellt skapa alla objekt som används av runbooks som importeras till en annan automation.

Du kan använda [Azure-cmdlets](https://msdn.microsoft.com/library/dn690262.aspx) att hämta information om okrypterad tillgångar och antingen spara dem för framtida bruk eller skapa motsvarande tillgångar i en annan automation-kontot.

Du kan inte hämta värdet för krypterade variabler eller lösenordsfältet autentiseringsuppgifter med hjälp av cmdlet: ar.  Om du inte känner till dessa värden, så du kan hämta dem från en runbook med den [Get-automationvariable,](https://msdn.microsoft.com/library/dn940012.aspx) och [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) aktiviteter.

Du kan inte exportera certifikat från Azure Automation.  Du måste se till att det finns några certifikat utanför Azure.

### <a name="dsc-configurations"></a>DSC-konfigurationer
Du kan exportera dina konfigurationer till filer med hjälp av antingen Azure-hanteringsportalen eller [Export AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) cmdlet i Windows PowerShell. De här konfigurationerna kan importeras och används i en annan automation-kontot.

## <a name="geo-replication-in-azure-automation"></a>GEO-replikering i Azure Automation
GEO-replikering, standard i Azure Automation-konton, säkerhetskopierar kontodata till en annan geografisk region för redundans. Du kan välja en primär region när du konfigurerar ditt konto och en sekundär region tilldelas den automatiskt. Den sekundära data som kopieras från den primära regionen uppdateras kontinuerligt händelse av dataförlust.  

I följande tabell visas tillgängliga primära och sekundära region pairings.

| Primär | Sekundär |
| --- | --- |
| Södra centrala USA |Norra centrala USA |
| Östra USA 2 |Centrala USA |
| Västra Europa |Norra Europa |
| Sydostasien |Östasien |
| Östra Japan |Västra Japan |

Microsoft försöker återställa den förmodan som en primär regiondata går förlorade. Om den primära data inte kan återställas, kommer att utföras geo-redundans och berörda kunder ska meddelas om detta via sin prenumeration.

