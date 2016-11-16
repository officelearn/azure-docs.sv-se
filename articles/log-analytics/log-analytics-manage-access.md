---
title: "Hantera åtkomst till Log Analytics | Microsoft Docs"
description: "Hantera åtkomst till Log Analytics med hjälp av olika administrativa uppgifter för användare, konton, OMS-arbetsytor och Azure-konton."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/02/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fbd35f9fdd5165a2dd4bc7dd47bd70c890539e38


---
# <a name="manage-access-to-log-analytics"></a>Hantera åtkomst till Log Analytics
För att hantera åtkomst till Log Analytics utför du olika administrativa åtgärder för användare, konton, OMS-arbetsytor och Azure-konton. Om du vill skapa en arbetsyta i Operations Management Suite (OMS) väljer du ett namn på arbetsytan, associerar det med ditt konto och du väljer en geografisk plats. En arbetsyta är i grunden en behållare som innehåller kontoinformation och enkel konfigurationsinformation för kontot. Du eller andra medlemmar i din organisation kan använda flera OMS-arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

Artikeln [Kom igång med Log Analytics](log-analytics-get-started.md) visar hur du snabbt kan komma igång. Resten av den här artikeln beskriver vanliga åtgätder:

* Bestämma antalet arbetsytor du behöver
* Hantera konton och användare
* Lägga till en grupp till en befintlig arbetsyta
* Länka en befintlig arbetsyta till en Azure-prenumeration
* Uppgradera en arbetsyta till en betald dataplan
* Ändra en dataplanstyp
* Lägga till en Azure Active Directory-organisation till en befintlig arbetsyta
* Ta bort din OMS-arbetsyta

## <a name="determine-the-number-of-workspaces-you-need"></a>Bestämma antalet arbetsytor du behöver
En arbetsyta är en Azure-resurs och är en behållare där data samlas in, aggregeras, analyseras och presenteras i OMS-portalen.

Det är möjligt att skapa flera arbetsytor för OMS Log Analytics och för användare att ha tillgång till en eller flera arbetsytor. Genom att minimera antalet arbetsytor kan du förfråga och kombinera över de flesta data. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

Idag är innehåller en Log Analytics-arbetsyta:

* En geografisk plats för lagring av data
* Precision för fakturering
* Dataisolering

Baserat på ovanstående egenskaper kan du behöva skapa flera arbetsytor om:

* Du är ett globalt företag och data behöver lagras i vissa områden för datasuveränitet eller kompatibilitetsskäl.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en Log Analytics-arbetsyta i samma region som de Azure-resurser den hanterar.
* Du vill fördela tillägg till olika avdelningar eller affärsgrupper baserat på deras användning. När du skapar en arbetsyta för varje avdelning eller affärsgrupp, visar Azure-fakturan och användningsutdraget avgifterna för varje arbetsyta separat.
* Du är en leverantör av hanterade tjänster och behöver logganalysdata för varje kund du hanterar isolerade från andra kunders data.
* Du hanterar flera kunder och vill att varje kund/avdelning/affärsgrupp ska se sina egna data, men inte data för några andra.

När du använder agenter för att samla in data kan du konfigurera att varje agent rapporterar till krävd arbetsyta.

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Du kan installera Microsoft Monitoring Agent på datorer som hanteras av Operations Manager och låta agenten rapporten till både Operations Manager och en annan Log Analytics-arbetsyta.

### <a name="workspace-information"></a>Arbetsyteinformation
I OMS-portalen kan du visa informationen om arbetsytan och välja om du vill få information från Microsoft.

#### <a name="view-workspace-information"></a>Visa arbetsyteinformation
1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton**.
3. Klicka på fliken **Arbetsyteinformation**.  
   ![Arbetsyteinformation](./media/log-analytics-manage-access/workspace-information.png)

## <a name="manage-accounts-and-users"></a>Hantera konton och användare
Varje arbetsyta kan ha flera användarkonton kopplade till sig, och varje användarkonto (Microsoft-konto eller organisationskonto) kan ha åtkomst till flera OMS-arbetsytor.

Som standard blir det Microsoft-konto eller organisationskonto som använts för att skapa arbetsytan administratör för arbetsytan. Administratören kan sedan bjuda in ytterligare Microsoft-konton eller välja användare från Azure Active Directory.

Åtkomst till OMS-arbetsytan kontrolleras via två platser:

