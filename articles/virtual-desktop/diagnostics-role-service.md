---
title: Diagnostisera problem i Windows Virtual Desktop – Azure
description: Så här använder du funktionen Windows Virtual Desktop Diagnostics för att diagnosticera problem.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6ff1e3d8eb9cb40d46ae0624be9d37fd199accd2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288761"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Identifiera och diagnostisera problem med virtuella Windows-datorer

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md).

Windows Virtual Desktop erbjuder en diagnostisk funktion som gör det möjligt för administratören att identifiera problem via ett enda gränssnitt. Mer information om diagnostiska funktioner i Windows Virtual Desktop finns i [använda Log Analytics för Diagnostics-funktionen](diagnostics-log-analytics.md).

Anslutningar som inte når Windows Virtual Desktop visas inte i diagnostiska resultat, eftersom själva roll tjänsten för diagnostik är en del av det virtuella Windows-skrivbordet. Problem med Windows anslutning till virtuella skriv bord kan inträffa när slutanvändaren har problem med nätverks anslutningen.

## <a name="common-error-scenarios"></a>Vanliga felscenarier

Fel scenarier kategoriseras internt till tjänsten och externa till Windows Virtual Desktop.

* Internt problem: anger scenarier som inte kan åtgärdas av kunden och som måste lösas som ett support ärende. När du ger feedback via [Tech-communityn i Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)kan du ta med korrelations-ID: t och den ungefärliga tids ramen för när problemet uppstod.
* Externt problem: relatera till scenarier som kan begränsas av kunden. Dessa är externa för virtuella Windows-datorer.

I följande tabell visas vanliga fel som dina administratörer kan köra i.

>[!NOTE]
>Den här listan innehåller de vanligaste felen och uppdateras i en vanlig takt. Se till att du har den senaste informationen genom att gå tillbaka till den här artikeln minst en gång i månaden.

## <a name="management-errors"></a>Hanterings fel

|Felmeddelande|Föreslagen lösning|
|---|---|
|Det gick inte att skapa registrerings nyckeln |Det gick inte att skapa en registrerings-token. Försök att skapa den igen med kortare förfallo tid (mellan 1 timme och 1 månad). |
|Det gick inte att ta bort registrerings nyckeln|Det gick inte att ta bort registrerings-token. Försök att ta bort den igen. Om det fortfarande inte fungerar använder du PowerShell för att kontrol lera om token fortfarande finns där. Om det är där, tar du bort det med PowerShell.|
|Det gick inte att ändra sessionens värddator dränerings läge |Det gick inte att ändra dränerings läge på den virtuella datorn. Kontrol lera statusen för den virtuella datorn. Om den virtuella datorn inte är tillgänglig går det inte att ändra dränerings läge.|
|Det gick inte att koppla bort användarsessioner |Det gick inte att koppla bort användaren från den virtuella datorn. Kontrol lera statusen för den virtuella datorn. Om den virtuella datorn inte är tillgänglig kan användarsessionen inte kopplas från. Om den virtuella datorn är tillgänglig kontrollerar du användarsessionen för att se om den är frånkopplad. |
|Det gick inte att logga ut alla användare inom sessionens värd |Det gick inte att logga ut användare från den virtuella datorn. Kontrol lera statusen för den virtuella datorn. Användare kan inte loggas ut om de inte är tillgängliga. Kontrol lera användarsessionen för att se om de redan har loggat ut. Du kan tvinga ut utloggning med PowerShell. |
|Det gick inte att ta bort tilldelningen av användaren från program gruppen|Det gick inte att avpublicera en app-grupp för en användare. Kontrol lera om användaren är tillgänglig i Azure AD. Kontrol lera om användaren är en del av en användar grupp som app-gruppen publiceras till. |
|Ett fel uppstod vid hämtning av tillgängliga platser |Kontrol lera platsen för den virtuella datorn som används i guiden skapa värdbaserad pool. Om avbildningen inte är tillgänglig på den platsen lägger du till avbildningen på den platsen eller väljer en annan VM-plats. |

### <a name="external-connection-error-codes"></a>Fel koder för extern anslutning

|Numerisk kod|Felkod|Föreslagen lösning|
|---|---|---|
|– 2147467259|ConnectionFailedAdTrustedRelationshipFailure|Sessions värden är inte korrekt ansluten till Active Directory.|
|– 2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Anslutningarna misslyckades eftersom sessions värden inte är tillgänglig. Kontrol lera hälso tillståndet för sessionens värd.|
|– 2146233088|ConnectionFailedClientDisconnect|Om det här felet ofta visas kontrollerar du att användarens dator är ansluten till nätverket.|
|– 2146233088|ConnectionFailedNoHealthyRdshAvailable|Sessionen som värd användaren försökte ansluta till är inte felfri. Felsök den virtuella datorn.|
|– 2146233088|ConnectionFailedUserNotAuthorized|Användaren har inte behörighet att komma åt den publicerade appen eller Skriv bordet. Felet kan visas efter att administratören har tagit bort publicerade resurser. Be användaren att uppdatera feeden i programmet för fjärr skrivbord.|
|2|FileNotFound|Programmet som användaren försökte komma åt är antingen felaktigt installerat eller har angetts till en felaktig sökväg.<br><br>När du publicerar nya appar medan användaren har en aktiv session kommer användaren inte att kunna komma åt den här appen. Sessionen måste stängas av och startas om innan användaren kan komma åt appen. |
|3|InvalidCredentials|Användar namnet eller lösen ordet som användaren angav stämmer inte överens med befintliga användar namn eller lösen ord. Granska autentiseringsuppgifterna för skrivfel och försök igen.|
|8|ConnectionBroken|Anslutningen mellan klienten och gatewayen eller servern släpptes. Ingen åtgärd krävs om den inte sker utan förvarning.|
|14|UnexpectedNetworkDisconnect|Anslutningen till nätverket har släppts. Be användaren att ansluta igen.|
|24|ReverseConnectFailed|Den virtuella datorns värd har ingen direkt rad information till RD Gateway. Se till att IP-adressen för gatewayen kan matchas.|

## <a name="next-steps"></a>Nästa steg

Mer information om roller i Windows Virtual Desktop finns i [Windows Virtual Desktop-miljö](environment-setup.md).

Om du vill se en lista över tillgängliga PowerShell-cmdletar för virtuella Windows-datorer, se [PowerShell-referensen](/powershell/windows-virtual-desktop/overview).
