---
title: Identifiera problem med Windows Virtual Desktop förhandsversion diagnostikfunktion - Azure
description: Beskriver Windows Virtual Desktop förhandsversion diagnostikfunktion och hur du använder den.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6b79a26d63c02dd06b62ea6ad09941f947704dc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870497"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identifiera problem med diagnostikfunktionen

Windows Virtual Desktop förhandsversionen erbjuder en diagnostikfunktion som administratören kan identifiera problem med hjälp av ett enda gränssnitt. Roller för virtuella Windows-skrivbordet logga en diagnostisk aktivitet när en användare interagerar med systemet. Varje logg innehåller relevant information, till exempel de som ingår i transaktionen, felmeddelanden, klientinformation och användarinformation rollerna för virtuella Windows-skrivbordet. Diagnostiska aktiviteter skapas av både slutanvändare och administrativa åtgärder och kan vara indelade i tre huvudsakliga buckets:

* Feed prenumeration aktiviteter: slutanvändaren utlöser aktiviteterna när de försöker ansluta till sina feed via Microsoft Remote Desktop program.
* Aktiviteter för anslutning: slutanvändaren utlöser aktiviteterna när de försöker ansluta till en stationär eller RemoteApp via Microsoft Remote Desktop program.
* Hanteringsaktiviteter: administratören utlöser aktiviteterna när de utföra hanteringsåtgärder på systemet, till exempel skapa pooler för värden, tilldela användare till app-grupper och skapa rolltilldelningar.
  
Anslutningar som inte når virtuella Windows-skrivbordet visas inte i Diagnostikresultat eftersom rolltjänsten diagnostik själva är en del av virtuella Windows-skrivbordet. Virtuella Windows-skrivbordet anslutningsproblem kan inträffa när slutanvändaren upplever problem med nätverksanslutningen.

