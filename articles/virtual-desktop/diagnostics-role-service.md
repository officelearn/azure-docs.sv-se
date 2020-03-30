---
title: Problem med diagnostisera Windows Virtual Desktop - Azure
description: Så här använder du diagnostikfunktionen för Windows Virtual Desktop för att diagnostisera problem.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254266"
---
# <a name="identify-and-diagnose-issues"></a>Identifiera och diagnostisera problem

Windows Virtual Desktop erbjuder en diagnostikfunktion som gör det möjligt för administratören att identifiera problem via ett enda gränssnitt. Windows Virtual Desktop-roller loggar en diagnostisk aktivitet när en användare interagerar med systemet. Varje logg innehåller relevant information, till exempel windows virtual desktop-roller som ingår i transaktionen, felmeddelanden, klientinformation och användarinformation. Diagnostiska aktiviteter skapas av både slutanvändare och administrativa åtgärder och kan kategoriseras i tre huvudgrupper:

* Feed-prenumerationsaktiviteter: slutanvändaren utlöser dessa aktiviteter när de försöker ansluta till sin feed via Microsoft Remote Desktop-program.
* Anslutningsaktiviteter: Slutanvändaren utlöser dessa aktiviteter när de försöker ansluta till ett skrivbord eller En RemoteApp via Microsoft Remote Desktop-program.
* Hanteringsaktiviteter: administratören utlöser dessa aktiviteter när de utför hanteringsåtgärder på systemet, till exempel skapa värdpooler, tilldela användare till appgrupper och skapa rolltilldelningar.
  
Anslutningar som inte når Windows Virtual Desktop visas inte i diagnostikresultat eftersom själva diagnostikrolltjänsten är en del av Windows Virtual Desktop. Anslutningsproblem med Windows Virtual Desktop kan inträffa när slutanvändaren har problem med nätverksanslutningen.

