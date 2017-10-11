---
title: "Office 365-lösningen i Operations Management Suite (OMS) | Microsoft Docs"
description: "Den här artikeln innehåller information om konfiguration och användning av Office 365-lösningen i OMS.  Den innehåller en detaljerad beskrivning av Office 365-poster som skapats i logganalys."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: dcc44986acbb76eafc3cfacb79acf237802de021
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="office-365-solution-in-operations-management-suite-oms"></a>Office 365-lösningen i Operations Management Suite (OMS)

![Office 365-logotyp](media/oms-solution-office-365/icon.png)

Office 365-lösningen för Operations Management Suite (OMS) kan du övervaka din Office 365-miljö i logganalys.  

- Övervaka användaraktiviteter på Office 365-konton för att analysera användningsmönster samt identifiera beteendebaserade trender. Du kan exempelvis extrahera av specifika Användningsscenarier, till exempel filer som delas utanför din organisation eller populäraste SharePoint-webbplatser.
- Övervaka aktiviteter för administratören att spåra konfigurationsändringar eller Privilegierade åtgärder.
- Identifiera och undersöka oönskade användarbeteende som kan anpassas efter organisationens behov.
- Visa granskning och kompatibilitet. Exempelvis kan du övervaka åtkomståtgärder på konfidentiella filer som kan hjälpa dig med processen gransknings- och efterlevnad.
- Utföra operativa felsökning med hjälp av OMS-sökning på Office 365 aktivitetsdata i din organisation.

## <a name="prerequisites"></a>Krav
Följande krävs innan den här lösningen som ska installeras och konfigureras.

