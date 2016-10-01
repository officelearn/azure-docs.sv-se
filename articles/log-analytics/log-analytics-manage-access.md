<properties
    pageTitle="Hantera åtkomst till Log Analytics | Microsoft Azure"
    description="Hantera åtkomst till Log Analytics med hjälp av olika administrativa uppgifter för användare, konton, OMS-arbetsytor och Azure-konton."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="banders"/>


# Hantera åtkomst till Log Analytics

För att hantera åtkomst till Log Analytics använder du administrativa åtgärder för användare, konton, OMS-arbetsytor och Azure-konton. Om du vill skapa en ny arbetsyta i Operations Management Suite (OMS), väljer du ett namn på arbetsytan, associerar det med ditt konto och du väljer en geografisk plats. En arbetsyta är i grunden en behållare som innehåller kontoinformation och enkel konfigurationsinformation för kontot. Du eller andra medlemmar i din organisation kan använda flera OMS-arbetsytor för att hantera olika uppsättningar av data som samlas in från alla eller delar av din IT-infrastruktur.

Artikeln [Kom igång med Log Analytics](log-analytics-get-started.md) visar hur du snabbt kommer igång och resten av den här artikeln beskriver i detalj några av de åtgärder som du behöver för att hantera åtkomst till OMS.

Även om du inte behöver utföra alla hanteringsaktiviteter, går vi igenom alla vanliga uppgifter som du kan komma använda i följande avsnitt:

- Bestämma antalet arbetsytor du behöver
- Hantera konton och användare
- Lägga till en grupp till en befintlig arbetsyta
- Länka en befintlig arbetsyta till en Azure-prenumeration
- Uppgradera en arbetsyta till en betald dataplan
- Ändra en dataplanstyp
- Lägga till en Azure Active Directory-organisation till en befintlig arbetsyta
- Stänga OMS-arbetsytan

## Bestämma antalet arbetsytor du behöver

En arbetsyta är en Azure-resurs och är en behållare där data samlas in, aggregeras, analyseras och presenteras i OMS-portalen.

Det är möjligt att skapa flera arbetsytor för OMS Log Analytics och för användare att ha tillgång till en eller flera arbetsytor. I allmänhet vill du minimera antalet arbetsytor eftersom detta gör att du kan fråga och korrelera över de flesta data. Det här avsnittet beskriver när det kan vara praktiskt att skapa fler än en arbetsyta.

Idag är innehåller en Log Analytics-arbetsyta:

- En geografisk plats för lagring av data
- Precision för fakturering
- Dataisolering

Baserat på ovanstående egenskaper kan du behöva skapa flera arbetsytor om:

- Du är ett globalt företag och data behöver lagras i vissa områden för datasuveränitet eller kompatibilitetsskäl.
- Du använder Azure och du vill undvika kostnader för överföring av utgående data genom att ha en Log Analytics-arbetsyta i samma region som de Azure-resurser den hanterar.
- Du vill fördela tillägg till olika avdelningar eller affärsgrupper baserat på deras användning. När du skapar en arbetsyta för varje avdelning eller affärsgrupp, visar Azure-fakturan och användningsutdraget avgifterna för varje arbetsyta separat.
- Du är en leverantör av hanterade tjänster och behöver logganalysdata för varje kund du hanterar isolerade från andra kunders data.
- Du hanterar flera kunder och du vill att varje kund, avdelning eller affärsgrupp ska se sina egna data, men inte data för andra kunder, avdelningar eller affärsgrupper.

När du använder agenter för att samla in data kan du konfigurera att varje agent rapporterar till krävd arbetsyta.

Om du använder System Center Operations Manager kan varje hanteringsgrupp för Operations Manager endast anslutas till en arbetsyta. Du kan installera Microsoft Monitoring Agent på datorer som hanteras av Operations Manager och låta agenten rapporten till både Operations Manager och en annan Log Analytics-arbetsyta.

### Arbetsyteinformation

I OMS-portalen kan du visa informationen om arbetsytan och välja om du vill få information från Microsoft.

#### Visa arbetsyteinformation

1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton**.
3. Klicka på fliken **Arbetsyteinformation**.  
  ![Arbetsyteinformation](./media/log-analytics-manage-access/workspace-information.png)

## Hantera konton och användare

