<properties
    pageTitle="Starta/stoppa virtuella datorer vid låg belastning på nätverket [förhandsgranskning] Lösning | Microsoft Azure"
    description="VM-hanteringslösningar startar och stoppar Azure-resurshanteraren för virtuella datorer i ett schema och övervakar proaktivt från Log Analytics."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/07/2016"
    ms.author="magoedte"/>


# Starta/stoppa virtuella datorer vid låg belastning på nätverket [förhandsgranskning] Lösning i Automation

Lösningen starta/stoppa VM:ar vid låg belastning [förhandsversion], startar och stoppar dina Azure Resource Manager virtuella datorer enligt ett användardefinierat schema och ger insikt i hur väl Automation-jobben är som startar och stoppar dina virtuella datorer med OMS Log Analytics.  

## Krav

- Runbooks använder ett [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).  Kör som-kontot är den lämpligaste autentiseringsmetoden eftersom den använder certifikatautentisering istället för ett lösenord som kan upphöra att gälla eller ändras ofta.  

- Den här lösningen kan endast hantera virtuella datorer som finns i samma prenumeration och resursgrupp som Automation-kontot.  

- Den här lösningen endast distribueras till följande Azure-regioner - sydöstra Australien, östra USA, Sydostasien och Västeuropa.  Runbooks som hanterar VM-schemat kan riktas in på virtuella datorer i valfri region.  

- Om du vill skicka e-postaviseringar när starta och stoppa virtuella runbooks har slutförts, krävs en företagsprenumeration på Office 365.  

## Lösningskomponenter

Den här lösningen består av följande resurser som ska importeras och läggas till i Automation-kontot.

### Runbooks

Runbook | Beskrivning|
--------|------------|
CleanSolution-MS-Mgmt-VM | Denna runbook tar bort alla inkluderade resurser och scheman för när du vill ta bort lösningen från prenumerationen.|  
SendMailO365-MS-Mgmt | Denna runbook skickar ett e-postmeddelande via Office 365 Exchange.|
StartByResourceGroup-MS-Mgmt-VM | Denna runbook är avsedd att starta virtuella datorer (både klassiskt och ARM-baserade virtuella datorer) som finns i en specifik lista med Azure resursgrupperna.
StopByResourceGroup-MS-Mgmt-VM | Denna runbook är avsedd att stoppa virtuella datorer (både klassiskt och ARM-baserade virtuella datorer) som finns i en specifik lista med Azure resursgrupperna.|
<br>

### Variabler