För att komma [igång, ladda ner och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan har gjort det. Därefter kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnostisera problem med PowerShell

Windows Virtual Desktop Diagnostics använder bara en PowerShell-cmdlet men innehåller många valfria parametrar för att begränsa och isolera problem. I följande avsnitt visas de cmdlets som du kan köra för att diagnostisera problem. De flesta filter kan användas tillsammans. Värden som `<tenantName>`anges inom parentes, till exempel , bör ersättas med de värden som gäller för din situation.

>[!IMPORTANT]
>Diagnostikfunktionen är för felsökning för en användare. Alla frågor som använder PowerShell måste innehålla parametrarna *-UserName* eller *-ActivityID.* Använd Log Analytics för övervakningsfunktioner. Mer information om hur du skickar diagnostikdata till arbetsytan finns i [Använda logganalys för diagnostikfunktionen.](diagnostics-log-analytics.md) 

### <a name="filter-diagnostic-activities-by-user"></a>Filtrera diagnostikaktiviteter efter användare

Parametern **-UserName** returnerar en lista över diagnostiska aktiviteter som initierats av den angivna användaren, vilket visas i följande exempel cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Parametern **-UserName** kan också kombineras med andra valfria filtreringsparametrar.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrera diagnostikaktiviteter efter tid

Du kan filtrera den returnerade diagnostikaktivitetslistan med parametrarna **-StartTime** och **-EndTime.** Parametern **-StartTime** returnerar en lista över diagnostikaktiviteter från ett visst datum, vilket visas i följande exempel.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

Parametern **-EndTime** kan läggas till i en cmdlet med parametern **-StartTime** för att ange en viss tidsperiod som du vill ta emot resultat för. I följande exempel returneras en lista över diagnostiska aktiviteter från mellan 1 augusti och 10 augusti.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Parametrarna **-StartTime** och **-EndTime** kan också kombineras med andra valfria filtreringsparametrar.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtrera diagnostikaktiviteter efter aktivitetstyp

Du kan också filtrera diagnostikaktiviteter efter aktivitetstyp med parametern **-ActivityType.** Följande cmdlet returnerar en lista över slutanvändaranslutningar:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Följande cmdlet returnerar en lista över administratörshanteringsuppgifter:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Cmdleten Get-RdsDiagnosticActivities** stöder för närvarande inte att ange feed som ActivityType.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrera diagnostikaktiviteter efter utfall

Du kan filtrera den returnerade diagnostikaktivitetslistan efter resultat med parametern **-Outcome.** I följande exempel returneras en lista över lyckade diagnostiska aktiviteter.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

I följande exempel returneras en lista över misslyckade diagnostiska aktiviteter i följande exempel.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**Parametern -Outcome** kan också kombineras med andra valfria filtreringsparametrar.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Hämta en viss diagnostikaktivitet efter aktivitets-ID

Parametern **-ActivityId** returnerar en specifik diagnostisk aktivitet om den finns, vilket visas i följande cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Visa felmeddelanden för en misslyckad aktivitet efter aktivitets-ID

Om du vill visa felmeddelandena för en misslyckad aktivitet måste du köra cmdleten med parametern **-Detaljerad.** Du kan visa listan över fel genom att köra cmdleten **Select-Object.**

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Hämta detaljerade diagnostikaktiviteter

Parametern **-Detailed** innehåller ytterligare information för varje returnerad diagnostikaktivitet. Formatet för varje aktivitet varierar beroende på dess aktivitetstyp. **Parametern -Detailed** kan läggas till i alla **Get-RdsDiagnosticActivities-frågor,** som visas i följande exempel.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Vanliga felscenarier

Felscenarier kategoriseras internt till tjänsten och externa till Windows Virtual Desktop.

* Internt problem: anger scenarier som inte kan mildras av klientadministratören och måste lösas som ett supportproblem. När du ger feedback via [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)ska du inkludera aktivitets-ID:t och ungefärlig tidsram för när problemet uppstod.
* Externt problem: relaterar till scenarier som kan mildras av systemadministratören. Dessa är externa till Windows Virtual Desktop.

I följande tabell visas vanliga fel som administratörerna kan stöta på.

>[!NOTE]
>Den här listan innehåller de vanligaste felen och uppdateras på en vanlig kadens. Se till att du har den senaste informationen, se till att kolla tillbaka den här artikeln minst en gång i månaden.

### <a name="external-management-error-codes"></a>Felkoder för extern hantering

|Numerisk kod|Felkod|Föreslagen lösning|
|---|---|---|
|3|Obehörigt tillträde|Användaren som försökte köra den administrativa PowerShell-cmdlet har antingen inte behörighet att göra det eller felskrivna sitt användarnamn.|
|1000|TenantNotFound (Hyresgäst)|Klientnamnet som du angav matchar inte några befintliga klienter. Granska klientnamnet för stavfel och försök igen.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Du kan inte ta bort en klient så länge den innehåller objekt. Ta bort sessionsvärdpoolerna först och försök sedan igen.|
|2000|HostPoolNotFound|Värdpoolnamnet du angav matchar inte några befintliga värdpooler. Granska värdpoolens namn för stavfel och försök igen.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups HostPoolCannotBeRemovedHasApplicationGroups HostPool|Du kan inte ta bort en värdpool så länge den innehåller objekt. Ta först bort alla appgrupper i värdpoolen.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Ta bort alla sessioner värdar först innan du tar bort sessionen värdpoolen.|
|5001|SessionHostNotFound|Den sessionsvärd du frågade kan vara offline. Kontrollera värdpoolens status.|
|5008|SessionHostUserSessionsExist |Du måste logga ut alla användare på sessionsvärden innan du kör den avsedda hanteringsaktiviteten.|
|6000|AppGroupNotFound|Appgruppnamnet du angav matchar inte några befintliga appgrupper. Granska appgruppsnamnet för stavfel och försök igen.|
|6022|RemoteAppNotFound|RemoteApp-namnet du angav matchar inte några RemoteApps. Granska RemoteApp-namnet för stavfel och försök igen.|
|6010|PublishedItemsExist|Namnet på den resurs som du försöker publicera är detsamma som en resurs som redan finns. Ändra resursnamnet och försök igen.|
|7002|Namn InteValidWhiteSpace|Använd inte tomt utrymme i namnet.|
|8000|Ogiltigaförfattareiseringsrollkop|Rollnamnet du angav matchar inte några befintliga rollnamn. Granska rollnamnet för stavfel och försök igen. |
|8001|AnvändareNotFound |Användarnamnet som du angav matchar inte några befintliga användarnamn. Granska namnet på stavfel och försök igen.|
|8005|AnvändareNintendeInAAD |Användarnamnet som du angav matchar inte några befintliga användarnamn. Granska namnet på stavfel och försök igen.|
|8008|TenantConsentRequired|Följ instruktionerna [här](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) för att ge ditt samtycke till din klient.|

### <a name="external-connection-error-codes"></a>Felkoder för extern anslutning

|Numerisk kod|Felkod|Föreslagen lösning|
|---|---|---|
|-2147467259|AnslutningFailedAdTrustedRelationskapFailure|Sessionsvärden är inte korrekt ansluten till Active Directory.|
|-2146233088|AnslutningFailedUserHasValidSessionButRdshIsUnhealthy|Anslutningarna misslyckades eftersom sessionsvärden inte är tillgänglig. Kontrollera sessionsvärdens hälsa.|
|-2146233088|AnslutningFailedClientDisconnect|Om det här felet visas ofta kontrollerar du att användarens dator är ansluten till nätverket.|
|-2146233088|AnslutningFailedNoHealthyRdshVailable|Sessionen som värdanvändaren försökte ansluta till är inte felfri. Felsök den virtuella datorn.|
|-2146233088|AnslutningMissadeAnvändareNär inte auktoriserat|Användaren har inte behörighet att komma åt den publicerade appen eller skrivbordet. Felet kan visas när administratören har tagit bort publicerade resurser. Be användaren att uppdatera flödet i programmet Fjärrskrivbord.|
|2|FilBlgrunda|Programmet som användaren försökte komma åt är antingen felaktigt installerat eller inställt på en felaktig sökväg.|
|3|Ogiltiga skruvar|Användarnamnet eller lösenordet som användaren angav matchar inte befintliga användarnamn eller lösenord. Granska autentiseringsuppgifterna för stavfel och försök igen.|
|8|AnslutningBroken|Anslutningen mellan klient och gateway eller server har tagits bort. Ingen åtgärd behövs om det inte händer oväntat.|
|14|UnexpectedNetworkDisconnect|Anslutningen till nätverket avbröts. Be användaren att ansluta igen.|
|24|Omvänd anslutningFailerad|Värddatorn har ingen direkt siktlinje till fjärrskrivbordsgatewayen. Kontrollera att gateway-IP-adressen kan lösas.|

## <a name="next-steps"></a>Nästa steg

Mer information om roller i Windows Virtual Desktop finns i [Windows Virtual Desktop-miljö](environment-setup.md).

Information om hur du ser en lista över tillgängliga PowerShell-cmdletar för Windows Virtual Desktop finns i [PowerShell-referensen](/powershell/windows-virtual-desktop/overview).
