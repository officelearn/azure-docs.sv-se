---
title: Hanteringslösning för Office 365 i Azure | Microsoft-dokument
description: Den här artikeln innehåller information om konfiguration och användning av Office 365-lösningen i Azure.  Den innehåller detaljerad beskrivning av Office 365-posterna som skapats i Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/08/2019
ms.openlocfilehash: 0018ae55ab74e691577a34a397c15355587e0fac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663274"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Hanteringslösning för Office 365 i Azure (förhandsversion)

![Office 365-logotyp](media/solution-office-365/icon.png)


> [!IMPORTANT]
> ## <a name="solution-update"></a>Uppdatering av lösning
> Den här lösningen har ersatts av [office 365-lösningen](../../sentinel/connect-office-365.md) för allmän tillgänglighet i [Azure Sentinel](../../sentinel/overview.md) och azure [AD-rapporterings- och övervakningslösningen](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Tillsammans tillhandahåller de en uppdaterad version av den tidigare Azure Monitor Office 365-lösningen med en förbättrad konfigurationsupplevelse. Du kan fortsätta att använda den befintliga lösningen fram till den 30 april 2020.
> 
> Azure Sentinel är en molnbaserad säkerhetsinformations- och händelsehanteringslösning som matar in loggar och tillhandahåller ytterligare SIEM-funktioner, inklusive identifieringar, undersökningar, jakt- och maskininlärningsdrivna insikter. Med Azure Sentinel får du nu information om Office 365 SharePoint-aktivitet och Exchange-hanteringsloggar.
> 
> Azure AD-rapportering ger en mer omfattande vy över loggar från Azure AD-aktivitet i din miljö, inklusive inloggningshändelser, granskningshändelser och ändringar i katalogen. Om du vill ansluta Azure AD-loggar kan du använda Azure [Sentinel Azure AD-anslutningen](../../sentinel/connect-azure-active-directory.md) eller konfigurera [Azure AD-loggningsintegrering med Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> Insamlingen av Azure AD-loggen är föremål för Azure Monitor-priser.  Mer information finns i [Azure Monitor-priser.](https://azure.microsoft.com/pricing/details/monitor/)
>
> Så här använder du Azure Sentinel Office 365-lösningen:
> 1. Om du använder Office 365-anslutningsappen i Azure Sentinel påverkas priserna för din arbetsyta. Mer information finns i [Azure Sentinel-priser](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Om du redan använder Azure Monitor Office 365-lösningen måste du först avinstallera den med skriptet i [avsnittet Avinstallera nedan](#uninstall).
> 3. [Aktivera Azure Sentinel-lösningen](../../sentinel/quickstart-onboard.md) på din arbetsyta.
> 4. Gå till sidan **Datakopplingar** i Azure Sentinel och aktivera **Office 365-anslutningen.**
>
> ## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-april-30th"></a>F: Är det möjligt att vara ombord på Office 365 Azure Monitor-lösningen fram till den 30 april?
> Nej, Azure Monitor Office 365-lösningsskript för introduktion av office är inte längre tillgängliga. Lösningen kommer att tas bort den 30 april.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>F: Ändras tabellerna och schemana?
> **Registret OfficeActivity-tabellens** namn och schema förblir desamma som i den aktuella lösningen. Du kan fortsätta använda samma frågor i den nya lösningen exklusive frågor som refererar till Azure AD-data.
> 
> De nya [Azure AD-rapporterings- och övervakningslösningsloggarna](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) kommer att intas i [tabellerna SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) och [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) i stället för **OfficeActivity**. Mer information finns i [hur du analyserar Azure AD-loggar](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), som också är relevant för Azure Sentinel- och Azure Monitor-användare.
> 
> Följande är exempel för att konvertera frågor från **OfficeActivity** till **SigninLogs:**
> 
> **Frågan misslyckades inloggningar, av användaren:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId 
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Visa Azure AD-åtgärder:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>F: Hur kan jag vara ombord på Azure Sentinel?
> Azure Sentinel är en lösning som du kan aktivera på ny eller befintlig Log Analytics-arbetsyta. Mer information finns i [Azure Sentinel-dokumentationen för ombordstigning](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>F: Behöver jag Azure Sentinel för att ansluta Azure AD-loggarna?
> Du kan konfigurera [Azure AD-loggar integrering med Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), som inte är relaterad till Azure Sentinel-lösningen. Azure Sentinel tillhandahåller en inbyggd anslutningsapp och innehåll utanför boxen för Azure AD-loggar. Mer information finns i frågan nedan om säkerhetsorienterat innehåll utanför boxen.
>
> ###   <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>F: Vilka är skillnaderna när du ansluter Azure AD-loggar från Azure Sentinel och Azure Monitor?
> Azure Sentinel och Azure Monitor ansluter till Azure AD-loggar baserat på samma [Azure AD-rapporterings- och övervakningslösning](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Azure Sentinel tillhandahåller en inbyggd anslutningsapp med ett klick som ansluter samma data och tillhandahåller övervakningsinformation.
>
> ###   <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>F: Vad behöver jag ändra när jag flyttar till de nya Azure AD-rapporterings- och övervakningstabellerna?
> Alla frågor som använder Azure AD-data, inklusive frågor i aviseringar, instrumentpaneler och allt innehåll som du har skapat med Office 365 Azure AD-data, måste återskapas med hjälp av de nya tabellerna.
>
> Azure Sentinel och Azure AD tillhandahåller inbyggt innehåll som du kan använda när du flyttar till Azure AD-rapporterings- och övervakningslösningen. Mer information finns i nästa fråga om säkerhetsorienterat innehåll och [hur du använder Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>F: Hur kan jag använda Azure Sentinel-säkerhetsorienterat innehåll?
> Azure Sentinel tillhandahåller säkerhetsorienterade instrumentpaneler utanför boxen, anpassade varningsfrågor, jaktfrågor, undersöknings- och automatiserade svarsfunktioner baserat på Office 365- och Azure AD-loggarna. Utforska Azure Sentinel GitHub och självstudier för att lära dig mer:
>
> - [Identifiera hot utanför boxen](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Skapa anpassade analysregler för att identifiera misstänkta hot](../../sentinel/tutorial-detect-threats-custom.md)
> - [Övervaka dina data](../../sentinel/tutorial-monitor-your-data.md)
> - [Undersöka incidenter med Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Konfigurera automatiska hotsvar i Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Azure Sentinel GitHub-community](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>F: Tillhandahåller Azure Sentinel ytterligare anslutningsappar som en del av lösningen?
> Ja, se [Azure Sentinel ansluta datakällor](../../sentinel/connect-data-sources.md).
> 
> ###   <a name="q-what-will-happen-on-april-30-do-i-need-to-offboard-beforehand"></a>F: Vad händer den 30 april? Måste jag gå överbord i förväg?
> 
> - Du kan inte ta emot data från **Office365-lösningen.** Lösningen kommer inte längre att vara tillgänglig på Marketplace
> - För Azure Sentinel-kunder inkluderas log analytics-arbetsytelösningen **Office365** i Azure Sentinel **SecurityInsights-lösningen.**
> - Om du inte går utanför lösningen manuellt kopplas dina data bort automatiskt den 30 april.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>F: Kommer mina data att överföras till den nya lösningen?
> Ja. När du tar bort **Office 365-lösningen** från arbetsytan blir dess data inte tillgängliga eftersom schemat tas bort. När du aktiverar den nya **Office 365-kopplingen** i Sentinel återställs schemat till arbetsytan och alla data som redan samlas in blir tillgängliga. 
 

Med hanteringslösningen för Office 365 kan du övervaka din Office 365-miljö i Azure Monitor.

- Övervaka användaraktiviteter på dina Office 365-konton för att analysera användningsmönster och identifiera beteendetrender. Du kan till exempel extrahera specifika användningsscenarier, till exempel filer som delas utanför organisationen eller de mest populära SharePoint-webbplatserna.
- Övervaka administratörsaktiviteter för att spåra konfigurationsändringar eller åtgärder med hög behörighet.
- Identifiera och undersöka oönskade användarbeteenden, som kan anpassas efter dina organisationsbehov.
- Visa granskning och efterlevnad. Du kan till exempel övervaka filåtkomståtgärder på konfidentiella filer, vilket kan hjälpa dig med gransknings- och efterlevnadsprocessen.
- Utför driftfelsökning med hjälp av [loggfrågor](../log-query/log-query-overview.md) ovanpå Office 365-aktivitetsdata i din organisation.


## <a name="uninstall"></a>Avinstallera

Du kan ta bort hanteringslösningen för Office 365 med hjälp av processen i [Ta bort en hanteringslösning](solutions.md#remove-a-monitoring-solution). Detta kommer inte att stoppa data som samlas in från Office 365 i Azure Monitor ändå. Följ proceduren nedan för att avsluta prenumerationen på Office 365 och sluta samla in data.

1. Spara följande skript som *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Kör skriptet med följande kommando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exempel:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Du kommer att uppmanas att ange autentiseringsuppgifter. Ange autentiseringsuppgifter för din Log Analytics-arbetsyta.

## <a name="data-collection"></a>Datainsamling

Det kan ta några timmar innan data samlas in från början. När det börjar samla in skickar Office 365 ett [webhook-meddelande](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) med detaljerade data till Azure Monitor varje gång en post skapas. Den här posten är tillgänglig i Azure Monitor inom några minuter efter att den tagits emot.

## <a name="using-the-solution"></a>Använda lösningen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

När du lägger till Office 365-lösningen på logganalysarbetsytan läggs **Office 365-panelen** till på instrumentpanelen. Den här panelen visar antal och en grafisk representation av antalet datorer i din miljö och deras uppdateringskompatibilitet.<br><br>
![Sammanfattningspanel för Office 365](media/solution-office-365/tile.png)  

Klicka på **Office 365-panelen** för att öppna Instrumentpanelen för **Office 365.**

![Instrumentpanelen i Office 365](media/solution-office-365/dashboard.png)  

Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn visar de tio bästa aviseringarna genom att räkna matchande den kolumnens villkor för det angivna scopet och tidsintervallet. Du kan köra en loggsökning som ger hela listan genom att klicka på Visa alla längst ned i kolumnen eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|:--|:--|
| Åtgärder | Innehåller information om aktiva användare från alla övervakade Office 365-prenumerationer. Du kommer också att kunna se antalet aktiviteter som sker över tiden.
| Exchange | Visar fördelningen av Exchange Server-aktiviteter som Behörighet för tilläggspostlåda eller Set-postlåda. |
| SharePoint | Visar de populäraste aktiviteterna som användarna utför i SharePoint-dokument. När du detaljgransar nedåt från den här panelen visar söksidan information om dessa aktiviteter, till exempel måldokumentet och platsen för den här aktiviteten. För en händelse i åtkomst till fil kan du till exempel se dokumentet som används, dess associerade kontonamn och IP-adress. |
| Azure Active Directory | Innehåller de vanligaste användaraktiviteterna, till exempel Återställ användarlösenord och inloggningsförsök. När du detaljgransar nedåt kan du se information om dessa aktiviteter som resultatstatus. Detta är till största till hjälp om du vill övervaka misstänkta aktiviteter i din Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Loggposter för Azure Monitor

Alla poster som skapas på log analytics-arbetsytan i Azure Monitor av Office 365-lösningen har en **typ** av **OfficeActivity**.  Egenskapen **OfficeWorkload** avgör vilken Office 365-tjänst posten refererar till - Exchange, AzureActiveDirectory, SharePoint eller OneDrive.  Egenskapen **RecordType** anger typen av åtgärd.  Egenskaperna varierar för varje operationstyp och visas i tabellerna nedan.

### <a name="common-properties"></a>Gemensamma egenskaper

Följande egenskaper är gemensamma för alla Office 365-poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ | *OfficeAktivitet* |
| KlientIP | IP-adressen för den enhet som användes när aktiviteten loggades. IP-adressen visas i IPv4- eller IPv6-adressformat. |
| OfficeWorkload (OfficeWorkload) | Office 365-tjänst som posten refererar till.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Åtgärd | Namnet på användarens eller administratörens aktivitet.  |
| OrganizationId | GUID för organisationens Office 365-klientorganisation. Det här värdet är alltid detsamma för din organisation, oavsett vilken Office 365-tjänst den utförs i. |
| RecordType | Typ av åtgärd som utförs. |
| ResultStatus | Anger om åtgärden (som anges i egenskapen Operation) lyckades eller inte. Möjliga värden lyckades, delvissucceed, eller Misslyckades. För Exchange-administratörsaktivitet är värdet antingen Sant eller Falskt. |
| UserId | UPN (Användarnamn) för den användare som utförde åtgärden som resulterade i att posten loggades. till exempel my_name@my_domain_name. Observera att poster för aktivitet som utförs av systemkonton (till exempel SHAREPOINT\system eller NTAUTHORITY\SYSTEM) också ingår. | 
| UserKey | Ett alternativt ID för användaren som identifieras i egenskapen UserId.  Den här egenskapen fylls till exempel med passunika ID (PUID) för händelser som utförs av användare i SharePoint, OneDrive för företag och Exchange. Den här egenskapen kan också ange samma värde som UserID-egenskapen för händelser som inträffar i andra tjänster och händelser som utförs av systemkonton|
| UserType | Den typ av användaren som utförde åtgärden.<br><br>Admin<br>Program<br>DcAdmin (olikartade)<br>Normal<br>Reserverad<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory-bas

Följande egenskaper är gemensamma för alla Azure Active Directory-poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ av Azure AD-händelse. |
| Utökade egenskaper | De utökade egenskaperna för Azure AD-händelsen. |


### <a name="azure-active-directory-account-logon"></a>Inloggning för Azure Active Directory-konto

Dessa poster skapas när en Active Directory-användare försöker logga in.

| Egenskap | Beskrivning |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Programmet som utlöser kontoinloggningshändelsen, till exempel Office 15. |
| `Client` | Information om klientenheten, enhetsoperativsystemet och enhetsbläddraren som användes för inloggningshändelsen för kontot. |
| `LoginStatus` | Boendet kommer från OrgIdLogon.LoginStatus direkt. Kartläggningen av olika intressanta inloggningsfel kan göras genom att varna algoritmer. |
| `UserDomain` | Klientidentitetsinformation (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Dessa poster skapas när ändringar eller tillägg görs i Azure Active Directory-objekt.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADMål | Användaren som åtgärden (identifieras av egenskapen Operation) utfördes på. |
| Skådespelare | Användarens eller tjänstens huvudnamn som utförde åtgärden. |
| ActorContextId | GUIDEN av organisationen som skådespelaren hör hemma till. |
| SkådespelareIpAddress | Aktörens IP-adress i IPV4- eller IPV6-adressformat. |
| InterSystemsId | GUID som spårar åtgärderna mellan komponenter i Office 365-tjänsten. |
| IntraSystemId |   GUID som genereras av Azure Active Directory för att spåra åtgärden. |
| SupportTicketId | Kundsupportbiljett-ID för åtgärden i "act-on-behalf-of"-situationer. |
| TargetContextId | GUID för den organisation som den riktade användaren tillhör. |


### <a name="data-center-security"></a>Säkerhet i datacenter

Dessa poster skapas från datacentersäkerhetsgranskningsdata.  

| Egenskap | Beskrivning |
|:--- |:--- |
| Effektivorganisation | Namnet på den klient som höjden/cmdleten var inriktad på. |
| HöjdApprovedTime | Tidsstämpeln för när höjden godkändes. |
| HöjdApprover | Namnet på en Microsoft-chef. |
| HöjdUndersättning | Den varaktighet som höjden var aktiv. |
| HöjdRequestId |  En unik identifierare för höjdbegäran. |
| HöjdRole | Rollen som höjden begärdes för. |
| Höjdtid | Höjdens starttid. |
| Start_Time | Starttiden för cmdlet-körningen. |


### <a name="exchange-admin"></a>Exchange-administratör

Dessa poster skapas när ändringar görs i Exchange-konfigurationen.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | Exchange |
| RecordType     | ExchangeAdmin |
| Externtaccess |  Anger om cmdleten kördes av en användare i organisationen, av Microsofts datacenterpersonal eller ett datacentertjänstkonto eller av en delegerad administratör. Värdet False anger att cmdleten kördes av någon i organisationen. Värdet True anger att cmdleten kördes av datacenterpersonal, ett datacentertjänstkonto eller en delegerad administratör. |
| ModifiedObjectResolvedName |  Det här är det användarvänliga namnet på objektet som har ändrats av cmdleten. Detta loggas bara om cmdleten ändrar objektet. |
| OrganizationName | Namnet på hyresgästen. |
| Ursprungsserver | Namnet på den server som cmdleten utfördes från. |
| Parametrar | Namnet och värdet för alla parametrar som användes med cmdlet som identifieras i egenskapen Operations. |


### <a name="exchange-mailbox"></a>Exchange-postlåda

Dessa poster skapas när ändringar eller tillägg görs i Exchange-postlådor.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | Exchange |
| RecordType     | ExchangeItem (ExchangeItem) |
| ClientInfoString | Information om e-postklienten som användes för att utföra åtgärden, till exempel en webbläsarversion, Outlook-version och information om mobila enheter. |
| Client_IPAddress | IP-adressen för den enhet som användes när åtgärden loggades. IP-adressen visas i IPv4- eller IPv6-adressformat. |
| ClientMachineName | Datornamnet som är värd för Outlook-klienten. |
| Klientprocessnamn | E-postklienten som användes för att komma åt postlådan. |
| KlientVersion | Versionen av e-postklienten . |
| InternalLogonType | Reserverad för internt bruk. |
| Logon_Type | Anger vilken typ av användare som kom åt postlådan och utförde åtgärden som loggades. |
| InloggningUserDisplayName |    Det användarvänliga namnet på den användare som utförde åtgärden. |
| LogonUserSid | SID för den användare som utförde åtgärden. |
| MailboxGuid (Brevlåda) | Exchange GUID för den postlåda som användes. |
| MailboxOwnerMasterAccountSid | Postlådeägarens kontos huvudkonto SID. |
| MailboxOwnerSid | Sid för postlådans ägare. |
| MailboxOwnerUPN | E-postadressen till den person som äger postlådan som användes. |


### <a name="exchange-mailbox-audit"></a>Granskning av Exchange-postlåda

Dessa poster skapas när en postlådegranskningspost skapas.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | Exchange |
| RecordType     | ExchangeItem (ExchangeItem) |
| Objekt | Representerar artikeln som operationen utfördes på | 
| SkickaAsUserMailboxGuid | Exchange GUID för postlådan som användes för att skicka e-post som. |
| SendAsUserSmtp | SMTP-adress för den användare som personifieras. |
| SendonBehalfOfUserMailboxGuid | Exchange GUID för postlådan som användes för att skicka e-post på uppdrag av. |
| SendOnBehalfOfUserSmtp | SMTP-adress för den användare för vars räkning e-postmeddelandet skickas. |


### <a name="exchange-mailbox-audit-group"></a>Granskningsgrupp för Exchange-postlåda

Dessa poster skapas när ändringar eller tillägg görs i Exchange-grupper.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | Exchange |
| OfficeWorkload (OfficeWorkload) | ExchangeItemGroup |
| Berördaobjekt | Information om varje objekt i gruppen. |
| CrossMailboxOperations | Anger om åtgärden omfattade mer än en postlåda. |
| DestMailboxId | Ange endast om parametern CrossMailboxOperations är True. Anger GUID för målpostlådan. |
| DestMailboxOwnerMasterAccountSid | Ange endast om parametern CrossMailboxOperations är True. Anger SID för huvudkontot SID för målpostlådeägaren. |
| DestMailboxOwnerSid | Ange endast om parametern CrossMailboxOperations är True. Anger SID för målpostlådan. |
| DestMailboxOwnerUPN | Ange endast om parametern CrossMailboxOperations är True. Anger UPN för ägaren till målpostlådan. |
| DestFolder (olika gånger) | Målmappen för åtgärder som Flytta. |
| Mapp | Mappen där en grupp objekt finns. |
| Mappar |     Information om källmapparna som ingår i en åtgärd. till exempel om mappar är markerade och sedan tas bort. |


### <a name="sharepoint-base"></a>SharePoint-bas

Dessa egenskaper är gemensamma för alla SharePoint-poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | SharePoint |
| OfficeWorkload (OfficeWorkload) | SharePoint |
| EventSource (EventSource) | Identifierar att en händelse inträffade i SharePoint. Möjliga värden är SharePoint eller ObjectModel. |
| ItemType | Den typ av objekt som har åtkomst eller ändrats. I tabellen ItemType finns information om vilka typer av objekt som finns. |
| MaskinDomäninInfo | Information om enhetssynkroniseringsåtgärder. Den här informationen rapporteras endast om den finns i begäran. |
| MachineId (maskin) |   Information om enhetssynkroniseringsåtgärder. Den här informationen rapporteras endast om den finns i begäran. |
| Site_ | GUID för den plats där filen eller mappen nås av användaren finns. |
| Source_Name | Entiteten som utlöste den granskade åtgärden. Möjliga värden är SharePoint eller ObjectModel. |
| Useragent | Information om användarens klient eller webbläsare. Denna information tillhandahålls av klienten eller webbläsaren. |


### <a name="sharepoint-schema"></a>SharePoint-schema

Dessa poster skapas när konfigurationsändringar görs i SharePoint.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | SharePoint |
| OfficeWorkload (OfficeWorkload) | SharePoint |
| CustomEvent | Valfri sträng för anpassade händelser. |
| Event_Data |  Valfri nyttolast för anpassade händelser. |
| ModifiedProperties | Egenskapen ingår för administratörshändelser, till exempel att lägga till en användare som medlem i en webbplats eller en administratörsgrupp för webbplatssamlingen. Egenskapen innehåller namnet på egenskapen som har ändrats (till exempel gruppen Webbplatsadministratör), det nya värdet för den ändrade egenskapen (till exempel användaren som lades till som webbplatsadministratör) och det tidigare värdet för det ändrade objektet. |


### <a name="sharepoint-file-operations"></a>SharePoint-filåtgärder

Dessa poster skapas som svar på filåtgärder i SharePoint.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload (OfficeWorkload) | SharePoint |
| OfficeWorkload (OfficeWorkload) | SharePointFileOperation |
| DestinationFileExtension | Filtillägget för en fil som kopieras eller flyttas. Den här egenskapen visas endast för FileCopied- och FileMoved-händelser. |
| DestinationFileName | Namnet på filen som kopieras eller flyttas. Den här egenskapen visas endast för FileCopied- och FileMoved-händelser. |
| DestinationRelativeUrl | URL:en för målmappen där en fil kopieras eller flyttas. Kombinationen av värdena för parametrarna SiteURL, DestinationRelativeURL och DestinationFileName är samma som värdet för egenskapen ObjectID, som är det fullständiga sökvägsnamnet för filen som kopierades. Den här egenskapen visas endast för FileCopied- och FileMoved-händelser. |
| SharingType (Olikartade) | Den typ av delningsbehörigheter som har tilldelats användaren som resursen delades med. Den här användaren identifieras av parametern UserSharedWith. |
| Site_Url | URL:en för den plats där filen eller mappen som användaren har åtkomst till finns. |
| KällaFileExtension | Filtillägget för filen som användes av användaren. Den här egenskapen är tom om objektet som användes är en mapp. |
| SourceFileName |  Namnet på den fil eller mapp som användaren har åtkomst till. |
| KällaRelativeUrl | URL:en för mappen som innehåller den fil som användaren har åtkomst till. Kombinationen av värdena för parametrarna SiteURL, SourceRelativeURL och SourceFileName är samma som värdet för egenskapen ObjectID, som är det fullständiga sökvägsnamnet för den fil som användaren använder. |
| UserSharedWith |  Användaren som en resurs delades med. |




## <a name="sample-log-queries"></a>Exempel på loggfrågor

I följande tabell finns exempelloggfrågor för uppdateringsposter som samlas in av den här lösningen.

| Söka i data | Beskrivning |
| --- | --- |
|Räkna alla åtgärder på din Office 365-prenumeration |OfficeActivity &#124; sammanfattar antal() efter åtgärd |
|Användning av SharePoint-webbplatser|OfficeActivity &#124; där OfficeWorkload =~ "sharepoint" &#124; sammanfattar antal() \| efter SiteUrl sortera efter antal asc|
|Filåtkomståtgärder efter användartyp | OfficeActivity &#124; sammanfattar antal() efter UserType |
|Övervaka externa åtgärder på Exchange|OfficeActivity &#124; där OfficeWorkload =~ "exchange" och ExternalAccess == true|



## <a name="next-steps"></a>Nästa steg

* Använd [loggfrågor i Azure Monitor](../log-query/log-query-overview.md) för att visa detaljerade uppdateringsdata.
* [Skapa egna instrumentpaneler](../learn/tutorial-logs-dashboards.md) för att visa dina favoritsökningsfrågor för Office 365.
* [Skapa aviseringar som](../platform/alerts-overview.md) proaktivt ska meddelas om viktiga Office 365-aktiviteter.  