- Organisationens prenumeration på Office 365.
- Autentiseringsuppgifterna för ett konto som är en Global administratör.
- För att ta emot granskningsdata, måste du [konfigurera granskning](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) i Office 365-prenumeration.  Observera att [postlåda granskning](https://technet.microsoft.com/library/dn879651.aspx) konfigureras separat.  Du kan fortfarande installera lösningen och samla in andra data om granskning inte har konfigurerats.
 


## <a name="management-packs"></a>Hanteringspaket
Den här lösningen installerar inte några management packs i anslutna hanteringsgrupper.
  

## <a name="configuration"></a>Konfiguration
När du [lägga till Office 365-lösningen till din prenumeration](../log-analytics/log-analytics-add-solutions.md), måste du ansluta till Office 365-prenumeration.

1. Lägg till avisering hanteringslösningen i OMS-arbetsytan med processen som beskrivs i [lägga till lösningar](../log-analytics/log-analytics-add-solutions.md).
2. Gå till **inställningar** i OMS-portalen.
3. Under **anslutna källor**väljer **Office 365**.
4. Klicka på **ansluta Office 365**.<br>![Koppla de olika processtegen Office 365](media/oms-solution-office-365/configure.png)
5. Logga in på Office 365 med ett konto som är en Global administratör för din prenumeration. 
6. Prenumerationen visas med arbetsbelastningar som övervakar lösningen.<br>![Koppla de olika processtegen Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Datainsamling
### <a name="supported-agents"></a>Agenter som stöds
Office 365-lösningen inte hämta data från någon av de [OMS agenter](../log-analytics/log-analytics-data-sources.md).  Den hämtar data direkt från Office 365.

### <a name="collection-frequency"></a>Insamlingsfrekvens
Office 365 skickar en [webhook meddelande](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) med detaljerad information till logganalys varje gång en post har skapats.

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till Office 365-lösningen till OMS-arbetsyta i **Office 365** panelen kommer att läggas till OMS-instrumentpanelen. Den här panelen visar antal och en grafisk representation av antalet datorer i din miljö och deras uppdateringskompatibilitet.<br><br>
![Panelen för Office 365-sammanfattning](media/oms-solution-office-365/tile.png)  

Klicka på den **Office 365** öppna den **Office 365** instrumentpanelen.

![Office 365-instrumentpanelen](media/oms-solution-office-365/dashboard.png)  

Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumnen visar de tio översta aviseringarna efter antal som matchar den kolumnen villkoren för angivet omfång och tidsintervall. Du kan köra en sökning i logg som innehåller hela listan genom att klicka på se alla längst ned i kolumnen eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|:--|:--|
| Åtgärder | Innehåller information om de aktiva användarna från alla övervakade Office 365-prenumerationer. Du kan också se antalet aktiviteter som sker över tid.
| Exchange | Visar fördelningen av Exchange Server-aktiviteter, till exempel Lägg till postlåda behörighet eller Set-postlåda. |
| SharePoint | Visar de översta aktiviteterna att användare utför på SharePoint-dokument. När du går nedåt från den här panelen visar sidan Sök efter information om dessa aktiviteter, till exempel måldokumentet och platsen för den här aktiviteten. Till exempel en komma åt filen händelse du kommer att kunna se dokumentet som används, associerade kontonamn och IP-adress. |
| Azure Active Directory | Innehåller översta användaraktiviteter, t.ex återställa användarlösenord och inloggningsförsök. När du går nedåt kommer du att kunna se detaljer för dessa aktiviteter som resultat. Detta är mest användbart om du vill övervaka misstänkta aktiviteter på Azure Active Directory. |




## <a name="log-analytics-records"></a>Log Analytics-poster

Alla poster som skapats i logganalys-arbetsytan med Office 365-lösningen har en **typen** av **OfficeActivity**.  Den **OfficeWorkload** egenskapen avgör vilken Office 365-tjänst som posten refererar till - Exchange, AzureActiveDirectory, SharePoint eller OneDrive.  Den **RecordType** egenskapen anger typ av åtgärd.  Egenskaperna varierar för varje åtgärdstyp av och visas i tabellerna nedan.

### <a name="common-properties"></a>Gemensamma egenskaper
Följande egenskaper är gemensamma för alla Office 365-poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ | *OfficeActivity* |
| ClientIP | IP-adressen på den enhet som användes när aktiviteten har loggats. IP-adressen visas i en IPv4- eller IPv6-adress-format. |
| OfficeWorkload | Office 365-tjänst som posten refererar till.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Åtgärd | Namnet på användaren eller administratören aktiviteten.  |
| Organisations-ID | GUID för Office 365-klient för din organisation. Det här värdet kommer alltid att samma för din organisation, oavsett tjänsten Office 365 den uppstår. |
| RecordType | Typ av av åtgärd utförs. |
| ResultStatus | Anger om åtgärden (anges i egenskapen åtgärden) lyckades eller inte. Möjliga värden är slutfört, PartiallySucceded eller misslyckades. Värdet är för Exchange-administratörsaktivitet, antingen SANT eller FALSKT. |
| Användar-ID | UPN (User Principal Name) för användaren som utförde åtgärden resulterade i posten loggas; till exempel my_name@my_domain_name. Observera att posterna för aktivitet som utförs av Systemkonton (till exempel SHAREPOINT\system eller NTAUTHORITY\SYSTEM) ingår också. | 
| UserKey | Ett alternativt ID för den användare som identifieras i användar-ID-egenskapen.  Den här egenskapen är fylls i med passport unikt ID (PUID) för händelser som utförs av användare i SharePoint, OneDrive för företag och Exchange. Den här egenskapen kan också ange samma värde som egenskapen användar-ID för händelser i andra tjänster och händelser som utförs av systemkonton|
| UserType | Typ av användaren som utförde åtgärden.<br><br>Admin<br>Program<br>DcAdmin<br>Vanliga<br>Reserverad<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory-bas
Följande egenskaper är gemensamma för alla poster i Azure Active Directory.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ av Azure AD-händelse. |
| extendedProperties | De utökade egenskaperna för Azure AD-händelse. |


### <a name="azure-active-directory-account-logon"></a>Inloggning med Azure Active Directory-konto
Dessa poster skapas när Active Directory-användare försöker logga in.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Program | Programmet som utlöser händelsen konto inloggning, till exempel Office 15. |
| Client | Information om klienten enhet, enhetens operativsystem och enhetens webbläsare som användes för den i händelsen konto inloggningen. |
| loginStatus | Den här egenskapen är direkt från OrgIdLogon.LoginStatus. Mappningen av olika intressanta inloggningsfel kan göras av aviseringar algoritmer. |
| UserDomain | Identitetsinformation klient (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Dessa poster skapas när ändringen eller tillägg görs i Azure Active Directory-objekt.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Användaren som åtgärden (som identifieras av egenskapen åtgärden) utfördes på. |
| aktören | Användaren eller tjänstens huvudnamn som utförde åtgärden. |
| ActorContextId | GUID för den organisation som aktören tillhör. |
| ActorIpAddress | Skådespelare, IP-adress i IPV4- eller IPV6-adress-format. |
| InterSystemsId | GUID som spårar åtgärder mellan komponenter i tjänsten Office 365. |
| IntraSystemId |   GUID som genereras av Azure Active Directory för att spåra åtgärden. |
| SupportTicketId | Biljett-ID kundsupport för åtgärden i ”act-on-behalf-of” situationer. |
| TargetContextId | GUID för den organisation som den aktuella användaren tillhör. |


### <a name="data-center-security"></a>Datacenter-säkerhet
Dessa poster har skapats från granskningsdata Center datasäkerhet.  

| Egenskap | Beskrivning |
|:--- |:--- |
| EffectiveOrganization | Namnet på den klient som höjning/cmdlet är riktad mot. |
| ElevationApprovedTime | Tidsstämpel för när höjningen har godkänts. |
| ElevationApprover | Namnet på en Microsoft-hanterare. |
| ElevationDuration | Varaktigheten höjningen var aktiv. |
| ElevationRequestId |  En unik identifierare för höjning begäran. |
| ElevationRole | Rollen höjningen mottog en begäran för. |
| ElevationTime | Starttid för höjningen. |
| Starttid | Starttiden för den cmdlet som körs. |


### <a name="exchange-admin"></a>Exchange-administratören
Dessa poster skapas när ändringar görs i Exchange-konfiguration.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Anger om cmdleten kördes av en användare i din organisation, av Microsoft datacenter-personal eller ett tjänstkonto för datacenter eller av en delegerad administratör. Värdet falskt anger att cmdleten kördes av någon i din organisation. Värdet True anger att cmdleten kördes av datacenter personal, ett tjänstkonto för datacenter eller en delegerad administratör. |
| ModifiedObjectResolvedName |  Detta är egna namnet på det objekt som har ändrats av cmdlet. Detta loggas endast om cmdlet ändrar objektet. |
| Organisationsnamn | Namn för innehavaren. |
| OriginatingServer | Namnet på den server där cmdleten kördes. |
| Parametrar | Namn och värde för alla parametrar som användes med den cmdlet som identifieras i Operations-egenskapen. |


### <a name="exchange-mailbox"></a>Exchange-postlåda
Dessa poster skapas när ändringar eller tillägg som görs till Exchange-postlådor.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Information om e-postklienten som användes för att utföra åtgärden, till exempel en webbläsarversion, Outlook-versionen och information för mobila enheter. |
| Client_IPAddress | IP-adressen på den enhet som användes när åtgärden har loggats. IP-adressen visas i en IPv4- eller IPv6-adress-format. |
| ClientMachineName | Namnet på den dator som är värd för Outlook-klienten. |
| ClientProcessName | E-postklienten som användes för att komma åt postlådan. |
| ClientVersion | Versionen av e-postklienten. |
| InternalLogonType | Reserverat för intern användning. |
| Logon_Type | Anger vilken typ av användare som nås postlådan och utföra åtgärden som loggades. |
| LogonUserDisplayName |    Användarvänligt namn för användaren som utförde åtgärden. |
| LogonUserSid | SID för användaren som utförde åtgärden. |
| MailboxGuid | Exchange-GUID för postlådan som öppnade. |
| MailboxOwnerMasterAccountSid | Postlåda ägare konto master kontots SID. |
| MailboxOwnerSid | SID för postlådans ägare. |
| MailboxOwnerUPN | Den person som äger den postlåda som har åtkomst till e-postadress. |


### <a name="exchange-mailbox-audit"></a>Exchange-postlåda granskning
Dessa poster skapas när en postlåda granskningspost skapas.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Objekt | Representerar det objektet som åtgärden utfördes | 
| SendAsUserMailboxGuid | GUID för postlådan som användes för att skicka e-postmeddelande som Exchange. |
| SendAsUserSmtp | SMTP-adress för den användare som har personifierats. |
| SendonBehalfOfUserMailboxGuid | GUID för den postlåda som användes för att skicka e-post för Exchange. |
| SendOnBehalfOfUserSmtp | SMTP-adress för den användare som e-postmeddelandet skickas. |


### <a name="exchange-mailbox-audit-group"></a>Exchange-postlåda Audit grupp
Dessa poster skapas när ändringar eller tillägg som görs till Exchange-grupper.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Information om varje objekt i gruppen. |
| CrossMailboxOperations | Anger om åtgärden involverad mer än en postlåda. |
| DestMailboxId | Ange endast om parametern CrossMailboxOperations är True. Anger målet postlådan GUID. |
| DestMailboxOwnerMasterAccountSid | Ange endast om parametern CrossMailboxOperations är True. Anger SID för master kontots SID för målet postlådans ägare. |
| DestMailboxOwnerSid | Ange endast om parametern CrossMailboxOperations är True. Anger SID för mål-postlådan. |
| DestMailboxOwnerUPN | Ange endast om parametern CrossMailboxOperations är True. Anger UPN för ägaren av mål-postlåda. |
| DestFolder | Målmapp för åtgärder som till exempel flytta. |
| Mapp | Den mapp där det finns en grupp med objekt. |
| Mappar |     Information om mapparna källa ingår i en funktion. Om exempelvis mappar valts och sedan ta bort. |


### <a name="sharepoint-base"></a>SharePoint-bas
Dessa egenskaper är gemensamma för alla SharePoint-poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| eventSource | Identifierar att en händelse har inträffat i SharePoint. Möjliga värden är SharePoint eller ObjectModel. |
| itemType | Typ av objekt som har åtkomst till eller ändrats. Se tabellen ItemType mer information om vilka typer av objekt. |
| MachineDomainInfo | Information om enheten synkroniseringsåtgärder. Denna information rapporteras endast om den finns i begäran. |
| MachineId |   Information om enheten synkroniseringsåtgärder. Denna information rapporteras endast om den finns i begäran. |
| Site_ | GUID för den plats där filen eller mappen som används av användaren finns. |
| Source_Name | Den enhet som utlöste åtgärden granskad. Möjliga värden är SharePoint eller ObjectModel. |
| UserAgent | Information om användarens klienten eller webbläsare. Denna information tillhandahålls av klienten eller webbläsare. |


### <a name="sharepoint-schema"></a>SharePoint-Schema
Dessa poster skapas när konfigurationsändringar görs i SharePoint.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Valfri sträng för anpassade händelser. |
| Event_Data |  Valfria nyttolasten för anpassade händelser. |
| ModifiedProperties | Egenskapen ingår för admin-händelser, till exempel lägga till en användare som en medlem av en webbplats eller en samling administratörsgrupp. Egenskapen innehåller namnet på egenskapen som har ändrats (t.ex, platsen administratörsgruppen), det nya värdet för egenskapen ändrade (sådana den användare som har lagts till som en plats-administratör) och det tidigare värdet för det ändrade objektet. |


### <a name="sharepoint-file-operations"></a>SharePoint-filåtgärder
Dessa poster skapas som svar på filåtgärder i SharePoint.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Filnamnstillägget för en fil som kopieras eller flyttas. Den här egenskapen visas endast för FileCopied och FileMoved händelser. |
| DestinationFileName | Namnet på den fil som kopieras eller flyttas. Den här egenskapen visas endast för FileCopied och FileMoved händelser. |
| DestinationRelativeUrl | URL till målmappen där en fil kopieras eller flyttas. Kombinationen av värden för parametrarna SiteURL, DestinationRelativeURL och DestinationFileName är detsamma som värdet för egenskapen ObjectID som är den fullständiga sökvägen för filen som har kopierats. Den här egenskapen visas endast för FileCopied och FileMoved händelser. |
| SharingType | Typ av behörighet som har tilldelats som resursen har delat med användaren. Den här användaren identifieras med parametern UserSharedWith. |
| Site_Url | URL till den plats där filen eller mappen som används av användaren finns. |
| SourceFileExtension | Filnamnstillägget för den fil som användes av användaren. Den här egenskapen är tom om det objekt som användes är en mapp. |
| SourceFileName |  Namnet på filen eller mappen som används av användaren. |
| SourceRelativeUrl | URL till den mapp som innehåller filen som används av användaren. Kombinationen av värden för parametrarna SiteURL, SourceRelativeURL och SourceFileName är detsamma som värdet för egenskapen ObjectID som är den fullständiga sökvägen för filen som används av användaren. |
| UserSharedWith |  Användaren som en resurs har delat med. |




## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel på sökningar i loggen för uppdateringsposter som har samlats in av den här lösningen.

| Fråga | Beskrivning |
| --- | --- |
|Antalet för alla åtgärder på Office 365-prenumeration |' Type = OfficeActivity | mäter count() för åtgärden ' |
|Användning av SharePoint-webbplatser|' Type = OfficeActivity OfficeWorkload = sharepoint | måttet count() som antal av SiteUrl | Sortera antal asc'|
|Filåtgärder för åtkomst av typ|' Type = OfficeActivity OfficeWorkload = sharepoint åtgärden = FileAccessed | mäter count() av UserType'|
|Söka med ett specifikt nyckelord|`Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"`|
|Övervaka externa åtgärder i Exchange|`Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true`|



## <a name="troubleshooting"></a>Felsökning

Om din Office 365-lösningen inte samlar in data som förväntat, kontrollera statusen i OMS-portalen på **inställningar** -> **anslutna källor** -> **Office 365**. I följande tabell beskrivs varje status.

| Status | Beskrivning |
|:--|:--|
| Active | Office 365-prenumeration är aktiv och arbetsbelastningen är ansluten till din OMS-arbetsyta. |
| Väntande åtgärder | Office 365-prenumeration är aktiv men arbetsbelastningen är ännu inte ansluten till OMS-arbetsytan har. Första gången du ansluter till Office 365-prenumeration är alla arbetsbelastningar inte på den här statusen förrän de har anslutits. Vänta 24 timmar för alla arbetsbelastningar för att växla till aktiv. |
| Inaktiva | Office 365-prenumeration är i ett inaktivt tillstånd. Kontrollera din Office 365 Admin information på sidan. När du har aktiverat din Office 365-prenumeration Avlänka från din OMS-arbetsyta och länka det igen för att börja ta emot data. |



## <a name="next-steps"></a>Nästa steg
* Använd loggsökningar i [Log Analytics](../log-analytics/log-analytics-log-searches.md) för att visa detaljerad uppdateringsinformation.
* [Skapa dina egna instrumentpaneler](../log-analytics/log-analytics-dashboards.md) att visa dina Favoriter Office 365-sökningar.
* [Skapa aviseringar](../log-analytics/log-analytics-alerts.md) ska meddelas proaktivt om viktiga Office 365-aktiviteter.  