Du kommer igång [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

## <a name="diagnose-issues-with-powershell"></a>Diagnostisera problem med PowerShell

Diagnostik för virtuella skrivbord i Windows använder bara en PowerShell-cmdleten, men innehåller många valfria parametrar för att hjälpa att begränsa och isolera problem. Följande avsnitt listar de cmdletar som du kan köra för att diagnostisera problem. De flesta filter kan användas tillsammans. Värden som visas inom parentes, till exempel `<tenantName>`, ska ersättas med de värden som gäller för din situation.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Hämta diagnostiska aktiviteter i din klient

Du kan hämta diagnostiska aktiviteter genom att ange den **Get-RdsDiagnosticActivities** cmdlet. Följande exempel-cmdlet returnerar en lista över diagnostiska aktiviteter, sorteras de mest minst nyligen gjorda.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Som andra Windows Virtual Desktop PowerShell-cmdletar, måste du använda den **- TenantName** parametern för att ange namnet på den klient som du vill använda för din fråga. Innehavarens namn kan användas för nästan alla diagnostiska aktivitet frågor.

### <a name="retrieve-detailed-diagnostic-activities"></a>Hämta detaljerad diagnostisk aktiviteter

Den **-detaljerad** parametern innehåller ytterligare information för varje diagnostiska aktivitet som returneras. Formatet för varje aktivitet varierar beroende på dess aktivitetstyp. Den **-detaljerad** parametern kan läggas till någon **Get-RdsDiagnosticActivities** fråga som visas i följande exempel.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Hämta en specifik diagnostiska aktivitet av aktivitets-ID

Den **- ActivityId** parametern returnerar ett visst diagnostiska aktivitet om den finns, som visas i följande exempel-cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrera diagnostiska aktiviteter av användaren

Den **- UserName** parametern returnerar en lista över diagnostiska aktiviteter som initierats av den angivna användaren som visas i följande exempel-cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Den **- UserName** parametern kan också kombineras med andra valfria parametrar för filtrering.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrera diagnostiska aktiviteter efter tid

Du kan filtrera aktivitetslistan över returnerade diagnostiska med den **- StartTime** och **- EndTime** parametrar. Den **- StartTime** parametern returnerar en diagnostisk Aktivitetslista med början från ett visst datum som visas i följande exempel.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

Den **- EndTime** parametern kan läggas till i en cmdlet med den **- StartTime** parametern för att ange en viss tidsperiod som du vill ta emot resultaten. Följande exempel-cmdlet returnerar en lista över diagnostiska aktiviteter finns mellan 1 augusti och den 10.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Den **- StartTime** och **- EndTime** parametrar kan också kombineras med andra valfria parametrar för filtrering.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrera diagnostiska aktiviteter med hjälp av aktivitetstyp

Du kan också filtrera diagnostiska aktiviteter med hjälp av aktivitetstyp med den **- ActivityType** parametern. Följande cmdlet returnerar en lista över anslutningar för slutanvändare:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

Följande cmdlet returnerar en lista över administratörsåtgärder för hantering:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Den **Get-RdsDiagnosticActivities** cmdlet för närvarande inte stöd att ange flöde som ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrera diagnostiska aktiviteter efter resultat

Du kan filtrera aktivitetslistan med returnerade diagnostiska efter resultatet med de **-resultatet** parametern. Följande exempel-cmdlet returnerar en lista över lyckade diagnostiska aktiviteter.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

Följande exempel-cmdlet returnerar en lista över misslyckade diagnostiska aktiviteter.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Den **-resultatet** parametern kan också kombineras med andra valfria parametrar för filtrering.

## <a name="common-error-scenarios"></a>Vanliga fel-scenarier

Fel vid scenarier kategoriseras i interna till tjänsten och externa till virtuella Windows-skrivbordet.

* Internt fel: Anger scenarier som inte kan hanteras av klientadministratören och måste lösas som supportärende. När höja en biljett Ange aktivitets-ID, klientnamn och ungefärliga tidsramen problemet uppstod.
* Externa problem: avse scenarier som kan undvikas av systemadministratören. Det här är externa för virtuella Windows-skrivbordet.

I följande tabell listas vanliga fel som dina administratörer stöta på.

>[!NOTE]
>Den här förhandsversionen innehåller inte en fullständig kategorisering av fel och kommer att uppdateras regelbundet. Om du vill kontrollera att du har den senaste informationen, måste du kontrollera tillbaka på den här artikeln minst en gång i månaden.

### <a name="external-management-error-codes"></a>Felkoder för extern hanteringsserver

|Numerisk kod|Felkod|Föreslagen lösning|
|---|---|---|
|3|UnauthorizedAccess|Den användare som försökte köra administrativa PowerShell-cmdleten har inte behörighet att göra det eller felstavad deras användarnamn.|
|1000|TenantNotFound|Innehavarens namn du angav matchar inte några befintliga klienter. Granska innehavarens namn för stavfel och försök igen.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Du kan inte ta bort en klient så länge den innehåller objekt. Ta bort sessionen värd pooler först och försök igen.|
|2000|HostPoolNotFound|Värdnamn för poolen som du har angett matchar inte någon befintlig värd-pooler. Granska pool värdnamnet för stavfel och försök igen.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Du kan inte ta bort poolen värd så länge som den innehåller objekt. Ta bort alla app-grupper i poolen värden först.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Ta bort alla sessioner värdar först innan tar bort session host-pool.|
|5001|SessionHostNotFound|Värd för fjärrskrivbordssession som du har frågor kan vara offline. Kontrollera statusen för värd-poolen.|
|5008|SessionHostUserSessionsExist |Du måste logga ut alla användare på värd för fjärrskrivbordssession innan du kör din avsedda aktivitet.|
|6000|AppGroupNotFound|Namnet på appen du har angett matchar inte några befintliga appgrupper. Granska namnet på appen för stavfel och försök igen.|
|6022|RemoteAppNotFound|RemoteApp-namnet som du angav matchar inte någon RemoteApps. Granska RemoteApp-namn för stavfel och försök igen.|
|6010|PublishedItemsExist|Namnet på den resurs som du försöker publicera är samma som en resurs som redan finns. Ändra resursnamnet och försök igen.|
|7002|NameNotValidWhiteSpace|Använd inte blanksteg i namnet.|
|8000|InvalidAuthorizationRoleScope|Rollnamnet du angav matchar inte någon befintlig rollnamn. Granska rollnamn för stavfel och försök igen. |
|8001|UserNotFound |Det angivna användarnamnet matchar inte något befintligt användarnamn. Granska namn för stavfel och försök igen.|
|8005|UserNotFoundInAAD |Det angivna användarnamnet matchar inte något befintligt användarnamn. Granska namn för stavfel och försök igen.|
|8008|TenantConsentRequired|Följ instruktionerna [här](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) att ge medgivande för din klient.|

### <a name="external-connection-error-codes"></a>Felkoder för extern anslutning

|Numerisk kod|Felkod|Föreslagen lösning|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Värd för fjärrskrivbordssession är inte korrekt ansluten till Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Anslutningarna misslyckades eftersom sessionen värden är tillgänglig. Kontrollera hälsotillståndet för värd för fjärrskrivbordssession.|
|-2146233088|ConnectionFailedClientDisconnect|Om du ser det här felet ofta kan du kontrollera att datorn är ansluten till nätverket.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Värd-användare har försökt att ansluta till sessionen är inte felfri. Felsöka den virtuella datorn.|
|-2146233088|ConnectionFailedUserNotAuthorized|Användaren har inte behörighet att komma åt den publicerade appen eller desktop. Felet kan visas när administratören bort publicerade resurser. Be användaren att uppdatera flödet i programmet fjärrskrivbord.|
|2|FileNotFound|Användaren försökte komma åt programmet är antingen felaktigt installerade eller inställd på en felaktig sökväg.|
|3|InvalidCredentials|Användarnamnet eller lösenordet du angav matchar inte någon befintlig användarnamn eller lösenord. Granska autentiseringsuppgifterna för stavfel och försök igen.|
|8|ConnectionBroken|Anslutningen mellan klient och Gateway- eller släppts. Ingen åtgärd krävs om det sker oväntat.|
|14|UnexpectedNetworkDisconnect|Anslutningen till nätverket har avbrutits. Be användaren att ansluta igen.|
|24|ReverseConnectFailed|Virtuella värddatorn har ingen direkt åtkomst till RD Gateway. Kontrollera att Gateway-IP-adressen kan matchas.|

## <a name="next-steps"></a>Nästa steg

Läs mer om roller i virtuella Windows-skrivbordet i [Windows Virtual Desktop förhandsversionsmiljön](environment-setup.md).

Om du vill se en lista över tillgängliga PowerShell-cmdletar för virtuella Windows-skrivbordet kan se den [PowerShell-referens](/powershell/windows-virtual-desktop/overview).