Variabel | Beskrivning|
---------|------------|
**SendMailO365-MS-Mgmt** Runbook ||
SendMailO365-IsSendEmail-MS-Mgmt | Anger om StartByResourceGroup-MS-Mgmt-VM och StopByResourceGroup-MS-Mgmt-VM-runbooks kan skicka e-postmeddelande när återställningen är klar.  Välj **Sant** för att aktivera och **Falskt** för att inaktivera aviseringar för e-post. Standardvärdet är **Falskt**.| 
**StartByResourceGroup-MS-Mgmt-VM** Runbook ||
StartByResourceGroup-ExcludeList-MS-Mgmt-VM | Ange VM-namn som ska undantas från hanteringsaktiviteten; separera namn med hjälp av semikolon(;). Värden är skiftlägeskänsliga och jokertecken (asterisk) stöds.|
StartByResourceGroup SendMailO365-EmailBodyPreFix-MS-Mgmt | Text som kan läggas till i början av e-postmeddelandet.|
StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | Anger namnet på Automation-kontot som innehåller en e-postrunbook.  **Ändra inte den här variabeln.**|
StartByResourceGroup SendMailO365-EmailRunbookName-MS-Mgmt | Anger namnet på runbooken som e-post.  Används av StartByResourceGroup-MS-Mgmt-VM och StopByResourceGroup-MS-Mgmt-VM-runbooks för att skicka e-postmeddelanden.  **Ändra inte den här variabeln.**|
StartByResourceGroup SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | Anger namnet på Automation-kontot som innehåller en e-postrunbook.  **Ändra inte den här variabeln.**|
StartByResourceGroup SendMailO365-EmailSubject-MS-Mgmt | Anger texten i ämnesraden för e-postmeddelandet.|  
StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | Anger mottagaren av e-postmeddelandet.  Ange separata namn med semikolon (;).|
StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | Ange VM-namn som ska undantas från hanteringsaktiviteten; separera namn med hjälp av semikolon(;). Värden är skiftlägeskänsliga och jokertecken (asterisk) stöds.  Standardvärdet (asterisk) inkluderar alla resursgrupper i prenumerationen.|
StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | Anger den prenumeration som innehåller virtuella datorer som ska hanteras av den här lösningen.  Detta måste vara samma prenumeration där Automation-kontot för den här lösningen finns.|
**StopByResourceGroup-MS-Mgmt-VM** Runbook ||
StopByResourceGroup-ExcludeList-MS-Mgmt-VM | Ange VM-namn som ska undantas från hanteringsaktiviteten; separera namn med hjälp av semikolon(;). Värden är skiftlägeskänsliga och jokertecken (asterisk) stöds.|
StopByResourceGroup SendMailO365-EmailBodyPreFix-MS-Mgmt | Text som kan läggas till i början av e-postmeddelandet.|
StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | Anger namnet på Automation-kontot som innehåller en e-postrunbook.  **Ändra inte den här variabeln.**|
StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | Anger namnet på Automation-kontot som innehåller en e-postrunbook.  **Ändra inte den här variabeln.**|
StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | Anger texten i ämnesraden för e-postmeddelandet.|  
StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | Anger mottagaren av e-postmeddelandet.  Ange separata namn med semikolon (;).|
StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | Ange VM-namn som ska undantas från hanteringsaktiviteten; separera namn med hjälp av semikolon(;). Värden är skiftlägeskänsliga och jokertecken (asterisk) stöds.  Standardvärdet (asterisk) inkluderar alla resursgrupper i prenumerationen.|
StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | Anger den prenumeration som innehåller virtuella datorer som ska hanteras av den här lösningen.  Detta måste vara samma prenumeration där Automation-kontot för den här lösningen finns.|  
<br>

### Scheman

Schema | Beskrivning|
---------|------------|
StartByResourceGroup-schema-MS-Mgmt | Schema för StartByResourceGroup runbooken som startar VM:ar som hanteras av den här lösningen.|
StopByResourceGroup-schema-MS-Mgmt | Schema för StopByResourceGroup runbooken som stänger ner VM:ar som hanteras av den här lösningen.|

### Autentiseringsuppgifter

Autentiseringsuppgift | Beskrivning|
-----------|------------|
O365-Autentiseringsuppgift | Anger ett giltigt Office 365-konto för att skicka e-post.  Krävs endast om variabeln SendMailO365-IsSendEmail-MS-Mgmt är inställd på **Sant**.

## Konfiguration

Utför följande steg för att lägga till lösningen Starta/Stoppa virtuella datorer vid låg belastning på nätverket [förhandsgranskning] i Automation-konto och konfigurera variabler för att anpassa lösningen.

