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
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3b4e0f978cc7d23d0157b78fd2dff27096d2768b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133019"
---
# <a name="manage-workspaces"></a>Hantera arbetsytor

Du hanterar åtkomsten till Log Analytics genom att utföra olika administrativa uppgifter relaterade till arbetsytor. Den här artikeln innehåller riktlinjer och procedurer för att hantera arbetsytor. En arbetsyta är i grunden en behållare som innehåller kontoinformation och enkel konfigurationsinformation för kontot. Du eller andra medlemmar i din organisation kan använda flera arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

För att skapa en arbetsyta måste du:

1. Ha en Azure-prenumeration.
2. Välja ett arbetsytenamn.
3. Koppla på arbetsytan med en av dina prenumerationer och resursgrupper.
4. Välja en geografisk plats.

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestämma antalet arbetsytor du behöver
En arbetsyta är en Azure-resurs och en behållare där data samlas in, aggregeras, analyseras och presenteras på Azure Portal.

Du kan ha flera arbetsytor per Azure-prenumeration och du kan ha åtkomst till fler än en arbetsyta, med möjlighet att snabbt göra förfrågningar i alla. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

För närvarande tillhandahåller en arbetsyta:

* En geografisk plats för lagring av data
* Isolering av data att definiera olika användarbehörigheter
* Omfång för att konfigurera inställningar för kvarhållning och data tak som skall

Från förbrukning synsätt rekommenderar vi för att skapa arbetsytor så lite som möjligt. Det gör administrationen och fråga upplever enklare och snabbare. Men på grund av föregående egenskaper, du kanske vill skapa flera arbetsytor om:

* Du är ett globalt företag och data behöver lagras i vissa områden för datasuveränitet eller kompatibilitetsskäl.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du vill fördela tillägg till olika avdelningar eller affärsgrupper baserat på deras användning. När du skapar en arbetsyta för varje avdelning eller affärsgrupp i sin egen Azure-prenumeration.
* Du är leverantör av hanterade tjänster och behöver Log Analytics-data för varje kund du hanterar isolerade från andra kunders data.
* Du hanterar flera kunder och vill att varje kund/avdelning/affärsgrupp ska se sina egna data, men inte data för några andra.

När du använder Windows-agenter för att samla in data måste du [konfigurera varje agent så att den rapporterar till en eller flera arbetsytor](log-analytics-windows-agents.md).

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Du kan installera Microsoft Monitoring Agent på datorer som hanteras av Operations Manager och låta agenten rapporten till både Operations Manager och en annan Log Analytics-arbetsyta.

### <a name="workspace-information"></a>Arbetsyteinformation

Du kan visa information om din arbetsyta på Azure Portal. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Visa information om arbetsytan på Azure Portal

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com) med din Azure-prenumeration.
2. På **navmenyn** klickar du på **Fler tjänster** och skriver **Log Analytics** i listan med resurser. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Log Analytics**.  
    ![Azure-hubb](./media/log-analytics-manage-access/hub.png)  