Varje arbetsyta kan ha flera användarkonton kopplade till sig, och varje användarkonto (Microsoft-konto eller organisationskonto) kan ha åtkomst till flera OMS-arbetsytor.

Som standard blir det Microsoft-konto eller organisationskonto som använts för att skapa arbetsytan administratör för arbetsytan. Administratören kan sedan bjuda in ytterligare Microsoft-konton eller välja användare från Azure Active Directory.

Att ge åtkomst till OMS-arbetsytan styrs på 2 platser:

- I Azure kan du använda rollbaserad åtkomstkontroll för att ge åtkomst till Azure-prenumerationen och tillhörande Azure-resurser. Det används också för PowerShell- och REST API-åtkomst.
- Endast åtkomst till OMS-portalen, inte tillhörande Azure-prenumeration.

Om du ger användare åtkomst till OMS-portalen men inte Azure-prenumerationen som den är länkad till, visas inte panelerna Automation, Backup och Site Recovery någon information till användare när de loggar in på OMS-portalen.

Om du vill tillåta alla användare att se data i dessa lösningar, se till att de har minst **läs**-åtkomst för Automation-konto, Backup-valvet och Site Recovery-valvet som är länkat till OMS-arbetsytan.   

### Hantera åtkomst till Log Analytics i Azure-portalen

Om du ger användare åtkomst till arbetsytan Log Analytics med Azure-behörigheter, i Azure-portalen t.ex., kan samma användare sedan använda Log Analytics-portalen. Om användare är i Azure-portalen kan de navigera till OMS-portalen genom att klicka på åtgärden **OMS-portal** när de ser Log Analytics-arbetsytan.

Vissa saker att tänka på vad gäller Azure-portalen:

- Detta är inte *Rollbaserad åtkomstkontroll*. Om du har *Läs*-åtkomstbehörigheter i Azure-portalen för Log Analytics-arbetsytan kan du göra ändringar i OMS-portalen. OMS-portalen har ett koncept med administratör, deltagare och skrivskyddad användare. Om det konto du är inloggat med är i det Azure Active Directory som är länkat till arbetsytan blir du administratör i OMS-portalen, annars blir du deltagare.

- När du loggar in på OMS-portalen med hjälp av http://mms.microsoft.com, visas som standard listan **Välj en arbetsyta**. Den innehåller endast arbetsytor som har lagts till med hjälp av OMS-portalen. Om du vill se arbetsytorna du har åtkomst till med Azure-prenumerationer, måste du ange en klient som en del av URL:en. Några exempel:

  `mms.microsoft.com/?tenant=contoso.com` Klient-ID är ofta den sista delen av e-postadressen som du loggar in med.

- Om det konto du loggar in med är ett konto i klient-Azure Active Directory, vilket är ofta fallet om du inte loggar in som en kryptografiprovider, kommer du att bli *Administratör* i OMS-portalen. Om ditt konto inte är i klient-Azure Active Directory så kommer du att bli *Användare* i OMS-portalen.

