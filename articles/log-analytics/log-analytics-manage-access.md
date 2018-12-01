---
title: Hantera arbetsytor i Azure Log Analytics och OMS-portalen | Microsoft-dokument
description: Du kan hantera arbetsytor i Azure Log Analytics och OMS-portalen med hjälp av olika administrativa uppgifter för användare, konton, arbetsytor och Azure-konton.
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
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 19af09e3c335d9b83fc5568014cba70630d25d24
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720821"
---
# <a name="manage-workspaces"></a>Hantera arbetsytor

Du hanterar åtkomsten till Log Analytics genom att utföra olika administrativa uppgifter relaterade till arbetsytor. Den här artikeln innehåller råd och procedurer för att hantera arbetsytor. En arbetsyta är i grunden en container som innehåller kontoinformation och enkel konfigurationsinformation för kontot. Du eller andra medlemmar i din organisation kan använda flera arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

För att skapa en arbetsyta måste du:

1. Ha en Azure-prenumeration.
2. Välja ett arbetsytenamn.
3. Associera arbetsytan med en av dina prenumerationer och resursgrupper.
4. Välja en geografisk plats.

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestämma antalet arbetsytor du behöver
En arbetsyta är en Azure-resurs och en container där data samlas in, aggregeras, analyseras och presenteras på Azure Portal.

Du kan ha flera arbetsytor per Azure-prenumeration och du kan ha åtkomst till fler än en arbetsyta, med möjlighet att snabbt göra förfrågningar i alla. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

För närvarande tillhandahåller en arbetsyta:

* En geografisk plats för lagring av data
* Dataisolering att definiera olika användare behörighet
* Omfång för konfiguration av inställningar som kvarhållning och data tak som skall

Från förbrukning synsätt rekommenderar vi att du skapar så få arbetsytor som möjligt. Det gör administration och fråga upplevelse för enklare och snabbare. Men baserat på föregående egenskaper kan du behöva skapa flera arbetsytor om:

* Du är ett globalt företag och data behöver lagras i vissa områden för datasuveränitet eller kompatibilitetsskäl.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du vill fördela tillägg till olika avdelningar eller affärsgrupper baserat på deras användning genom att skapa en arbetsyta för varje avdelning eller affärsgrupp i sin egen Azure-prenumeration.
* Du är leverantör av hanterade tjänster och behöver Log Analytics-data för varje kund du hanterar isolerade från andra kunders data.
* Du hanterar flera kunder och vill att varje kund / avdelning / affärsgrupp ska se sina egna data, men inte data från andra.

När du använder Windows-agenter för att samla in data måste du [konfigurera varje agent så att den rapporterar till en eller flera arbetsytor](../azure-monitor/platform/agent-windows.md).

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Du kan installera Microsoft Monitoring Agent på datorer som hanteras av Operations Manager och låta agenten rapporten till både Operations Manager och en annan Log Analytics-arbetsyta.

## <a name="workspace-information"></a>Arbetsyteinformation

Du kan visa information om din arbetsyta på Azure Portal. 

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com).

2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.  

    ![Azure Portal](media/log-analytics-manage-access/azure-portal-01.png)  

3. Välj en arbetsyta i fönstret Log Analytics-prenumerationer.