* I Azure kan du använda rollbaserad åtkomstkontroll för att ge åtkomst till Azure-prenumerationen och tillhörande Azure-resurser. Dessa tillstånd används också för PowerShell- och REST API-åtkomst.
* Endast åtkomst till OMS-portalen, inte tillhörande Azure-prenumeration.

Användarna ser inte data i lösningspanelerna säkerhetskopiering och återställning om du endast ger dem åtkomst till OMS-portalen men inte den Azure-prenumeration som den är länkad till.
Om du vill tillåta alla användare att se data i dessa lösningar, se till att de har minst **läs**-åtkomst för Backup Vault och det Site Recovery-valv som är länkat till OMS-arbetsytan.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Hantera åtkomst till Log Analytics i Azure-portalen
Om du ger användare åtkomst till arbetsytan Log Analytics med Azure-behörigheter, i Azure-portalen t.ex., kan samma användare sedan använda Log Analytics-portalen. Om användare är i Azure-portalen kan de navigera till OMS-portalen genom att klicka på åtgärden **OMS-portal** när de ser Log Analytics-arbetsytan.

Vissa saker att tänka på vad gäller Azure-portalen:

* Detta är inte *Rollbaserad åtkomstkontroll*. Om du har *Läs*-åtkomstbehörigheter i Azure-portalen för Log Analytics-arbetsytan kan du göra ändringar i OMS-portalen. OMS-portalen har ett koncept med administratör, deltagare och skrivskyddad användare. Om det konto du är inloggat med är i det Azure Active Directory som är länkat till arbetsytan är du en administratör i OMS-portalen, annars är du deltagare.
* När du loggar in på OMS-portalen med hjälp av http://mms.microsoft.com, visas som standard listan **Välj en arbetsyta**. Den innehåller endast arbetsytor som har lagts till med hjälp av OMS-portalen. Om du vill se arbetsytorna du har åtkomst till med Azure-prenumerationer måste du ange en klient som en del av URL:en. Exempel:
  
  `mms.microsoft.com/?tenant=contoso.com` Klient-ID är ofta den sista delen av e-postadressen som du loggade in med.