- Om du vill gå direkt till en portal som du har åtkomst till genom att använda Azure-behörigheter måste du ange resursen som en del av URL:en. Det är möjligt att hämta den här URL:en med hjälp av PowerShell.

  Till exempel `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  URL-adressen ser ut som:
`https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`


### Hantera användare i OMS-portalen

Du kan hantera användare och grupp på fliken **Hantera användare** under fliken **Konton** på sidan Inställningar. Där kan du utföra uppgifter i följande avsnitt.  

![hantera användare](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### Lägga till en användare till en befintlig arbetsyta

Använd följande steg för att lägga till en användare eller grupp i en OMS-arbetsyta. Användaren eller gruppen kommer att kunna se och arbeta med alla aviseringar som är associerade med den här arbetsytan.

>[AZURE.NOTE] Om du vill lägga till en användare eller grupp från din organisations Azure Active Directory-konto måste du först se till att du har associerat OMS-kontot med Active Directory-domänen. Se [Lägga till en Azure Active Directory-organisation till en befintlig arbetsyta](#add-an-azure-active-directory-organization-to-an-existing-workspace).

1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Hantera användare**.
3. I avsnittet **Hantera användare** väljer du kontotyp för att lägga till: **Organisationskonto**, **Microsoft-konto**, **Microsoft Support**.
    - Om du väljer Microsoft-konto, skriver du e-postadressen för den användare som är associerad med Microsoft-kontot.
    - Om du väljer organisationskonto, kan du ange en del av användarens eller gruppens namn eller e-postalias så visas en lista över användare och grupper. Välj en användare eller grupp.
    - Använd Microsoft Support för att ge en Microsoft-supporttekniker tillfällig åtkomst till ditt arbetsområde för felsökning.

    >[AZURE.NOTE] Begränsa av prestandaskäl antalet Active Directory-grupper som är kopplade till ett enda OMS-konto till tre – en för administratörer, en för deltagare och en för skrivskyddade användare. Att använda flera grupper kan påverka prestandan hos Log Analytics.

5. Välj typ av användare eller grupp att lägga till: **Administratör**, **Deltagare** eller **skrivskyddad användare** .  
6. Klicka på **Lägg till**.

  Om du lägger till ett Microsoft-konto skickas en inbjudan att ansluta till arbetsytan till den e-postadress som du angav. När användaren följer instruktionerna i inbjudan att ansluta till OMS, kan användaren se aviseringar och kontoinformation för det här OMS-kontot och du kommer att kunna se användarinformation på fliken **Konton** på sidan **Inställningar**.
  Om du lägger till ett organisationskonto kommer användaren att kunna komma åt Log Analytics omedelbart.  
  ![e-postinbjudan](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### Redigera en befintlig användartyp

Du kan ändra kontorollen för en användare som är associerad med OMS-kontot. Du har följande rollalternativ:

 - *Administratör*: Kan hantera användare, visa och arbeta med alla aviseringar och lägga till och ta bort servrar

 - *Deltagare*: Kan visa och vidta åtgärder för alla aviseringar och lägga till och ta bort servrar

 - *Skrivskyddad användare*: Användare som har markerats som skrivskyddade kommer inte att kunna:
   1. Lägg till/ta bort lösningar. Lösningsgalleriet är dolt.
   2. Lägg till/ändra/ta bort ikoner på **Min instrumentpanel**.
   3. Visa sidorna **Inställningar**. Sidorna är dolda.
   4. I sökvyn är aktiviteterna, PowerBI-konfiguration, sparade sökningar och aviseringar dolda.


#### Så här redigerar du ett konto

1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Hantera användare**.
3. Välj rollen för den användare som du vill ändra.
2. I bekräftelsedialogrutan klickar du **Ja**.

### Ta bort en användare från en OMS-arbetsyta

Använd följande steg för att ta bort en användare från en OMS-arbetsyta. Observera att detta inte stänger användarens arbetsyta. I stället tas associationen mellan användaren och arbetsytan bort. Om en användare är associerad med flera arbetsytor, kommer användaren fortfarande att kunna logga in på OMS och se andra arbetsytor som finns.

1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Hantera användare**.
3. Klicka på **Ta bort** bredvid det användarnamn som du vill ta bort.
4. I bekräftelsedialogrutan klickar du **Ja**.


### Lägga till en grupp till en befintlig arbetsyta

1.  Följ steg 1 -4 i "Om du vill lägga till en användare till en befintlig arbetsyta" ovan.
2.  Under **Välj användare/grupp**, väljer du **Grupp**.
    ![lägga till en grupp till en befintlig arbetsyta](./media/log-analytics-manage-access/add-group.png)
3.  Ange ett visningsnamn eller en e-postadress för gruppen som du vill lägga till.
4.  Markera gruppen i listresultaten och klicka sedan på **Lägg till**.

## Länka en befintlig arbetsyta till en Azure-prenumeration

Det är möjligt att skapa en arbetsyta från webbplatsen [microsoft.com/oms](https://microsoft.com/oms).  Men det finns vissa begränsningar för dessa arbetsytor, den mest anmärkningsvärda är högst 500MB per dag för överföring av data om du använder ett kostnadsfritt konto. För att göra ändringar i den här arbetsytan behöver du *Länka din befintliga arbetsyta till en Azure-prenumeration*.

>[AZURE.IMPORTANT] För att länka en arbetsyta måste Azure-kontot redan ha åtkomst till arbetsytan som du vill länka.  Med andra ord måste kontot du använder för att få åtkomst till Azure-portalen vara **samma** som det konto som används för att komma åt OMS-arbetsytan. Om detta inte är fallet, se [Lägga till en användare till en befintlig arbetsyta](#add-a-user-to-an-existing-workspace).

### Om du vill länka en arbetsyta till en Azure-prenumeration i OMS-portalen

För att länka en arbetsyta till en Azure-prenumeration i OMS-portalen, måste den inloggade användaren redan ha ett betalt Azure-konto. Arbetsytan som du använder aktivt blir länkad till Azure-kontot.

1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Azure-prenumeration och dataplan**.
3. Klicka på den plan som du vill använda.
4. Klicka på **Spara**.  
  ![prenumeration och dataplaner](./media/log-analytics-manage-access/subscription-tab.png)

Den nya dataplanen visas på menyfliken i OMS-portalen längst upp på webbsidan.

![OMS-menyflikar](./media/log-analytics-manage-access/data-plan-changed.png)

### Om du vill länka en arbetsyta till en Azure-prenumeration i Azure-portalen

1.  Logga in på [Azure-portalen](http://portal.azure.com).
2.  Bläddra efter **Log Analytics (OMS)** och markerar den.
3.  Du ser listan över befintliga arbetsytor. Klicka på **Lägg till**.  
    ![lista över arbetsytor](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.  Under **OMS-arbetsytan**, klicka på **eller länka befintliga**.  
    ![länka befintliga](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.  Klicka på **Konfigurera nödvändiga inställningar**.  
    ![konfigurera nödvändiga inställningar](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.  Du ser listan över arbetsytor som ännu inte har länkats till ditt Azure-konto. Välj en arbetsyta.  
    ![välj arbetsytor](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.  Om det behövs, kan du ändra värdena för följande objekt:
    - Prenumeration
    - Resursgrupp
    - Plats
    - Prisnivå  
        ![ändra värden](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.  Klicka på **Skapa**. Arbetsytan är nu länkad till ditt Azure-konto.

>[AZURE.NOTE] Om du inte ser arbetsytan som du vill länka så saknar din Azure-prenumeration åtkomst till OMS-arbetsytan som du skapade med OMS-webbplatsen.  Du måste bevilja åtkomst till det här kontot inifrån OMS-arbetsytan via OMS-webbplatsen. Du hittar information om att göra detta i [Lägga till en användare till en befintlig arbetsyta](#add-a-user-to-an-existing-workspace).



## Uppgradera en arbetsyta till en betald dataplan

Det finns tre typer av dataplaner för arbetsytor i OMS: **Gratis**, **Standard** och **Premium**.  Om du har en *Gratis* plan kan du ha uppnått datakapaciteten på 500 MB.  Du måste uppgradera arbetsytan till en plan som ***betalar per användning*** för att samla in data utöver den här gränsen. Du kan konvertera din plantyp när som helst.  Mer information om OMS-priser finns i [Prisinformation](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] Arbetsytans planer kan bara ändras om de är *länkade* till en Azure-prenumeration.  Om du har skapat din arbetsyta i Azure eller om du *redan* har länkat arbetsytan, kan du ignorera det här meddelandet.  Om du har skapat ditt arbetsområde med [OMS-webbplatsen](http://www.microsoft.com/oms), måste du följa stegen i [Länka en befintlig arbetsyta till en Azure-prenumeration](#link-an-existing-workspace-to-an-azure-subscription).

### Använda rättigheter från OMS-tillägg för System Center 

OMS-tillägget för System Center innehåller ett berättigande för premiumplan för OMS Log Analytics, som beskrivs i [OMS-priser](https://www.microsoft.com/en-us/server-cloud/operations-management-suite/pricing.aspx).

När du köper OMS-tillägget för System Center läggs OMS-tillägget till som ett berättigande i ditt System Center-avtal. Alla Azure-prenumerationer som har skapats under det här avtalet kan använda berättigandet. Detta gör att du till exempel kan ha flera OMS-arbetsytor med berättigandet från OMS-tillägget.

För att säkerställa att användningen av en OMS-arbetsyta tillämpas till dina berättiganden från OMS-tillägget, måste du:

1. Länka OMS-arbetsytan till en Azure-prenumeration som är en del av ett Enterprise-avtal som innehåller både OMS-tilläggsinköp och Azure-prenumerationsanvändning
2. Välj premiumplan för arbetsytan

När du granskar din användning i Azure eller OMS-portalen, ser du inte OMS-tilläggsberättigandena. Du kan dock se berättigandena i Enterprise Portal.  

Om du behöver ändra Azure-prenumerationen som OMS-arbetsytan är länkad till kan du använda cmdlet:en för Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

### Använda Azure-åtagande från ett Enterprise-avtal

Om du väljer att använda fristående priser för OMS-komponenter betalar du för varje komponent i OMS separat och användningen visas på Azure-fakturan.

Om du har ett Azure-betalningsåtagande på företagsregistreringen som är kopplad till dina Azure-prenumerationer, kommer all användning av Log Analytics automatiskt att debiteras mot eventuella återstående betalningsåtaganden.

Om du behöver ändra Azure-prenumerationen som OMS-arbetsytan är länkad till kan du använda cmdlet:en för Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).  



### Ändra en arbetsyta till en betald dataplan

1.  Logga in på [Azure-portalen](http://portal.azure.com).
2.  Bläddra efter **Log Analytics (OMS)** och markerar den.
3.  Du ser listan över befintliga arbetsytor. Välj en arbetsyta.  
    ![lista över arbetsytor](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.  Under **Inställningar**, klicka på **Prisnivå**.  
    ![prisnivå](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.  Under **Prisnivå**, välj en dataplan och klicka på **Välj**.  
    ![välj plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.  När du har uppdaterat vyn i Azure-portalen visas **Prisnivå** uppdaterad med den plan som du har valt.  
    ![uppdatera prisnivå](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Nu kan du samla in data utanför datakapaciteten "gratis".


## Lägga till en Azure Active Directory-organisation till en befintlig arbetsyta

Du kan associera arbetsytan för Log Analytics (OMS) med en Azure Active Directory-domän. På så sätt kan du lägga till användare från Active Directory direkt till OMS-arbetsytan utan att ett separat Microsoft-konto.

När du skapar arbetsytan från Azure-portalen eller länkar arbetsytan till en Azure-prenumeration länkas Azure Active Directory som ditt organisationskonto.

När du skapar arbetsytan från OMS-portalen uppmanas du att länka till en Azure-prenumeration och ett organisationskonto.

### Så här lägger du till en Azure Active Directory-organisation till en befintlig arbetsyta

1. På sidan Inställningar i OMS klickar du på **Konton** och klickar sedan på fliken **Arbetsyteinformation**.  
2. Granska informationen om organisationskonton och klicka sedan på **Lägga till organisation**.  
    ![lägg till organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Ange ID-information för en administratör i Azure Active Directory-domänen. Därefter visas en bekräftelse om att din arbetsyta är kopplad till Azure Active Directory-domänen.
    ![bekräftelse av länkad arbetsyta](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

>[AZURE.NOTE] När ditt konto är länkat till ett organisationskonto kan länkningen inte tas bort eller ändras.

## Stänga OMS-arbetsytan

När du stänger en OMS-arbetsyta raderas alla data som är relaterade till arbetsytan från OMS-tjänsten inom 30 dagar från det att arbetsytan stängts.

Om du är administratör och det finns flera användare som är kopplade till arbetsytan bryts kopplingen mellan användare och arbetsytan. Om användarna är associerade med andra arbetsytor, kan de fortsätta använda OMS med de andra arbetsytorna. Men om de inte är associerade med andra arbetsytor behöver de skapa en ny arbetsyta för att använda OMS.

### Om du vill stänga en OMS-arbetsyta

1. I OMS, klickar du på ikonen **Inställningar**.
2. Klicka på fliken **Konton** och klicka sedan på fliken **Arbetsyteinformation**.
3. Klicka på **Stäng arbetsytan**.
4. Välj en av följande orsaker för att stänga ditt arbetsområde eller ange en annan orsak i textrutan.
5. Klicka på **Stäng arbetsytan**.

## Nästa steg

- Se [Ansluta Windows-datorer till Log Analytics](log-analytics-windows-agents.md) för att lägga till agenter och samla in data.
- [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.
- [Konfigurera inställningar för proxyserver och brandvägg i Log Analytics](log-analytics-proxy-firewall.md) om din organisation använder en proxyserver eller brandvägg så att agenterna kan kommunicera med Log Analytics-tjänsten.



<!--HONumber=Sep16_HO3-->


