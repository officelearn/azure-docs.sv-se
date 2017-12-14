---
title: Hantera arbetsytor i Azure Log Analytics | Microsoft-dokument
description: "Du kan hantera arbetsytor i Azure Log Analytics med hjälp av olika administrativa uppgifter för användare, konton, arbetsytor och Azure-konton."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2017
ms.author: magoedte
ms.openlocfilehash: 1549408c6885ee556a142ab7de613ebb1629070d
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="manage-workspaces"></a>Hantera arbetsytor

Du hanterar åtkomsten till Log Analytics genom att utföra olika administrativa uppgifter relaterade till arbetsytor. Den här artikeln innehåller riktlinjer och procedurer för att hantera arbetsytor. En arbetsyta är i grunden en behållare som innehåller kontoinformation och enkel konfigurationsinformation för kontot. Du eller andra medlemmar i din organisation kan använda flera arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

För att skapa en arbetsyta måste du:

1. Ha en Azure-prenumeration.
2. Välja ett arbetsytenamn.
3. Associera arbetsytan med din prenumeration.
4. Välja en geografisk plats.

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestämma antalet arbetsytor du behöver
En arbetsyta är en Azure-resurs och en behållare där data samlas in, aggregeras, analyseras och presenteras på Azure Portal.

Du kan ha flera arbetsytor per Azure-prenumeration och du kan ha åtkomst till fler än en arbetsyta. När du minimerar antalet arbetsytor kan du söka och samordna de flesta data eftersom det inte går att söka i flera arbetsytor. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

För närvarande tillhandahåller en arbetsyta:

* En geografisk plats för lagring av data
* Precision för fakturering
* Dataisolering
* Omfång för konfiguration

Baserat på föregående egenskaper kan du behöva skapa flera arbetsytor om:

* Du är ett globalt företag och data behöver lagras i vissa områden för datasuveränitet eller kompatibilitetsskäl.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du vill fördela tillägg till olika avdelningar eller affärsgrupper baserat på deras användning. När du skapar en arbetsyta för varje avdelning eller affärsgrupp, visar Azure-fakturan och användningsutdraget avgifterna för varje arbetsyta separat.
* Du är en leverantör av hanterade tjänster och behöver se till att logganalysdata för varje kund du hanterar är isolerade från andra kunders data.
* Du hanterar flera kunder och vill att varje kund/avdelning/affärsgrupp ska se sina egna data, men inte data för några andra.

När du använder agenter för att samla in data måste du [konfigurera varje agent så att den rapporterar till en eller flera arbetsytor](log-analytics-windows-agent.md).

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Microsoft Monitoring Agent på datorn kan dock konfigureras att rapportera till både Operations Manager och en annan Log Analytics-arbetsyta.  

### <a name="workspace-information"></a>Arbetsyteinformation

Du kan visa information om din arbetsyta på Azure Portal. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Visa information om arbetsytan på Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på knappen **Fler tjänster** längst upp till vänster i Azure-portalen.  I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Log Analytics**.  
    ![Azure-hubb](./media/log-analytics-manage-access/hub.png)  