* Om det konto som du loggar in med är ett konto i klient-Azure Active Directory är du en *Administratör* i OMS-portalen. Detta är ofta vanligt om du inte loggar in som en kryptografiprovider.  Om ditt konto inte är i klient-Azure Active Directory så är du en *Användare* i OMS-portalen.
* Om du vill gå direkt till en portal som du har åtkomst till genom att använda Azure-behörigheter måste du ange resursen som en del av URL:en. Det är möjligt att hämta den här URL:en med hjälp av PowerShell.
  
  Till exempel `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.
  
  URL-adressen liknar: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Hantera användare i OMS-portalen
Du kan hantera användare och grupp på fliken **Hantera användare** under fliken **Konton** på sidan Inställningar.   

![hantera användare](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>Lägga till en användare till en befintlig arbetsyta
Använd följande steg för att lägga till en användare eller grupp i en OMS-arbetsyta.

1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Hantera användare**.
3. I avsnittet **Hantera användare** väljer du kontotyp för att lägga till: **Organisationskonto**, **Microsoft-konto**, **Microsoft Support**.
   
   * Om du väljer Microsoft-konto, skriver du e-postadressen för den användare som är associerad med Microsoft-kontot.
   * Om du väljer organisationskonto kan du ange en del av användarens eller gruppens namn eller e-postalias så visas en lista över matchande användare och grupper i en listruta. Välj en användare eller grupp.
   * Använd Microsoft Support för att ge en Microsoft-supporttekniker eller annan Microsoft-medarbetare tillfällig åtkomst till ditt arbetsområde för hjälp med felsökning.
     
     > [!NOTE]
     > Begränsa av prestandaskäl antalet Active Directory-grupper som är kopplade till ett enda OMS-konto till tre – en för administratörer, en för deltagare och en för skrivskyddade användare. Att använda flera grupper kan påverka prestandan hos Log Analytics.
     > 
     > 
4. Välj typ av användare eller grupp att lägga till: **Administratör**, **Deltagare** eller **Skrivskyddad användare**.  
5. Klicka på **Lägg till**.
   
   Om du lägger till ett Microsoft-konto skickas en inbjudan att ansluta till arbetsytan till den e-postadress som du angav. Efter att användaren följt instruktionerna i inbjudan till att ansluta till OMS får användaren åtkomst till den här OMS-arbetsytan.
   Om du lägger till ett organisationskonto kan användaren komma åt Log Analytics omedelbart.  
   ![e-postinbjudan](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

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
1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Hantera användare**.
3. Välj rollen för den användare som du vill ändra.
4. I bekräftelsedialogrutan klickar du **Ja**.

### <a name="remove-a-user-from-an-oms-workspace"></a>Ta bort en användare från en OMS-arbetsyta
Använd följande steg för att ta bort en användare från en OMS-arbetsyta. Borttagning av användaren stänger inte arbetsytan. I stället tas associationen mellan användaren och arbetsytan bort. Om en användare är associerad med flera arbetsytor kommer denna användare fortfarande att kunna logga in på OMS och se sina andra arbetsytor.

1. I OMS, klickar du på ikonen **Inställningar**.
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
Alla arbetsytor som skapats efter den 26 september 2016 måste kopplas till en Azure-prenumeration vid tidpunkten för skapandet. Arbetsytor som skapats innan detta datum måste kopplas till en arbetsyta nästa gång du loggar in. 

> [!IMPORTANT]
> För att länka en arbetsyta måste Azure-kontot redan ha åtkomst till arbetsytan som du vill länka.  Med andra ord måste kontot du använder för att få åtkomst till Azure-portalen vara **samma** som det konto som används för att komma åt OMS-arbetsytan. Om det inte är det hittar du mer information i [Lägga till en användare till en befintlig arbetsyta](#add-a-user-to-an-existing-workspace).
> 
> 

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Om du vill länka en arbetsyta till en Azure-prenumeration i OMS-portalen
För att länka en arbetsyta till en Azure-prenumeration i OMS-portalen måste den inloggade användaren redan ha ett betalt Azure-konto.

1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Azure-prenumeration och dataplan**.
3. Klicka på den plan som du vill använda.
4. Klicka på **Spara**.  
   ![prenumeration och dataplaner](./media/log-analytics-manage-access/subscription-tab.png)

Den nya dataplanen visas på menyfliken i OMS-portalen längst upp på webbsidan.

![OMS-menyflikar](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Om du vill länka en arbetsyta till en Azure-prenumeration i Azure-portalen
1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Bläddra efter **Log Analytics (OMS)** och markerar den.
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
8. Klicka på **Skapa**. Arbetsytan är nu länkad till ditt Azure-konto.

> [!NOTE]
> Om du inte ser arbetsytan som du vill länka så saknar din Azure-prenumeration åtkomst till OMS-arbetsytan som du skapade med OMS-webbplatsen.  Du måste bevilja åtkomst till det här kontot via OMS-portalen. Du hittar information om att göra detta i [Lägga till en användare till en befintlig arbetsyta](#add-a-user-to-an-existing-workspace).
> 
> 

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Uppgradera en arbetsyta till en betald plan
Det finns tre typer av planer för arbetsytor i OMS: **Kostnadsfri**, **Fristående** och **OMS**.  Om du har planen *Kostnadsfri* finns det en gräns på 500 MB data per dag som kan skickas till Log Analytics.  Om du överskrider denna mängd måste du ändra din arbetsyta till en betald plan för att undvika att data inte samlas in utöver denna gräns. Du kan ändra din plantyp när som helst.  Mer information om OMS-priser finns i [Prisinformation](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Använda rättigheter från en OMS-prenumeration
För att använda rättigheter som kommer från inköp av OMS E1, OMS E2 OMS eller MS-tillägg för System Center väljer du *OMS*-planen för OMS Log Analytics.

När du köper en OMS-prenumeration läggs rättigheterna till i ditt Enterprise Agreement. Alla Azure-prenumerationer som har skapats under det här avtalet kan använda rättigheterna. Detta gör att du till exempel kan ha flera OMS-arbetsytor som använder rättigheten från OMS-prenumerationerna.

För att säkerställa att användningen av en OMS-arbetsyta tillämpas på dina rättigheter från OMS-prenumerationen måste du:

1. Skapa din OMS-arbetsyta i en Azure-prenumeration som är en del av ett Enterprise Agreement som inkluderar OMS-prenumerationen
2. Välja planen *OMS* för arbetsytan

> [!NOTE]
> Om din arbetsyta skapades innan 26 september 2016 och din Log Analytics-prisplan är *Premium* kommer den här arbetsytan att använda rättigheter från OMS tillägget för System Center. Du kan också använda dina rättigheter genom att ändra till prisnivån *OMS*. 
> 
> 

OMS-prenumerationens rättigheter är inte synliga i Azure eller OMS-portalen. Du kan se rättigheter och användning i Enterprise Portal.  

Om du behöver ändra Azure-prenumerationen som OMS-arbetsytan är länkad till kan du använda cmdlet:en för Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Använda Azure-åtagande från ett Enterprise-avtal
Om du inte har en OMS-prenumeration betalar du separat för varje komponent i OMS och användningen visas på din Azure-faktura.

Om du har ett Azure-betalningsåtagande på företagsregistreringen som är kopplad till dina Azure-prenumerationer, kommer all användning av Log Analytics automatiskt att debiteras mot eventuella återstående betalningsåtaganden.

Om du behöver ändra den Azure-prenumeration som OMS-arbetsytan är länkad till kan du använda cmdlet:en för Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).  

### <a name="change-a-workspace-to-a-paid-data-plan"></a>Ändra en arbetsyta till en betald dataplan
1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Bläddra efter **Log Analytics** och markera den.
3. Du ser listan över befintliga arbetsytor. Välj en arbetsyta.  
   ![lista över arbetsytor](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4. Under **Inställningar**, klicka på **Prisnivå**.  
   ![prisnivå](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5. Under **Prisnivå**, välj en dataplan och klicka på **Välj**.  
   ![välj plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. När du har uppdaterat vyn i Azure-portalen visas **Prisnivå** uppdaterad med den plan som du har valt.  
   ![uppdatera prisnivå](./media/log-analytics-manage-access/manage-access-change-plan04.png)

## <a name="add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Lägga till en Azure Active Directory-organisation till en befintlig arbetsyta
Du kan associera arbetsytan för Log Analytics med en Azure Active Directory-domän och lägga till användare från din katalog i OMS-arbetsytan.

När du skapar arbetsytan från Azure-portalen eller länkar arbetsytan till en Azure-prenumeration länkas Azure Active Directory som ditt organisationskonto.

När du skapar arbetsytan från OMS-portalen uppmanas du att länka till en Azure-prenumeration och ett organisationskonto för den Azure-prenumeration som är kopplad till Log Analytics-arbetsytan.

### <a name="to-add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Så här lägger du till en Azure Active Directory-organisation till en befintlig arbetsyta
Genom att lägga till en Azure Active Directory-organisation kan du lägga till användare och grupper från katalogen till arbetsytan.

1. På sidan Inställningar i OMS klickar du på **Konton** och klickar sedan på fliken **Hantera användare**.  
2. Granska informationen om organisationskonton och klicka sedan på **Lägga till organisation**.  
    ![lägga till organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Ange ID-information för en administratör i Azure Active Directory-domänen. Därefter visas en bekräftelse om att din arbetsyta är kopplad till Azure Active Directory-domänen.
    ![bekräftelse av länkad arbetsyta](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

Om du behöver lägga till användare från en annan katalog klickar du på knappen *Ändra organisation* för att koppla arbetsytan till en annan katalog.

## <a name="delete-your-log-analytics-workspace"></a>Ta bort din Log Analytics-arbetsyta
När du tar bort en Log Analytics-arbetsyta raderas alla data som är relaterade till arbetsytan från OMS-tjänsten inom 30 dagar.

Om du är administratör och det finns flera användare som är kopplade till arbetsytan bryts kopplingen mellan användare och arbetsytan. Om användarna är associerade med andra arbetsytor, kan de fortsätta använda OMS med de andra arbetsytorna. Om de inte är associerade med andra arbetsytor måste de dock skapa en arbetsyta för att använda OMS.

### <a name="to-delete-an-oms-workspace"></a>Ta bort en OMS-arbetsyta
1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Arbetsyteinformation**.
3. Klicka på **Stäng arbetsytan**.
4. Välj en av följande orsaker för att stänga ditt arbetsområde eller ange en annan orsak i textrutan.
5. Klicka på **Stäng arbetsytan**.

## <a name="next-steps"></a>Nästa steg
* Se [Ansluta Windows-datorer till Log Analytics](log-analytics-windows-agents.md) för att lägga till agenter och samla in data.
* [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.
* [Konfigurera inställningar för proxyserver och brandvägg i Log Analytics](log-analytics-proxy-firewall.md) om din organisation använder en proxyserver eller brandvägg så att agenterna kan kommunicera med Log Analytics-tjänsten.




<!--HONumber=Nov16_HO2-->