3. Välj en arbetsyta på Log Analytics-prenumerationsbladet.
4. Bladet för arbetsytan visar information om arbetsytan och länkar till ytterligare information.  
    ![information om arbetsytan](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Hantera konton och användare
Varje arbetsyta kan ha flera konton som är kopplade till den och varje konto kan ha åtkomst till flera arbetsytor. Åtkomst hanteras via [Azure rollbaserad åtkomst](../active-directory/role-based-access-control-configure.md). Dessa behörigheter gäller både på Azure portal och API-åtkomst.


Följande aktiviteter kräver även Azure-behörigheter:

| Åtgärd                                                          | Azure-behörigheter krävs | Anteckningar |
|-----------------------------------------------------------------|--------------------------|-------|
| Lägga till och ta bort hanteringslösningar                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Behörigheterna måste beviljas på resursgrupp- eller prenumerationsnivå. |
| Ändra prisnivån                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visa data i lösningspanelerna *säkerhetskopiering* och *Site Recovery* | Administratör/medadministratör | Åtkomst till resurser som distribueras med den klassiska distributionsmodellen |
| Skapa en arbetsyta i Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Hantera åtkomst till Log Analytics med Azure-behörighet
Om du vill bevilja åtkomst till Log Analytics-arbetsytan med Azure-behörigheter följer du stegen i [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../active-directory/role-based-access-control-configure.md).

Azure har två inbyggda användarroller för Log Analytics:
- Log Analytics Reader
- Log Analytics Contributor

Medlemmar av *Log Analytics Reader*-rollen kan:
- Visa och söka i alla övervakningsdata 
- Visa övervakningsinställningar, även konfiguration av Azure-diagnostik för alla Azure-resurser.

Rollen Analytics Händelseloggläsare innehåller följande Azure åtgärder:

| Typ    | Behörighet | Beskrivning |
| ------- | ---------- | ----------- |
| Åtgärd | `*/read`   | Möjligheten att visa alla Azure-resurser och resurskonfigurationen. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure-diagnostik för resurser <br> Alla egenskaper och inställningar för alla resurser |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Kan utföra Log Search v2-sökfrågor |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/search/action` | Kan utföra Log Search v1-sökfrågor |
| Åtgärd | `Microsoft.Support/*` | Kan öppna supportärenden |
|Ingen åtgärd | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Förhindrar läsningen av arbetsyta krävs för att använda datainsamling API och installera agenter. Detta förhindrar att användare lägger till nya resurser till arbetsytan |


Medlemmar av *Log Analytics Contributor*-rollen kan:
- Läsa alla övervakningsdata som Analytics Händelseloggläsare  
- Skapa och konfigurera Automation -konton  
- Lägga till och ta bort hanteringslösningar    
    > [!NOTE] 
    > För att kunna utföra de sista två åtgärderna, behöver den här behörigheten beviljas på resursen grupp- eller prenumeration.  

- Läsa lagringskontonycklar   
- Konfigurera loggsamlingar från Azure Storage  
- Redigera övervakningsinställningar för Azure-resurser, bland annat
  - Lägga till tillägg för virtuell dator i virtuella datorer
  - Konfigurera Azure-diagnostik på alla Azure-resurser

> [!NOTE] 
> Du kan använda möjligheten att lägga till ett virtuellt datortillägg i en virtuell dator för att få fullständig kontroll över datorn.

Log Analytics deltagarrollen innehåller följande Azure åtgärder:

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

Vi rekommenderar att du utför tilldelningar på resursnivå (arbetsyta) för att garantera korrekt åtkomstkontroll.  Använd [anpassade roller](../active-directory/role-based-access-control-custom-roles.md) för att skapa roller med specifik behörighet.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Länka en befintlig arbetsyta till en Azure-prenumeration
Alla arbetsytor som skapats efter den 26 september 2016 måste kopplas till en Azure-prenumeration vid tidpunkten för skapandet. Arbetsytor som skapats innan det här datumet måste kopplas till en arbetsyta när du loggar in. När du skapar arbetsytan från Azure Portal eller när du länkar arbetsytan till en Azure-prenumeration länkas din Azure Active Directory som ditt organisationskonto.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Om du vill länka en arbetsyta till en Azure-prenumeration i Azure-portalen
1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Bläddra efter **Log Analytics** och markera den.
3. Du ser listan över befintliga arbetsytor. Klicka på **Lägg till**.  
   ![lista över arbetsytor](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. Under **OMS-arbetsytan**, klicka på **eller länka befintliga**.  
   ![länka befintliga](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Klicka på **Konfigurera nödvändiga inställningar**.  
   ![konfigurera nödvändiga inställningar](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Du ser listan över arbetsytor som ännu inte har länkats till ditt Azure-konto. Välj en arbetsyta.  
   ![välj arbetsytor](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Om det behövs, kan du ändra värdena för följande objekt:
   * Prenumeration
   * Resursgrupp
   * Plats
   * Prisnivå  
     ![ändra värden](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Klicka på **OK**. Arbetsytan är nu länkad till ditt Azure-konto.

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

OMS-prenumerationens rättigheter är inte synliga i Azure eller OMS-portalen. Du kan se rättigheter och användning i Enterprise Portal.  

Om du behöver ändra Azure-prenumerationen som arbetsytan är länkad till kan du använda Azure PowerShell-cmdlet:en [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Använda Azure-åtagande från ett Enterprise-avtal
Om du inte har en OMS-prenumeration betalar du separat för varje komponent i OMS och användningen visas på din Azure-faktura.

Om du har ett Azure-betalningsåtagande på företagsregistreringen som är kopplad till dina Azure-prenumerationer kommer all användning av Log Analytics automatiskt att debiteras mot eventuella återstående betalningsåtaganden.

Om du behöver ändra Azure-prenumerationen som arbetsytan är länkad till kan du använda Azure PowerShell-cmdlet:en [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Ändra en arbetsyta till en prisnivå i Azure Portal
1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Bläddra efter **Log Analytics** och markera den.
3. Du ser listan över befintliga arbetsytor. Välj en arbetsyta.  
4. Klicka på **Prisnivå** under **Allmänt** på bladet för arbetsytan.  
5. Välj en prisnivå under **Prisnivå** och klicka på **Välj**.  
    ![välj plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. När du har uppdaterat vyn i Azure Portal visas **Prisnivå** uppdaterad med den prisnivå som du har valt.  
    ![uppdaterad plan](./media/log-analytics-manage-access/manage-access-change-plan04.png)

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


## <a name="change-how-long-log-analytics-stores-data"></a>Ändra hur länge Log Analytics lagrar data

På den kostnadsfria prisnivån tillgängliggör Log Analytics de senaste sju dagarnas data.
På standardprisnivån tillgängliggör Log Analytics de senaste 30 dagarnas data.
På premiumprisnivån tillgängliggör Log Analytics de senaste 365 dagarnas data.
På den fristående prisnivån och OMS-prisnivån tillgängliggör Log Analytics de senaste 31 dagarnas data.

När du använder den fristående prisnivån och OMS-prisnivån kan du behålla upp till två års data (730 dagar). Data som lagras längre än standardvärdet 31 dagar medför en avgift för datakvarhållning. Mer information om priser finns i [överförbrukningsdebitering](https://azure.microsoft.com/pricing/details/log-analytics/).

Om du vill ändra längden på datakvarhållning läser du [Hantera kostnader genom att kontrollera datavolym och kvarhållning i Log Analytics](log-analytics-manage-cost-storage.md).


## <a name="delete-a-log-analytics-workspace"></a>Ta bort en Log Analytics-arbetsyta
När du tar bort en Log Analytics-arbetsyta raderas alla data som är relaterade till arbetsytan från Log Analytics-tjänsten inom 30 dagar.

Om du är administratör och det finns flera användare som är kopplade till arbetsytan bryts kopplingen mellan användare och arbetsytan. Om användarna är associerade med andra arbetsytor kan de fortsätta använda Log Analytics med de andra arbetsytorna. Om de inte är associerade med andra arbetsytor måste de skapa en arbetsyta för att kunna använda tjänsten. Om du vill ta bort en arbetsyta går du till [Ta bort en Azure Log Analytics-arbetsyta](log-analytics-manage-del-workspace.md)

## <a name="next-steps"></a>Nästa steg
* Om du vill samla in data från datorer i ditt datacenter eller annan molnmiljö går du till [Samla in data från datorer i din miljö med Log Analytics](log-analytics-concept-hybrid.md).
* Om du vill konfigurera datainsamling från virtuella Azure-datorer läser du [Samla in data om Azure Virtual Machines](log-analytics-quick-collect-azurevm.md).  
* [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.

