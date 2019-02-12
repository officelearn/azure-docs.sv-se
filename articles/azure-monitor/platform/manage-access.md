---
title: Hantera Log Analytics-arbetsytor i Azure Monitor | Microsoft Docs
description: Du kan hantera Log Analytics-arbetsytor i Azure Monitor med en rad olika administrativa uppgifter på användare, konton, arbetsytor och Azure-konton.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: 4a777c2bd57d40b4bb6c8d36c996b655cb019e5f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005378"
---
# <a name="manage-log-analytics-workspaces-in-azure-monitor"></a>Hantera Log Analytics-arbetsytor i Azure Monitor
Azure Monitor-butiker logga data över en Log Analytics-arbetsyta som är i grunden en behållare som innehåller data och konfigurationsinformation. För att hantera åtkomst för att logga data måste utföra du olika administrativa uppgifter relaterade till arbetsytor. Du eller andra medlemmar i din organisation kan använda flera arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

För att skapa en arbetsyta måste du:

1. Ha en Azure-prenumeration.
2. Välja ett arbetsytenamn.
3. Associera arbetsytan med en av dina prenumerationer och resursgrupper.
4. Välja en geografisk plats.

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestämma antalet arbetsytor du behöver
En Log Analytics-arbetsyta är en Azure-resurs och är en behållare där data samlas in, aggregeras, analyseras och presenteras i Azure Monitor.

Du kan ha flera arbetsytor per Azure-prenumeration och du kan ha åtkomst till fler än en arbetsyta, med möjlighet att enkelt söka i dem. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

Tillhandahåller en Log Analytics-arbetsyta:

