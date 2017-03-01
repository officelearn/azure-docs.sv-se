---
title: Hantera arbetsytor | Microsoft Docs
description: "Hantera arbetsytor i Azure Log Analytics med hjälp av olika administrativa uppgifter för användare, konton, arbetsytor och Azure-konton."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/17/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 9ee8f4aafcc35e43c4fcba5a3a72b043dd9fc32c
ms.openlocfilehash: 1d330362389ee690dc2942c9bb1bc32e1f10e08a


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

Det är möjligt att skapa flera arbetsytor så att användare har åtkomst till en eller flera arbetsytor. Genom att minimera antalet arbetsytor kan du förfråga och kombinera över de flesta data. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

För närvarande tillhandahåller en arbetsyta:

* En geografisk plats för lagring av data
* Precision för fakturering
* Dataisolering
* Omfång för konfiguration

Baserat på föregående egenskaper kan du behöva skapa flera arbetsytor om:

* Du är ett globalt företag och data behöver lagras i vissa områden för datasuveränitet eller kompatibilitetsskäl.
* Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en arbetsyta i samma region som de Azure-resurser som den hanterar.
* Du vill fördela tillägg till olika avdelningar eller affärsgrupper baserat på deras användning. När du skapar en arbetsyta för varje avdelning eller affärsgrupp, visar Azure-fakturan och användningsutdraget avgifterna för varje arbetsyta separat.
* Du är en leverantör av hanterade tjänster och behöver logganalysdata för varje kund du hanterar isolerade från andra kunders data.
* Du hanterar flera kunder och vill att varje kund/avdelning/affärsgrupp ska se sina egna data, men inte data för några andra.

När du använder agenter för att samla in data måste du konfigurera varje agent så att den rapporterar till en eller flera arbetsytor.

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Du kan installera Microsoft Monitoring Agent på datorer som hanteras av Operations Manager och låta agenten rapporten till både Operations Manager och en annan Log Analytics-arbetsyta.

### <a name="workspace-information"></a>Arbetsyteinformation

Du kan visa information om din arbetsyta på Azure Portal. Du kan också visa information på OMS-portalen.

#### <a name="view-workspace-information-the-azure-portal"></a>Visa information om arbetsytan på Azure Portal

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com) med din Azure-prenumeration.
2. På **navmenyn** klickar du på **Fler tjänster** och skriver **Log Analytics** i listan med resurser. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Log Analytics**.  
    ![Azure-hubb](./media/log-analytics-manage-access/hub.png)  