3. Välj en arbetsyta på Log Analytics-prenumerationsbladet.
4. Bladet för arbetsytan visar information om arbetsytan och länkar till ytterligare information.  
    ![information om arbetsytan](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Hantera konton och användare
Varje arbetsyta kan ha flera associerade konton, och varje konto (Microsoft-konto eller organisationskonto) kan ha åtkomst till flera arbetsytor.

Som standard blir det Microsoft-konto eller organisationskonto som använts för att skapa arbetsytan administratör för arbetsytan.

Det finns två behörighetsmodeller som kontrollerar åtkomst till en Log Analytics-arbetsyta:

1. Äldre Log Analytics-användarroller
2. [Rollbaserad åtkomst i Azure](../active-directory/role-based-access-control-configure.md)

I följande tabell sammanfattas åtkomsten som kan ställas in med varje behörighetsmodell:

|                          | Log Analytics-portal | Azure Portal | API (inklusive PowerShell) |
|--------------------------|----------------------|--------------|----------------------------|
| Log Analytics-användarroller | Ja                  | Nej           | Nej                         |
| Rollbaserad åtkomst i Azure  | Ja                  | Ja          | Ja                        |

> [!NOTE]
> Log Analytics flyttar för att använda Azure-rollbaserad åtkomst som behörighetsmodell, vilket ersätter Log Analytics-användarrollerna.
>
>

De äldre Log Analytics-användarrollerna kontrollerar endast åtkomst till aktiviteter som utförs i [Log Analytics-portalen](https://mms.microsoft.com).

Följande aktiviteter kräver även Azure-behörigheter:

| Åtgärd                                                          | Azure-behörigheter krävs | Anteckningar |
|-----------------------------------------------------------------|--------------------------|-------|
| Lägga till och ta bort hanteringslösningar                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
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

| Typ    | Behörighet | Beskrivning |
| ------- | ---------- | ----------- |
| Åtgärd | `*/read`   | Kan visa alla resurser och resurskonfigurationer. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure-diagnostik för resurser <br> Alla egenskaper och inställningar för alla resurser |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Kan utföra Log Search v2-sökfrågor |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/search/action` | Kan utföra Log Search v1-sökfrågor |
| Åtgärd | `Microsoft.Support/*` | Kan öppna supportärenden |
|Ingen åtgärd | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Förhindrar läsning av arbetsytenyckeln som krävs för att använda datasamlings-API:et och installera agenter |


Medlemmar av *Log Analytics Contributor*-rollen kan:
- Läsa alla övervakningsdata 
- Skapa och konfigurera Automation -konton
- Lägga till och ta bort hanteringslösningar
- Läsa lagringskontonycklar 
- Konfigurera loggsamlingar från Azure Storage
- Redigera övervakningsinställningar för Azure-resurser, bland annat
  - Lägga till tillägg för virtuell dator i virtuella datorer
  - Konfigurera Azure-diagnostik på alla Azure-resurser

> [!NOTE] 
> Du kan använda möjligheten att lägga till ett virtuellt datortillägg i en virtuell dator för att få fullständig kontroll över datorn.

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

Använd [anpassade roller](../active-directory/role-based-access-control-custom-roles.md) för att skapa roller med specifik behörighet.

### <a name="azure-user-roles-and-log-analytics-portal-user-roles"></a>Azure-användarroller och Log Analytics Portal-användarroller
Om du har minst Azure-läsbehörighet i Log Analytics-arbetsytan kan du öppna OMS-portalen genom att klicka på uppgiften **OMS-portal** när du visar Log Analytics-arbetsytan.

När du öppnar OMS-portalen växlar du till de äldre Log Analytics-användarrollerna. Om du inte har någon rolltilldelning i Log Analytics-portalen [kontrollerar tjänsten vilka Azure-behörigheter du har på arbetsytan](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
Din rolltilldelning i OMS-portalen fastställs så här:

| Villkor                                                   | Tilldelad Log Analytics-användarroll | Anteckningar |
|--------------------------------------------------------------|----------------------------------|-------|
| Ditt konto tillhör en tidigare Log Analytics-användarroll     | Den angivna Log Analytics-användarrollen | |
| Ditt konto tillhör inte en tidigare Log Analytics-användarroll <br> Fullständiga Azure-behörigheter för arbetsytan (`*` behörighet <sup>1</sup>) | Administratör ||
| Ditt konto tillhör inte en tidigare Log Analytics-användarroll <br> Fullständiga Azure-behörigheter för arbetsytan (`*` behörighet <sup>1</sup>) <br> *inte åtgärder* för `Microsoft.Authorization/*/Delete` och `Microsoft.Authorization/*/Write` | Deltagare ||
| Ditt konto tillhör inte en tidigare Log Analytics-användarroll <br> Läsbehörighet för Azure | Skrivskyddad ||
| Ditt konto tillhör inte en tidigare Log Analytics-användarroll <br> Azure-behörigheter kan inte tolkas | Skrivskyddad ||
| För CSP-hanterade (Cloud Solution Provider) prenumerationer <br> Kontot du är inloggad med är i Azure Active Directory, länkat till arbetsytan | Administratör | Vanligtvis en CSP-kund |
| För CSP-hanterade (Cloud Solution Provider) prenumerationer <br> Kontot du är inloggad med är inte i Azure Active Directory, länkat till arbetsytan | Deltagare | Vanligtvis CSP |

<sup>1</sup> Läs [Azure permissions](../active-directory/role-based-access-control-custom-roles.md) (Azure-behörigheter) om du vill ha mer information om rolldefinitioner. När du utvärderar roller motsvarar åtgärden `*` inte `Microsoft.OperationalInsights/workspaces/*`.

Vissa saker att tänka på vad gäller Azure-portalen:

* När du loggar in på OMS-portalen med hjälp av http://mms.microsoft.com, visas listan **Välj en arbetsyta**. Listan innehåller endast arbetsytor där du har en Log Analytics-användarroll. Om du vill se arbetsytorna du har åtkomst till med Azure-prenumerationer måste du ange en klient som en del av URL:en. Till exempel: `mms.microsoft.com/?tenant=contoso.com`. Klient-ID är ofta den sista delen av e-postadressen som du loggade in med.
* Om du vill gå direkt till en portal som du har åtkomst till genom att använda Azure-behörigheter måste du ange resursen som en del av URL:en. Det är möjligt att hämta den här URL:en med hjälp av PowerShell.

  Till exempel `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  URL-adressen liknar: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Hantera användare i OMS-portalen
Du kan hantera användare och grupp på fliken **Hantera användare** under fliken **Konton** på sidan Inställningar.   

![hantera användare](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Lägga till en användare till en befintlig arbetsyta
Följ stegen nedan om du vill lägga till en användare eller grupp på en arbetsyta.

1. Klicka på panelen **Inställningar** på OMS-portalen.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Hantera användare**.
3. I avsnittet **Hantera användare** väljer du kontotyp för att lägga till: **Organisationskonto**, **Microsoft-konto**, **Microsoft Support**.

   * Om du väljer Microsoft-konto, skriver du e-postadressen för den användare som är associerad med Microsoft-kontot.
   * Om du väljer organisationskonto kan du ange en del av användarens/gruppens namn eller e-postalias så visas en lista över matchande användare och grupper i en listruta. Välj en användare eller grupp.
   * Använd Microsoft Support för att ge en Microsoft-supporttekniker eller annan Microsoft-medarbetare tillfällig åtkomst till ditt arbetsområde för hjälp med felsökning.

     > [!NOTE]
     > Begränsa av prestandaskäl antalet Active Directory-grupper som är kopplade till ett enda OMS-konto till tre – en för administratörer, en för deltagare och en för skrivskyddade användare. Att använda flera grupper kan påverka prestandan hos Log Analytics.
     >
     >
4. Välj typ av användare eller grupp att lägga till: **Administratör**, **Deltagare** eller **Skrivskyddad användare**.  
5. Klicka på **Lägg till**.

   Om du lägger till ett Microsoft-konto skickas en inbjudan att ansluta till arbetsytan till den e-postadress som du angav. När användaren har följt instruktionerna i inbjudan för att ansluta till OMS får han eller hon åtkomst till arbetsytan.
   Om du lägger till ett organisationskonto kan användaren komma åt Log Analytics omedelbart.  

#### <a name="edit-an-existing-user-type"></a>Redigera en befintlig användartyp
Du kan ändra kontorollen för en användare som är associerad med OMS-kontot. Du har följande rollalternativ:

* *Administratör*: Kan hantera användare, visa och arbeta med alla aviseringar och lägga till och ta bort servrar
* *Deltagare*: Kan visa och vidta åtgärder för alla aviseringar och lägga till och ta bort servrar
* *Skrivskyddad användare*: Användare som har markerats som skrivskyddade kan inte:

  1. Lägg till/ta bort lösningar. Lösningsgalleriet är dolt.
  2. Lägg till/ändra/ta bort ikoner på **Min instrumentpanel**.
  3. Visa sidorna **Inställningar**. Sidorna är dolda.
  4. I sökvyn är aktiviteterna, PowerBI-konfiguration, sparade sökningar och aviseringar dolda.

#### <a name="to-edit-an-account"></a>Så här redigerar du ett konto
1. Klicka på panelen **Inställningar** på OMS-portalen.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Hantera användare**.
3. Välj rollen för den användare som du vill ändra.
4. I bekräftelsedialogrutan klickar du **Ja**.

### <a name="remove-a-user-from-a-workspace"></a>Ta bort en användare från en arbetsyta
Följ stegen nedan om du vill ta bort en användare från en arbetsyta. Borttagning av användaren stänger inte arbetsytan. I stället tas associationen mellan användaren och arbetsytan bort. Om en användare är associerad med flera arbetsytor kommer denna användare fortfarande att kunna logga in på OMS och se sina andra arbetsytor.

1. Klicka på panelen **Inställningar** på OMS-portalen.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Hantera användare**.
3. Klicka på **Ta bort** bredvid det användarnamn som du vill ta bort.
4. I bekräftelsedialogrutan klickar du **Ja**.

### <a name="add-a-group-to-an-existing-workspace"></a>Lägga till en grupp till en befintlig arbetsyta
1. Följ steg 1 -4 i "Om du vill lägga till en användare till en befintlig arbetsyta" ovan.
2. Under **Välj användare/grupp**, väljer du **Grupp**.  
   ![lägga till en grupp till en befintlig arbetsyta](./media/log-analytics-manage-access/add-group.png)
3. Ange ett visningsnamn eller en e-postadress för gruppen som du vill lägga till.
4. Markera gruppen i listresultaten och klicka sedan på **Lägg till**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Länka en befintlig arbetsyta till en Azure-prenumeration
Alla arbetsytor som skapats efter den 26 september 2016 måste kopplas till en Azure-prenumeration vid tidpunkten för skapandet. Arbetsytor som skapats innan det här datumet måste kopplas till en arbetsyta när du loggar in. När du skapar arbetsytan från Azure Portal eller när du länkar arbetsytan till en Azure-prenumeration länkas din Azure Active Directory som ditt organisationskonto.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Om du vill länka en arbetsyta till en Azure-prenumeration i OMS-portalen

- När du loggar in till OMS-portalen uppmanas du att välja en Azure-prenumeration. Välj den prenumeration som du vill länka till din arbetsyta och klicka sedan på **Länka**.  
    ![länka Azure-prenumeration](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > För att länka en arbetsyta måste Azure-kontot redan ha åtkomst till arbetsytan som du vill länka.  Med andra ord måste kontot som du använder för att få åtkomst till Azure Portal vara **samma** som det konto som används för att komma åt arbetsytan. Om det inte är det hittar du mer information i [Lägga till en användare till en befintlig arbetsyta](#add-a-user-to-an-existing-workspace).

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

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Ändra en organisations Azure Active Directory för en arbetsyta

Du kan ändra en arbetsytas Azure Active Directory-organisation. Genom att ändra Azure Active Directory-organisationen kan du lägga till användare och grupper från katalogen till arbetsytan.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Så här ändrar du en organisations Azure Active Directory för en arbetsyta

1. På sidan Inställningar på OMS-portalen klickar du på **Konton** och sedan på fliken **Hantera användare**.  
2. Granska informationen om organisationskonton och klicka sedan på **Ändra organisation**.  
    ![ändra organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Ange ID-information för en administratör i Azure Active Directory-domänen. Därefter visas en bekräftelse om att din arbetsyta är kopplad till Azure Active Directory-domänen.  
    ![bekräftelse av länkad arbetsyta](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

## <a name="next-steps"></a>Nästa steg
* Se [Förstå dataanvändning](log-analytics-usage.md) om du vill lära dig hur du analyserar datamängden som samlas in av lösningar och skickas från datorer.
* [Lägg till Log Analytics-hanteringslösningar från Azure Marketplace](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.