1. Välj hemskärmen i Azure-portalen på panelen **Marketplace** .  Om panelen är inte längre fäst på hemskärmen ska du välja **Ny** i den vänstra navigationspanelen.  
2. Skriv **Starta VM** i sökrutan i bladet Marketplace och välj sedan lösningen **Starta/Stoppa VM under kontorstid [förhandsgranskning]** från sökresultaten.  
3. I bladet **Starta/Stoppa VM under kontorstid [förhandsgranskning]** för den valda lösningen läser du sammanfattningsinformationen och klickar sedan på **Skapa**.  
4. Bladet **Lägga till lösning** visas där du uppmanas att konfigurera lösningen innan du kan importera den till din Automation-prenumeration.<br><br> ![Bladet VM-hantering, Lägga till lösning](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5.  På bladet **Lägga till lösning** väljer du **Arbetsyta**. Här kan du välja en OMS arbetsyta som är länkad till samma Azure-prenumeration som Automation-kontot eller skapa en ny OMS-arbetsyta.  Om du inte har en OMS-arbetsyta kan du välja **Skapa ny arbetsyta** och utföra följande på bladet **OMS-arbetsyta**: 
   - Ange ett namn för den nya **OMS-arbetsytan**.
   - Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   - Du kan skapa en ny **resursgrupp** eller välja en befintlig resursgrupp.  
   - Välj en **Plats**.  För närvarande kan endast regionerna **Australien, sydöstra**, ** USA, östra**, **Sydostasien**, och **Västeuropa** väljas.
   - Välj en **Prisnivå**.  Lösningen erbjuds i två nivåer: kostnadsfri eller betald med OMS.  Den kostnadsfria nivån har en gräns för mängden information som samlas in varje dag, kvarhållningsperioden och körtid för runbook-jobb.  Betalningsnivån har ingen daglig gräns för insamlad data.  

        > [AZURE.NOTE]
        > Även om den fristående betalda nivån visas som ett alternativ så är den inte tillämplig.  Om du väljer den och fortsätter att försöka skapa den här lösningen i din prenumeration kommer detta att misslyckas.  Detta åtgärdas när den här lösningen släpps officiellt.<br>Om du använder den här lösningen, kommer den endast använda automation-jobbminuter och logga inmatning.  Lösningen lägger inte till ytterligare OMS-noder i miljön.  

6. När du har angett informationen som krävs på bladet **OMS arbetsyta**, klickar du på **Skapa**.  När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn.  Du kommer tillbaka till bladet **Lägg till lösning**.  
7. På bladet **Lägga till lösning** väljer du **Automation-konto**.  Om du skapar en ny OMS arbetsyta, kommer du dessutom att behöva skapa tt ny Automation-konto som ska associeras med den nya OMS-arbetsytan, inklusivedin Azure-prenumeration, resursgrupp och region.  Du kan välja **Skapa ett Automation-konto** och ange följande på bladet **Lägga till Automation-konto**: 
  - I fältet **namn** anger du namnet på Automation-kontot.

    Alla andra alternativ fylls i automatiskt baserat på den valda OMS-arbetsytan. Dessa alternativ kan inte ändras.  Ett Azure kör som-konto är standardmetoden för autentisering för runbooks som ingår i den här lösningen.  När du klickar på **OK** verifieras konfigurationsalternativen och Automation-kontot skapas.  Du kan spåra förloppet under **Meddelanden** på menyn. 

    Annars kan du välja ett befintligt Automation kör som-konto.  Observera att det konto som du väljer får inte redan vara kopplat till en annan OMS-arbetsyta. Om detta är fallet kommer ett meddelande att visas i bladet som informerar dig om detta.  Om det redan är länkat måste du välja ett annat Automation kör som-konto eller skapa ett nytt.<br><br> ![Automation-konto som redan är länkat till OMS-arbetsyta](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Välj slutligen **Konfiguration** på bladet **Lägga till lösning**. Bladet **Parametrar** visas.  På bladet **Parametrar** uppmanas du att:  
   - Ange **målresursnamn**, vilket är ett resursgruppsnamn som innehåller virtuella datorer som hanteras av den här lösningen.  Du kan ange flera namn och skilja dem åt med ett semikolon (värden är skiftlägeskänsliga).  Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen.
   - Välj ett **Schema** som är ett återkommande datum och tid för att starta och stoppa virtuella datorer i målresursgrupperna.  

10. När du har slutfört konfigurationen av de ursprungliga inställningarna som krävs för lösningen väljer du **Skapa**.  Alla inställningar kommer att valideras och sedan kommer lösningen att distribueras i din prenumeration.  Den här processen kan ta flera sekunder att slutföra och du kan spåra förloppet under **Meddelanden** på menyn. 

## Insamlingsfrekvens

Automation-jobbloggen och arbetsflödesdata matas in i OMS-databasen var femte minut.  

## Använda lösningen

När du lägger till hanteringslösningen för virtuella datorer i OMS-arbetsytan läggs panelen **StartStopVM-vy** till på OMS-instrumentpanelen.  Den här panelen visar ett antal och en grafisk representation av runbooksjobben för lösningen som har startat och har slutförts.<br><br> ![Panelen VM-hantering, StartStopVM-vy](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

I Automation-kontot kan du komma åt och hantera lösningen genom att välja panelen **lösningar**. Välj lösningen **Start stoppa VM [Workspace]** från listan i bladet **lösningar**.<br><br> ![Lista över Automation-lösningar](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

Om lösningen markeras visas lösningsbladet **Starta-stoppa VM [Workspace]**där du kan granska viktig information, till exempel panelen **StartStopVM** som i din OMS arbetsyta som visar antal och grafisk representation av runbooksjobb för lösningen som har startat och har slutförts.<br><br> ![Lösningsblad i Automation VM](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

Härifrån kan du också öppna din OMS-arbetsyta och utföra ytterligare analys av jobbposterna.  Klicka bara på **Alla inställningar** och i bladet **Inställningar**, väljer du **Snabbstart** och sedan i bladet **Snabbstart**, väljer du **OMS Portal**.   Det öppnar en ny flik eller en ny webbläsarsession och visar dig OMS-arbetsytan som är kopplad till ditt Automation-konto och prenumeration.  


### Konfigurera e-postaviseringar

Om du vill aktivera e-postmeddelanden när runbooken starta och stoppa VM har slutförts behöver du ändra autentiseringsuppgifterna för **O365Credential** och minst följande variabler:

 - SendMailO365-IsSendEmail-MS-Mgmt
 - StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
 - StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

Konfigurera autentiseringsuppgifterna för **O365Credential** med följande steg:

1. Klicka du på **Alla inställningar** längst upp i fönstret i ditt Automation-konto.. 
2. På bladet **Inställningar** under avsnittet **Automation-resurser** väljer du **Tillgångar**. 
3. På bladet **tillgångar** väljer du panelen **autentiseringsuppgifter** och från bladet **autentiseringsuppgifter** väljer du **O365Credential**.  
4. Ange ett giltigt Office 365-användarnamn och lösenord och klicka sedan på **Spara** att spara ändringarna.  

Utför följande steg för att konfigurera de variabler som markerats tidigare:

1. Klicka du på **Alla inställningar** längst upp i fönstret i ditt Automation-konto.. 
2. På bladet **Inställningar** under avsnittet **Automation-resurser** väljer du **Tillgångar**. 
3. På bladet **Tillgångar** väljer du panelen **variabler** panelen. Från bladet **Variabler** väljer du variabeln från listan och modifierar dess värde enligt dess beskrivning i avsnittet [Variabel](##variables) ovan.  
4. Klicka på **Spara** för att ändringarna hos variabeln.   

### Ändra schemat för start och avstängning

Hantera schemat för start och avstängning i den här lösningen genom att följa samma steg som beskrivs i [Schemalägg en runbook i Azure Automation](automation-scheduling-a-runbook.md).  Kom ihåg att du inte kan ändra schemakonfigurationen.  Du måste inaktivera det befintliga schemat och skapa ett nytt som du länkar till runbooken **StartByResourceGroup-MS-Mgmt-VM** eller **StopByResourceGroup-MS-Mgmt-VM** som du vill att schemat ska gälla för.   

## Log Analytics-poster

Automation skapar två typer av poster i OMS-databasen.

### Jobbloggar

Egenskap | Beskrivning|
----------|----------|
Anropare |  Den som initierade åtgärden.  Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
Kategori | Klassificering av typ av data.  För Automation är värdet JobLogs.|
CorrelationId | GUID som är korrelations-Id för runbook-jobbet.|
JobId | GUID som är Id för runbook-jobbet.|
operationName | Anger åtgärdstypen i Azure.  För Automation är värdet Job.|
resourceId | Anger resurstypen i Azure.  För Automation är värdet Automation-kontot som är kopplat till runbook.|
ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera.  För Automation är värdet Azure Automation.|
ResourceType | Anger resurstypen i Azure.  För Automation är värdet Automation-kontot som är kopplat till runbook.|
resultType | Status för runbookjobbet.  Möjliga värden:<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Slutförd|
resultDescription | Beskriver jobbstatusen för runbook.  Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes|
RunbookName | Anger namnet på runbooken.|
SourceSystem | Anger källsystemet för data som skickats.  För Automation är värdet: OpsManager|
StreamType | Anger händelsetypen. Möjliga värden:<br>- Verbose<br>- Utdata<br>- Fel<br>- Varning|
SubscriptionId | Anger prenumerations-ID för jobbet.
Tid | Datum och tid då runbook-jobbet körs.|


### Arbetsflöden

Egenskap | Beskrivning|
----------|----------|
Anropare |  Den som initierade åtgärden.  Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
Kategori | Klassificering av typ av data.  För Automation är värdet JobStreams.|
JobId | GUID som är Id för runbook-jobbet.|
operationName | Anger åtgärdstypen i Azure.  För Automation är värdet Job.|
ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
resourceId | Anger resurs-Id i Azure.  För Automation är värdet Automation-kontot som är kopplat till runbook.|
ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera.  För Automation är värdet Azure Automation.|
ResourceType | Anger resurstypen i Azure.  För Automation är värdet Automation-kontot som är kopplat till runbook.|
resultType | Resultatet av runbook-jobbet då händelsen skapades.  Möjliga värden:<br>- Ärendeposter|
resultDescription | Innehåller utdataströmmen från runbook.|
RunbookName | Anger namnet på runbooken.|
SourceSystem | Anger källsystemet för data som skickats.  För Automation är värdet OpsManager|
StreamType | Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose|
Tid | Datum och tid då runbook-jobbet körs.|

När du utför alla loggsökningar som returnerar poster för **JobLogs** eller **JobStreams**, kan du välja vyn **JobLogs** eller **JobStreams** som visar en uppsättning paneler som sammanfattar de uppdateringar som returneras av sökningen.

## Exempel på loggsökningar

Följande tabell innehåller exempel på sökningar i loggen för jobbposter som har samlats in av den här lösningen. 

Fråga | Beskrivning|
----------|----------|
Hitta jobb för runbook StartVM som har slutförts | Kategori = JobLogs RunbookName_s = "StartByResourceGroup-MS-Mgmt-VM" ResultType = lyckades &#124; mätning av antal () efter JobId_g|
Hitta jobb för runbook StopVM som har slutförts | Kategori = JobLogs RunbookName_s = "StartByResourceGroup-MS-Mgmt-VM" ResultType = lyckades &#124; mätning av antal () efter JobId_g
Visa jobbstatus med tiden för runbookarna StartVM och StopVM | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ELLER "StopByResourceGroup-MS-Mgmt-VM" INTE(ResultType="started") | Mät Count() ResultType intervall 1 dag|

## Nästa steg

- Läs mer om hur du konstruerar olika sökfrågor och granskar jobbloggarna i Automation med Log Analytics i [Logga sökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra runbook-jobb](automation-runbook-execution.md)
- Läs mer om OMS Log Analytics och datakällsamling i [Samla in data om Azure-lagring i Log Analytics-översikten](../log-analytics/log-analytics-azure-storage.md)






   




<!--HONumber=Oct16_HO3-->