* En geografisk plats för lagring av data
* Dataisolering att definiera olika användare behörighet
* För konfigurationen av inställningarna som [prisnivån](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [kvarhållning](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) och [data tak som skall](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 

Från förbrukning synsätt rekommenderar vi att du skapar så få arbetsytor som möjligt. Det gör administration och fråga upplevelse för enklare och snabbare. Men baserat på föregående egenskaper kan du behöva skapa flera arbetsytor om:

* Du är ett globalt företag och du behöver logga data som lagras i vissa områden för datasuveränitet eller kompatibilitetsskäl i data.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du vill fördela tillägg till olika avdelningar eller affärsgrupper baserat på deras användning genom att skapa en arbetsyta för varje avdelning eller affärsgrupp i sin egen Azure-prenumeration.
* Du är leverantör av hanterade tjänster och behöver Log Analytics-data för varje kund du hanterar isolerade från andra kunders data.
* Du hanterar flera kunder och vill att varje kund / avdelning / affärsgrupp ska se sina egna data, men inte data från andra.

När du använder Windows-agenter för att samla in data måste du [konfigurera varje agent så att den rapporterar till en eller flera arbetsytor](../../azure-monitor/platform/agent-windows.md).

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Du kan installera Microsoft Monitoring Agent på datorer som hanteras av Operations Manager och låta agenten rapporten till både Operations Manager och en annan Log Analytics-arbetsyta.

## <a name="workspace-information"></a>Arbetsyteinformation
När du analyserar data i Log Analytics-arbetsytan i den **Azure Monitor** menyn i Azure-portalen, som du skapar och hanterar arbetsytor i den **Log Analytics-arbetsytor** menyn.
 

1. Logga in på den [Azure-portalen](https://portal.azure.com) och klicka på **alla tjänster**. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics** arbetsytor.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Välj din arbetsyta i listan.

4. Sidan för arbetsytan visar information om att komma igång, konfiguration och länkar till ytterligare information.  

    ![Information om arbetsytan](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Hantera konton och användare
Varje arbetsyta kan ha flera associerade konton, och varje konto kan ha åtkomst till flera arbetsytor. Åtkomst hanteras [Azure rollbaserad åtkomst](../../role-based-access-control/role-assignments-portal.md). Dessa behörigheter gäller både på Azure portal och API-åtkomst.


Följande aktiviteter kräver även Azure-behörigheter:

| Åtgärd                                                          | Azure-behörigheter krävs | Anteckningar |
|-----------------------------------------------------------------|--------------------------|-------|
| Lägga till och ta bort hanteringslösningar                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Behörigheterna måste beviljas på resursgrupp- eller prenumerationsnivå. |
| Ändra prisnivån                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visa data i lösningspanelerna *säkerhetskopiering* och *Site Recovery* | Administratör/medadministratör | Åtkomst till resurser som distribueras med den klassiska distributionsmodellen |
| Skapa en arbetsyta i Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-workspace-using-azure-permissions"></a>Hantera åtkomst till Log Analytics-arbetsyta med hjälp av Azure-behörigheter
Om du vill bevilja åtkomst till Log Analytics-arbetsytan med Azure-behörigheter följer du stegen i [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../../role-based-access-control/role-assignments-portal.md).

Azure har två inbyggda användarroller för Log Analytics-arbetsytor:
- Log Analytics Reader
- Log Analytics Contributor

Medlemmar av *Log Analytics Reader*-rollen kan:
- Visa och söka i alla övervakningsdata 
- Visa övervakningsinställningar, även konfiguration av Azure-diagnostik för alla Azure-resurser.

Läsarroll för Log Analytics innehåller följande Azure åtgärder:

| Typ    | Behörighet | Beskrivning |
| ------- | ---------- | ----------- |
| Åtgärd | `*/read`   | Möjlighet att visa alla Azure-resurser och resurskonfigurationen. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure-diagnostik för resurser <br> Alla egenskaper och inställningar för alla resurser |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Kan utföra Log Search v2-sökfrågor |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/search/action` | Kan utföra Log Search v1-sökfrågor |
| Åtgärd | `Microsoft.Support/*` | Kan öppna supportärenden |
|Ingen åtgärd | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Förhindrar läsning av arbetsytenyckeln nyckel som krävs för att använda datasamlings-API och installera agenter. Detta hindrar användaren från att lägga till nya resurser till arbetsytan |


Medlemmar av *Log Analytics Contributor*-rollen kan:
- Läsa alla övervakningsdata som Log Analytics Reader  
- Skapa och konfigurera Automation -konton  
- Lägga till och ta bort hanteringslösningar    
    > [!NOTE] 
    > För att kunna genomföra de sista två åtgärderna, måste den här behörigheten beviljas resource group eller på prenumerationsnivån.  

- Läsa lagringskontonycklar   
- Konfigurera loggsamlingar från Azure Storage  
- Redigera övervakningsinställningar för Azure-resurser, bland annat
  - Lägga till tillägg för virtuell dator i virtuella datorer
  - Konfigurera Azure-diagnostik på alla Azure-resurser

> [!NOTE] 
> Du kan använda möjligheten att lägga till ett virtuellt datortillägg i en virtuell dator för att få fullständig kontroll över datorn.

Rollen Log Analytics Contributor innehåller följande Azure åtgärder:

| Behörighet | Beskrivning |
| ---------- | ----------- |
| `*/read`     | Kan visa alla resurser och resurskonfigurationer. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure-diagnostik för resurser <br> Alla egenskaper och inställningar för alla resurser |
| `Microsoft.Automation/automationAccounts/*` | Kan skapa och konfigurera Azure Automation-konton, inklusive lägga till och redigera runbookflöden |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Lägga till, uppdatera och ta bort virtuella datortillägg, även Microsoft Monitoring Agent-tillägget och OMS Agent for Linux-tillägget |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Visa lagringskontonyckeln. Krävs för att ställa in Log Analytics för läsning av loggar från Azure-lagringskonton |
| `Microsoft.Insights/alertRules/*` | Lägga till, uppdatera och ta bort aviseringsregler |
| `Microsoft.Insights/diagnosticSettings/*` | Lägga till, uppdatera och ta bort diagnostikinställningar på Azure-resurser |
| `Microsoft.OperationalInsights/*` | Lägga till, uppdatera och ta bort konfigurationer för Log Analytics-arbetsytor |
| `Microsoft.OperationsManagement/*` | Lägga till och ta bort hanteringslösningar |
| `Microsoft.Resources/deployments/*` | Skapa och ta bort distributioner. Krävs för att lägga till och ta bort lösningar, arbetsytor och Automation-konton |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Skapa och ta bort distributioner. Krävs för att lägga till och ta bort lösningar, arbetsytor och Automation-konton |

För att kunna lägga till och ta bort användare i en användarroll måste du ha behörigheten `Microsoft.Authorization/*/Delete` och `Microsoft.Authorization/*/Write`.

Använd de här rollerna för att ge användare åtkomst med olika omfång:
- Prenumeration: åtkomst till alla arbetsytor i prenumerationen
- Resursgrupp: åtkomst till alla arbetsytor i resursgruppen
- Resurs: endast åtkomst till en angiven arbetsyta

Vi rekommenderar att du utför tilldelningar på resursnivå (arbetsyta) för att garantera korrekt åtkomstkontroll.  Använd [anpassade roller](../../role-based-access-control/custom-roles.md) för att skapa roller med specifik behörighet.

## <a name="next-steps"></a>Nästa steg
* Se [översikt över Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md) samla in data från datorer i ditt datacenter eller andra moln.
* Om du vill konfigurera datainsamling från virtuella Azure-datorer läser du [Samla in data om Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md).  