3. Välj en arbetsyta på Log Analytics-prenumerationsbladet.
4. Bladet för arbetsytan visar information om arbetsytan och länkar till ytterligare information.  
    ![information om arbetsytan](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Hantera konton och användare
Varje arbetsyta kan ha flera associerade användarkonton, och varje användarkonto (Microsoft-konto eller organisationskonto) kan ha åtkomst till flera arbetsytor.

Som standard blir det Microsoft-konto eller organisationskonto som använts för att skapa arbetsytan administratör för arbetsytan. Administratören kan sedan bjuda in ytterligare Microsoft-konton eller välja användare från Azure Active Directory.

Du hanterar användarnas åtkomst till arbetsytan på två platser:

* I Azure kan du använda rollbaserad åtkomstkontroll för att ge åtkomst till Azure-prenumerationen och tillhörande Azure-resurser. Dessa tillstånd används också för PowerShell- och REST API-åtkomst.
* Endast åtkomst till OMS-portalen, inte tillhörande Azure-prenumeration.

Om du vill visa panelerna för Backup och Site Recovery måste du ha administratörs- eller medadministratörsbehörighet till den Azure-prenumeration som arbetsytan är länkad till.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Hantera åtkomst till Log Analytics i Azure-portalen
Om du ger användare åtkomst till arbetsytan Log Analytics med Azure-behörigheter, i Azure-portalen t.ex., kan samma användare sedan använda Log Analytics-portalen. Om användare är i Azure-portalen kan de navigera till OMS-portalen genom att klicka på åtgärden **OMS-portal** när de ser Log Analytics-arbetsytan.

Vissa saker att tänka på vad gäller Azure-portalen:

* Detta är inte *Rollbaserad åtkomstkontroll*. Om du har *Läs*-åtkomstbehörigheter i Azure-portalen för Log Analytics-arbetsytan kan du göra ändringar i OMS-portalen. OMS-portalen har ett koncept med administratör, deltagare och skrivskyddad användare. Om det konto som du är inloggad med är i det Azure Active Directory som är länkat till arbetsytan är du en administratör i OMS-portalen, annars är du deltagare.
* När du loggar in på OMS-portalen med hjälp av http://mms.microsoft.com, visas som standard listan **Välj en arbetsyta**. Den innehåller endast arbetsytor som har lagts till med hjälp av OMS-portalen. Om du vill se arbetsytorna du har åtkomst till med Azure-prenumerationer måste du ange en klient som en del av URL:en. Exempel:

  `mms.microsoft.com/?tenant=contoso.com` Klient-ID är ofta den sista delen av e-postadressen som du loggade in med.
* Om det konto som du loggar in med är ett konto i klient-Azure Active Directory är du en *Administratör* i OMS-portalen. Detta är ofta vanligt om du inte loggar in som en kryptografiprovider.  Om ditt konto inte är i klient-Azure Active Directory så är du en *Användare* i OMS-portalen.
* Om du vill gå direkt till en portal som du har åtkomst till genom att använda Azure-behörigheter måste du ange resursen som en del av URL:en. Det är möjligt att hämta den här URL:en med hjälp av PowerShell.

  Till exempel `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  URL-adressen liknar: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

För att exempelvis lägga till eller ta bort hanteringslösningar måste användaren vara administratör eller bidragsgivare till Azure-prenumerationen vid användning av Azure Portal. Användaren måste dessutom ha bidragsgivar- eller ha administratörsrollen för OMS-arbetsytan i OMS-portalen.

### <a name="managing-users-in-the-oms-portal"></a>Hantera användare i OMS-portalen
Du kan hantera användare och grupp på fliken **Hantera användare** under fliken **Konton** på sidan Inställningar.   

![hantera användare](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Lägga till en användare till en befintlig arbetsyta
Följ stegen nedan om du vill lägga till en användare eller grupp på en arbetsyta.

1. Klicka på panelen **Inställningar** på OMS-portalen.
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
Alla arbetsytor som skapats efter den 26 september 2016 måste kopplas till en Azure-prenumeration vid tidpunkten för skapandet. Arbetsytor som skapats innan detta datum måste kopplas till en arbetsyta nästa gång du loggar in. När du skapar arbetsytan från Azure Portal eller när du länkar arbetsytan till en Azure-prenumeration länkas din Azure Active Directory som ditt organisationskonto.

![länka Azure-prenumeration](./media/log-analytics-manage-access/required-link.png)

> [!IMPORTANT]
> För att länka en arbetsyta måste Azure-kontot redan ha åtkomst till arbetsytan som du vill länka.  Med andra ord måste kontot som du använder för att få åtkomst till Azure Portal vara **samma** som det konto som används för att komma åt arbetsytan. Om det inte är det hittar du mer information i [Lägga till en användare till en befintlig arbetsyta](#add-a-user-to-an-existing-workspace).
>
>

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Om du vill länka en arbetsyta till en Azure-prenumeration i OMS-portalen
För att länka en arbetsyta till en Azure-prenumeration i OMS-portalen måste den inloggade användaren redan ha ett betalt Azure-konto.

1. Klicka på panelen **Inställningar** på OMS-portalen.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Azure-prenumeration och dataplan**.
3. Klicka på den plan som du vill använda.
4. Klicka på **Spara**.  
   ![prenumeration och dataplaner](./media/log-analytics-manage-access/subscription-tab.png)

Den nya dataplanen visas på menyfliken i OMS-portalen längst upp på webbsidan.

![OMS-menyflikar](./media/log-analytics-manage-access/data-plan-changed.png)

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
> Om du inte ser arbetsytan som du vill länka så har din Azure-prenumeration inte åtkomst till arbetsytan som du skapade via OMS-webbplatsen.  Läs [Lägga till en användare i en befintlig arbetsyta](#add-a-user-to-an-existing-workspace) om du vill bevilja åtkomst till kontot från OMS-portalen.
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Uppgradera en arbetsyta till en betald plan
Det finns tre typer av planer för arbetsytor i OMS: **Kostnadsfri**, **Fristående** och **OMS**.  Om du har planen *Kostnadsfri* finns det en gräns på 500 MB data per dag som kan skickas till Log Analytics.  Om du överskrider denna mängd måste du ändra din arbetsyta till en betald plan för att undvika att data inte samlas in utöver denna gräns. Du kan ändra din plantyp när som helst.  Mer information om OMS-priser finns i [Prisinformation](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Använda rättigheter från en OMS-prenumeration
För att använda rättigheter som kommer från inköp av OMS E1, OMS E2 OMS eller MS-tillägg för System Center väljer du *OMS*-planen för OMS Log Analytics.

När du köper en OMS-prenumeration läggs rättigheterna till i ditt Enterprise Agreement. Alla Azure-prenumerationer som har skapats under det här avtalet kan använda rättigheterna. Detta gör att du till exempel kan ha flera arbetsytor som använder rättigheten från OMS-prenumerationerna.

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

Om du har ett Azure-betalningsåtagande på företagsregistreringen som är kopplad till dina Azure-prenumerationer, kommer all användning av Log Analytics automatiskt att debiteras mot eventuella återstående betalningsåtaganden.

Om du behöver ändra Azure-prenumerationen som arbetsytan är länkad till kan du använda Azure PowerShell-cmdlet:en [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).  

### <a name="change-a-workspace-to-a-paid-pricing-tier"></a>Ändra en arbetsyta till en betalnivå
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

## <a name="change-how-long-log-analytics-stores-data"></a>Ändra hur länge Log Analytics lagrar data

På den kostnadsfria prisnivån tillgängliggör Log Analytics de senaste sju dagarnas data.
På standardprisnivån tillgängliggör Log Analytics de senaste 30 dagarnas data.
På premiumprisnivån tillgängliggör Log Analytics de senaste 365 dagarnas data.
På den fristående prisnivån och OMS-prisnivån tillgängliggör Log Analytics de senaste 31 dagarnas data.

När du använder den fristående prisnivån och OMS-prisnivån kan du behålla upp till två års data (730 dagar). Data som lagras längre än standardvärdet 31 dagar medför en avgift för datakvarhållning. Mer information om priser finns i [överförbrukningsdebitering](https://azure.microsoft.com/pricing/details/log-analytics/).

Ändra längden för datakvarhållning:

1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Bläddra efter **Log Analytics** och markera den.
3. Du ser listan över befintliga arbetsytor. Välj en arbetsyta.  
4. Klicka på **Kvarhållning** under **Allmänt** på bladet för arbetsytan.  
5. Använd skjutreglaget för att öka eller minska antalet dagar för kvarhållning och klicka sedan på **Spara**
![ändra kvarhållning](./media/log-analytics-manage-access/manage-access-change-retention01.png)

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Ändra en organisations Azure Active Directory för en arbetsyta

Du kan ändra en arbetsytas Azure Active Directory-organisation. Genom att ändra Azure Active Directory-organisationen kan du lägga till användare och grupper från katalogen till arbetsytan.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Så här ändrar du en organisations Azure Active Directory för en arbetsyta

1. På sidan Inställningar på OMS-portalen klickar du på **Konton** och sedan på fliken **Hantera användare**.  
2. Granska informationen om organisationskonton och klicka sedan på **Ändra organisation**.  
    ![ändra organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Ange ID-information för en administratör i Azure Active Directory-domänen. Därefter visas en bekräftelse om att din arbetsyta är kopplad till Azure Active Directory-domänen.  
    ![bekräftelse av länkad arbetsyta](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## <a name="delete-a-log-analytics-workspace"></a>Ta bort en Log Analytics-arbetsyta
När du tar bort en Log Analytics-arbetsyta raderas alla data som är relaterade till arbetsytan från OMS-tjänsten inom 30 dagar.

Om du är administratör och det finns flera användare som är kopplade till arbetsytan bryts kopplingen mellan användare och arbetsytan. Om användarna är associerade med andra arbetsytor, kan de fortsätta använda OMS med de andra arbetsytorna. Om de inte är associerade med andra arbetsytor måste de dock skapa en arbetsyta för att använda OMS.

### <a name="to-delete-a-workspace"></a>Så här tar du bort en arbetsyta
1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Bläddra efter **Log Analytics** och markera den.
3. Du ser listan över befintliga arbetsytor. Välj den arbetsyta som du vill ta bort.
4. Klicka på bladet för arbetsytan och sedan på **Ta bort**.  
    ![ta bort](./media/log-analytics-manage-access/delete-workspace01.png)
5. Klicka på **Ja** i bekräftelsedialogrutan för att ta bort arbetsytan.

## <a name="next-steps"></a>Nästa steg
* Se [Ansluta Windows-datorer till Log Analytics](log-analytics-windows-agents.md) för att lägga till agenter och samla in data.
* [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.
* [Konfigurera inställningar för proxyserver och brandvägg i Log Analytics](log-analytics-proxy-firewall.md) om din organisation använder en proxyserver eller brandvägg så att agenterna kan kommunicera med Log Analytics-tjänsten.



<!--HONumber=Feb17_HO3-->