4. Sidan för arbetsytan visar information om att komma igång, konfiguration och länkar till ytterligare information.  

    ![Information om arbetsytan](./media/log-analytics-manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Hantera konton och användare
Varje arbetsyta kan ha flera associerade konton, och varje konto kan ha åtkomst till flera arbetsytor. Åtkomst hanteras [Azure rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md). Dessa behörigheter gäller både på Azure portal och API-åtkomst.


Följande aktiviteter kräver även Azure-behörigheter:

| Åtgärd                                                          | Azure-behörigheter krävs | Anteckningar |
|-----------------------------------------------------------------|--------------------------|-------|
| Lägga till och ta bort hanteringslösningar                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Behörigheterna måste beviljas på resursgrupp- eller prenumerationsnivå. |
| Ändra prisnivån                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visa data i lösningspanelerna *säkerhetskopiering* och *Site Recovery* | Administratör/medadministratör | Åtkomst till resurser som distribueras med den klassiska distributionsmodellen |
| Skapa en arbetsyta i Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Hantera åtkomst till Log Analytics med Azure-behörighet
Om du vill bevilja åtkomst till Log Analytics-arbetsytan med Azure-behörigheter följer du stegen i [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md).

Azure har två inbyggda användarroller för Log Analytics:
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

Vi rekommenderar att du utför tilldelningar på resursnivå (arbetsyta) för att garantera korrekt åtkomstkontroll.  Använd [anpassade roller](../role-based-access-control/custom-roles.md) för att skapa roller med specifik behörighet.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Länka en befintlig arbetsyta till en Azure-prenumeration
Alla arbetsytor som skapats efter den 26 september 2016 måste kopplas till en Azure-prenumeration vid tidpunkten för skapandet. Arbetsytor som skapats innan det här datumet måste kopplas till en arbetsyta när du loggar in. När du skapar arbetsytan från Azure Portal eller när du länkar arbetsytan till en Azure-prenumeration länkas din Azure Active Directory som ditt organisationskonto.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Om du vill länka en arbetsyta till en Azure-prenumeration i Azure-portalen
1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.  

2. Klicka på fönstret Log Analytics-prenumerationer **Lägg till**.  

    ![lista över arbetsytor](./media/log-analytics-manage-access/workspace-link-existing-01.png)

3. Från den **Log Analytics-arbetsyta** fönstret klickar du på **länka befintliga**.  

4. Klicka på **Konfigurera nödvändiga inställningar**.  

5. Du ser listan över arbetsytor som ännu inte har länkats till ditt Azure-konto. Välj arbetsytan.  
   
6. Om det behövs, kan du ändra värdena för följande objekt:
   * Prenumeration
   * Resursgrupp
   * Plats
   * Prisnivå  

7. Klicka på **OK**. Arbetsytan är nu länkad till ditt Azure-konto.

> [!NOTE]
> Om du inte ser den arbetsyta du vill länka så har inte Azure-prenumerationen åtkomst till den arbetsyta du skapade via OMS-portalen.  Läs [Lägga till en användare i en befintlig arbetsyta](#add-a-user-to-an-existing-workspace) om du vill bevilja åtkomst till kontot från OMS-portalen.
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Uppgradera en arbetsyta till en betald plan
Det finns tre typer av planer för arbetsytor i OMS: **Kostnadsfri**, **Fristående** och **OMS**.  Om du har planen *Kostnadsfri* finns det en gräns på 500 MB data per dag som kan skickas till Log Analytics.  Om du överskrider denna mängd måste du ändra din arbetsyta till en betald plan för att undvika att data inte samlas in utöver denna gräns. Du kan ändra din plantyp när som helst.  Mer information om OMS-priser finns i [Prisinformation](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Använda rättigheter från en OMS-prenumeration
För att använda rättigheter som kommer från inköp av OMS E1, OMS E2 OMS eller MS-tillägg för System Center väljer du *OMS*-planen för OMS Log Analytics.

När du köper en OMS-prenumeration läggs rättigheterna till i ditt Enterprise Agreement. Alla Azure-prenumerationer som har skapats under det här avtalet kan använda rättigheterna. Alla arbetsytor på dessa prenumerationer använder OMS-rättigheter.

För att säkerställa att användningen av en arbetsyta tillämpas på dina rättigheter från OMS-prenumerationen måste du:

1. Skapa din arbetsyta i en Azure-prenumeration som är en del av ett Enterprise Agreement som omfattar OMS-prenumerationen

2. Välja planen *OMS* för arbetsytan

> [!NOTE]
> Om din arbetsyta skapades innan 26 september 2016 och din Log Analytics-prisplan är *Premium* kommer den här arbetsytan att använda rättigheter från OMS tillägget för System Center. Du kan också använda dina rättigheter genom att ändra till prisnivån *OMS*.
>
>

OMS-prenumerationens rättigheter är inte synliga i Azure-portalen. Du kan se rättigheter och användning i Enterprise Portal.  

Om du behöver ändra Azure-prenumerationen som arbetsytan är länkad till kan du använda Azure PowerShell-cmdlet:en [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Använda Azure-åtagande från ett Enterprise-avtal
Om du inte har en OMS-prenumeration betalar du separat för varje komponent i OMS och användningen visas på din Azure-faktura.

Om du har ett Azure-betalningsåtagande på företagsregistreringen som är kopplad till dina Azure-prenumerationer kommer all användning av Log Analytics automatiskt att debiteras mot eventuella återstående betalningsåtaganden.

Om du behöver ändra Azure-prenumerationen som arbetsytan är länkad till kan du använda Azure PowerShell-cmdlet:en [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Ändra en arbetsyta till en prisnivå i Azure Portal
1. Välj en arbetsyta från fönstret Log Analytics-prenumerationer i Azure-portalen.

2. Från fönstret med arbetsytans under **Allmänt**väljer **prisnivå**.  

3. Under **prisnivå**, Välj en prisnivå och klickar sedan på **Välj**.  
    ![Valt prisplanen](./media/log-analytics-manage-access/workspace-pricing-tier-info.png)

> [!NOTE]
> Om arbetsytan är länkad till ett Automation-konto måste du ta bort alla **Automation and Control**-lösningar och ta bort länken för Automation-kontot innan du kan välja prisnivån *Fristående (per GB)*. I arbetsytebladet klickar du på **Lösningar** under **Allmänt** för att visa och ta bort lösningar. Du tar bort länken för Automation-kontot genom att klicka på namnet på Automation-kontot på bladet **Prisnivå**.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Ändra en arbetsyta till en prisnivå i OMS-portalen

Om du vill ändra prisnivån med OMS-portalen måste du ha en Azure-prenumeration.

1. Klicka på panelen **Inställningar** på OMS-portalen.

2. Klicka på fliken **Konton** och klicka sedan på fliken **Azure-prenumeration och dataplan**.

3. Klicka på den prisnivå som du vill använda.

4. Klicka på **Spara**.  

    ![prenumeration och dataplaner](./media/log-analytics-manage-access/subscription-tab.png)

Den nya dataplanen visas på menyfliken i OMS-portalen längst upp på webbsidan.

![OMS-menyflikar](./media/log-analytics-manage-access/data-plan-changed.png)

## <a name="next-steps"></a>Nästa steg
* Se [översikt över Log Analytics-agenten](../azure-monitor/platform/log-analytics-agent.md) samla in data från datorer i ditt datacenter eller andra moln.
* Om du vill konfigurera datainsamling från virtuella Azure-datorer läser du [Samla in data om Azure Virtual Machines](log-analytics-quick-collect-azurevm.md).  
* [Lägg till Log Analytics-lösningar från lösningsgalleriet](../azure-monitor/insights/solutions.md) för att lägga till funktioner och samla in data.

