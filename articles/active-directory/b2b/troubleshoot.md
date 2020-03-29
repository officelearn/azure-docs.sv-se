---
title: Felsökning av B2B-samarbete – Azure Active Directory | Microsoft-dokument
description: Åtgärder för vanliga problem med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 03/19/2020
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f8bafb04d0a5d9c6d25a7ed7e155888d492e9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050787"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Felsöka Azure Active Directory B2B-samarbete

Här är några lösningar på vanliga problem med Azure Active Directory (Azure AD) B2B-samarbete.

   > [!IMPORTANT]
   > **Från och med den 31 mars 2021**stöder Microsoft inte längre inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbetsscenarier. Som förberedelse uppmuntrar vi kunder att välja [e-post engångskodautentisering.](one-time-passcode.md) Vi välkomnar din feedback på den här offentliga förhandsversionen och är glada över att skapa ännu fler sätt att samarbeta.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Jag har lagt till en extern användare men inte ser dem i min globala adressbok eller i personväljaren

I de fall externa användare inte fylls i i listan kan det ta några minuter att replikera objektet.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>En B2B-gästanvändare visas inte i SharePoint Online/OneDrive-personer som väljer

Möjligheten att söka efter befintliga gästanvändare i SharePoint Online (SPO) personer plockare är OFF som standard för att matcha äldre beteende.

Du kan aktivera den här funktionen med inställningen "ShowPeoplePickerSuggestionsForGuestUsers" på klient- och webbplatssamlingsnivå. Du kan ange funktionen med cmdlets Set-SPOTenant och Set-SPOSite, som gör det möjligt för medlemmar att söka efter alla befintliga gästanvändare i katalogen. Ändringar i klientomfånget påverkar inte redan etablerade SPO-platser.

## <a name="invitations-have-been-disabled-for-directory"></a>Inbjudningar har inaktiverats för katalog

Om du får ett meddelande om att du inte har behörighet att bjuda in användare kontrollerar du att ditt användarkonto har behörighet att bjuda in externa användare under Azure Active Directory > Användarinställningar > Externa användare > Hantera inställningar för externt samarbete:

![Skärmbild som visar inställningarna för externa användare](media/troubleshoot/external-user-settings.png)

Om du nyligen har ändrat dessa inställningar eller tilldelat rollen Gästombjudning till en användare kan det ta 15–60 minuters fördröjning innan ändringarna börjar gälla.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Användaren som jag bjöd in får ett fel under inlösen

Vanliga fel är:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Invitee's Admin har förbjudit e-postVerifierade användare från att skapas i sin klientorganisation

När du bjuder in användare vars organisation använder Azure Active Directory, men där den specifika användarens konto inte finns (till exempel finns användaren inte i Azure AD contoso.com). Administratören för contoso.com kan ha en princip som förhindrar att användare skapas. Användaren måste kontrollera med sin administratör för att avgöra om externa användare är tillåtna. Den externa användarens administratör kan behöva tillåta e-postverifierade användare i sin domän (se den här [artikeln](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) om att tillåta e-postverifierade användare).

![Fel om att klienten inte tillåter e-postverifierade användare](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Extern användare finns inte redan i en federerad domän

Om du använder federationsautentisering och användaren inte redan finns i Azure Active Directory kan användaren inte bjudas in.

För att lösa problemet måste den externa användarens administratör synkronisera användarens konto till Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hur synkroniseras '\#', som normalt inte är ett giltigt tecken, med Azure AD?

"\#" är ett reserverat tecken i UPN för Azure AD user@contoso.com B2B-samarbete eller externa användare, eftersom det inbjudna kontot blir user_contoso.com#EXT#@fabrikam.onmicrosoft.com. \# Därför i UPN som kommer från lokala är inte tillåtet att logga in på Azure-portalen. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Jag får ett felmeddelande när du lägger till externa användare i en synkroniserad grupp

Externa användare kan bara läggas till i grupper om "tilldelad" eller "Säkerhet" och inte till grupper som behärskas lokalt.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Min externa användare fick inget e-postmeddelande för att lösa in

Den inbjudna bör kontrollera med sin ISP eller spam filter för att säkerställa att följande adress är tillåten:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Jag märker att det anpassade meddelandet inte inkluderas i inbjudningsmeddelanden ibland

För att följa sekretesslagar inkluderar våra API:er inte anpassade meddelanden i e-postbjudan när:

- Den inbjudna har ingen e-postadress i den inbjudande klienten
- När ett huvudnamn för apptjänst skickar inbjudan

Om det här scenariot är viktigt för dig kan du undertrycka vårt API-inbjudningsmeddelande och skicka det via valfri e-postmekanism. Kontakta din organisations juridiska ombud för att se till att alla e-postmeddelanden du skickar på detta sätt också följer sekretesslagar.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Felmeddelandet "AADSTS65005" visas när du försöker logga in på en Azure-resurs

En användare som har ett gästkonto kan inte logga in och får följande felmeddelande:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

Användaren har ett Azure-användarkonto och är en viral klientorganisation som har övergivits eller ohanterats. Dessutom finns det inga globala administratörer eller företagsadministratörer i klienten.

För att lösa problemet måste du ta över den övergivna klienten. Se [Ta över en ohanterat katalog som administratör i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Du måste också ansluta till den internetinriktade DNS för domänsuffixet i fråga för att kunna ge direkta bevis på att du har kontroll över namnområdet. När klienten har returnerats till ett hanterat tillstånd kan du diskutera med kunden om det är det bästa alternativet för deras organisation att lämna användarna och verifierat domännamn.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>En gästanvändare med en just-in-time- eller "viral" klientorganisation kan inte återställa sitt lösenord

Om identitetsklienten är en JUST-in-time (JIT) eller viral klient (vilket innebär att det är en separat, ohanterad Azure-klient) kan bara gästanvändaren återställa sitt lösenord. Ibland tar en organisation [över hanteringen av virala klienter](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) som skapas när medarbetare använder sina e-postadresser för arbete för att registrera sig för tjänster. När organisationen tar över en viral klientorganisation kan endast en administratör i den organisationen återställa användarens lösenord eller aktivera SSPR. Om det behövs kan du som inbjudande organisation ta bort gästanvändarkontot från katalogen och skicka en inbjudan igen.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>En gästanvändare kan inte använda AzureAD PowerShell V1-modulen

Från och med den 18 november 2019 blockeras gästanvändare i katalogen (definierat som användarkonton där **egenskapen userType** equals **Guest)** från att använda Modulen AzureAD PowerShell V1. Framöver måste en användare antingen vara medlemsanvändare (där **userType** är lika **med medlem)** eller använda AzureAD PowerShell V2-modulen.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>I en Azure US Government-klient kan jag inte bjuda in en gästanvändare för B2B-samarbete

Inom Azure US Government-molnet stöds B2B-samarbete för närvarande endast mellan klienter som både finns i Azure US Government-molnet och som båda stöder B2B-samarbete. Om du bjuder in en användare i en klient som inte ingår i Azure US Government-molnet eller som ännu inte stöder B2B-samarbete får du ett felmeddelande. Mer information och begränsningar finns i [Azure Active Directory Premium P1- och P2-varianter](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

## <a name="next-steps"></a>Nästa steg

[Få support för B2B-samarbete](get-support.md)
