---
title: "Vad är självbetjäningsregistrering för Azure? | Microsoft Docs"
description: "En översikt över självbetjäningsregistrering för Azure, hur du hanterar registreringen och hur du tar över ett DNS-domännamn."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 511088156d3546e2e0f3ac40e72bf2b8e4ae2cb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-self-service-signup-for-azure"></a>Vad är självbetjäningsregistrering för Azure?
Det här avsnittet beskrivs processen självbetjäningsregistrering och hur du tar över ett DNS-domännamn.  

## <a name="why-use-self-service-signup"></a>Varför använda självbetjäningsregistrering?
* Hämta kunder till tjänster som de vill snabbare.
* Skapa e-postbaserad erbjudanden för en tjänst.
* Skapa e-postbaserad signup flöden som snabbt kan du skapa identiteter med hjälp av deras arbete lätt att komma ihåg e-alias.
* Ohanterad Azure kataloger kan göras i hanterade kataloger senare och återanvändas för andra tjänster.

## <a name="terms-and-definitions"></a>Termer och definitioner
* **Självbetjäningsregistreringen**: det här är den metod som en användare som registrerar sig för en tjänst i molnet och har en identitet skapas automatiskt för dem i Azure Active Directory (AD Azure) baserat på deras e-postdomän.
* **Ohanterad Azure directory**: det här är den katalog där identitet skapas. En ohanterad katalog är en katalog som har ingen global administratör.
* **E-post verifierade användaren**: Detta är en typ av konto i Azure AD. En användare som har en identitet skapas automatiskt när du registrerar dig för ett erbjudande för självbetjäning kallas en e-post verifierade användare. Ett e-post verifierade användaren är medlem reguljära i en katalog som är märkta med creationmethod = EmailVerified.

## <a name="user-experience"></a>Användarens upplevelse
Anta exempelvis att en användare vars e-post är Dan@BellowsCollege.com tar emot känsliga filer via e-post. Filerna har skyddats av Azure Rights Management (Azure RMS). Men Lisas organisation, bälgar universitet har inte registrerat dig för Azure RMS eller har distribuerat Active Directory RMS. I det här fallet kan Dan registrera dig för en kostnadsfri prenumeration på RMS för enskilda användare för att kunna läsa de skyddade filerna.

Om Dan är den första användaren med en e-postadress från BellowsCollege.com registrera dig för den här självbetjäning erbjuder, och sedan en ohanterad katalog kommer att skapas för BellowsCollege.com i Azure AD. Om andra användare från domänen BellowsCollege.com registrerar dig för detta erbjudande eller ett liknande erbjudande för självbetjäning, måste de även e-post verifierade användarkonton som skapats i samma ohanterade katalog i Azure.

## <a name="admin-experience"></a>Administratörsupplevelse
En administratör som äger DNS-domännamnet för en ohanterad Azure-katalog kan ta över eller merge-katalogen när bevisar ägarskap. I nästa avsnitt beskrivs administratörsupplevelse i detalj, men här är en sammanfattning:

* När du tar över en ohanterad Azure directory blir du helt enkelt global administratör för katalogen ohanterad. Detta kallas ibland ett internt övertag.
* När du kopplar en ohanterad Azure-katalogen kan du lägga till DNS-domännamnet för katalogen som ohanterad till hanterad Azure-katalogen och skapas en mappning av användare till resurser så användare kan fortsätta att komma åt tjänster utan avbrott. Detta kallas ibland ett externt övertag.

## <a name="what-gets-created-in-azure-active-directory"></a>Vad skapas i Azure Active Directory?
#### <a name="directory"></a>Directory
* En Azure Active Directory-katalog för domänen skapas en katalog per domän.
* Azure AD-katalog har ingen global administratör.

#### <a name="users"></a>Användare
* För varje användare som registrerar sig för har ett användarobjekt skapats i Azure AD-katalog.
* Varje användarobjekt har markerats som extern.
* Varje användare får åtkomst till tjänsten som de har registrerat sig för.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Hur anspråksregelmallar en Azure AD med självbetjäning katalog för en domän som jag äger?
Du kan begära en Azure AD med självbetjäning katalogen genom att utföra verifiering av domän. Verifiering av domän bevisar du äger domänen genom att skapa DNS-poster.

Det finns två sätt att göra en DNS-övertag av Azure AD-katalog:

* internt övertag (Admin identifierar en ohanterad Azure-katalog och vill göra till en hanterad katalog)
* externa övertag (Admin försöker lägga till en ny domän i deras hanterade Azure directory)

Du kan vara intresserad av att verifiera att du äger en domän eftersom du tar över en ohanterad katalog när en användare har genomfört självbetjäningsregistrering eller du kanske att lägga till en ny domän till en befintlig hanterad katalog. Till exempel att du har en domän med namnet contoso.com och du vill lägga till en ny domän med namnet contoso.co.uk eller contoso.uk.

## <a name="what-is-domain-takeover"></a>Vad är domänen övertag?
Det här avsnittet beskriver hur du verifierar att du äger en domän

### <a name="what-is-domain-validation-and-why-is-it-used"></a>Vad är verifiering av domän och varför används den?
För att utföra åtgärder på en katalog, kräver Azure AD att du verifierar ägarskapet för DNS-domän.  Validering av domänen kan du göra anspråk på katalogen och antingen befordra katalogen självbetjäning till en hanterad katalog eller sammanfoga katalogen självbetjäning till en befintlig hanterad katalog.

## <a name="examples-of-domain-validation"></a>Exempel på verifiering av domän
Det finns två sätt att göra en DNS-övertag i en katalog:

* internt övertag (t.ex, en administratör identifierar en självbetjäning, ohanterad katalog och vill göra till hanterad katalog)
* externa övertag (t.ex, en administratör försöker att lägga till en ny domän i en hanterad katalog)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Internt övertag - befordra en självbetjäning, ohanterad katalog för att vara en hanterad katalog
När du gör interna övertag konverteras katalogen från en ohanterad katalog till en hanterad katalog. Du måste utföra valideringen av DNS-domän, där du skapar en MX-post eller en TXT-post i DNS-zonen. Åtgärden:

* Verifierar att du äger domänen
* Gör den katalog som hanteras
* Gör den globala administratören i katalogen

Anta att en IT-administratör bälgar kollega upptäcker att användare från skolan har registrerat sig för erbjudanden för självbetjäning. Som registrerad ägare av DNS-namnet BellowsCollege.com IT-administratören verifiera ägarskapet för DNS-namnet i Azure och sedan ta över ohanterade katalogen. Katalogen blir en hanterad katalog och IT-administratören har tilldelats rollen global administratör för katalogen BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Externa övertag - koppla en självbetjäning katalog till en befintlig hanterad katalog
I en extern övertag du har redan en hanterad katalog och du vill att alla användare och grupper från en ohanterad katalog för att ansluta till den hantera katalogen i stället egna två separata kataloger.

Du lägger till en domän som administratör för en hanterad katalog och domänen händer med har en ohanterad katalog som hör till den.

Till exempel anta att du är IT-administratör och du redan har en hanterad katalog för Contoso.com, ett domännamn som har registrerats för din organisation. Du upptäcker att användare från din organisation har utfört självbetjäning logga in för ett erbjudande via e-domännamn user@contoso.co.uk, vilket är ett annat domännamn som din organisation äger. Dessa användare har för närvarande konton i en ohanterad för contoso.co.uk.

Du vill inte hantera två separata kataloger, så att du slå samman ohanterade katalogen för contoso.co.uk till din befintliga IT-hanterad katalog för contoso.com.

Externa övertag följer samma DNS-valideringen som interna övertag.  Skillnad vara: användare och tjänster mappas om till katalogen IT hanteras.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Vad är effekten av den en extern övertag?
Med en extern övertag skapas en mappning av användare till resurser så att användarna kan fortsätta att komma åt tjänster utan avbrott. Många program, inklusive RMS för enskilda användare, hanterar mappningen av användare till resurser och och användare kan fortsätta att komma åt dessa tjänster utan att ändra. Om ett program inte hanterar mappningen av användare till resurser effektivt, blockeras externa övertag explicit för att förhindra användare från en dålig upplevelse.

#### <a name="directory-takeover-support-by-service"></a>Directory gäller stöd av tjänsten
För närvarande stöder följande tjänster övertag:

* RMS

Följande tjänster kommer snart att stödja övertag:

* PowerBI

Följande inte och kräver ytterligare administrativa åtgärder för att migrera användardata när en extern övertag.

* SharePoint/OneDrive

## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Så här utför du en DNS-domänen namnet övertag
Har du några alternativ att utföra verifiering av domän (och en övertag om du vill):

1. Hanteringsportalen för Azure

   En övertag utlöses genom att göra ett tillägg för domänen.  Om det finns redan en katalog för domänen, har du alternativet att utföra en extern övertag.

   Logga in på Azure-portalen med dina autentiseringsuppgifter.  Navigera till en befintlig katalog och sedan till **Lägg till domän**.
2. Office 365

   Du kan använda alternativen på den [hantera domäner](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) sida i Office 365 för att arbeta med domäner och DNS-poster. Se [verifiera din domän i Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).
3. Windows PowerShell

   Följande steg krävs för att utföra en verifiering med Windows PowerShell.

   | Steg | För att använda |
   | --- | --- |
   | Skapa ett autentiseringsuppgiftobjekt |Get-Credential |
   | Anslut till Azure AD |Connect-MsolService |
   | Hämta en lista över domäner |Get-MsolDomain |
   | Skapa en utmaning |Get-MsolDomainVerificationDns |
   | Skapa DNS-post |Gör detta på DNS-servern |
   | Kontrollera utmaningen |Confirm-MsolEmailVerifiedDomain |

Exempel:

1. Anslut till Azure AD med hjälp av autentiseringsuppgifterna som användes för att svara på självbetjäning-erbjudande:

        import-module MSOnline
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
2. Hämta en lista över domäner:

    Get-MsolDomain
3. Kör cmdleten Get-MsolDomainVerificationDns om du vill skapa en förfrågan:

    Get-MsolDomainVerificationDns – DomainName *your_domain_name* – läge DnsTxtRecord

    Exempel:

    Get-MsolDomainVerificationDns – DomainName contoso.com – läge DnsTxtRecord
4. Kopiera värdet (challenge) som returneras från det här kommandot.

    Exempel:

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. Skapa en DNS txt-post som innehåller värdet som du kopierade i föregående steg i det offentliga DNS-namnområdet.

    Namnet för den här posten är namnet på den överordnade domänen, om du skapar denna resurspost med hjälp av DNS-serverrollen från Windows Server, se till att posten namn är tomt och bara klistra in värdet i textrutan
6. Kör cmdleten bekräfta MsolDomain för att verifiera utmaningen:

    Bekräfta MsolEmailVerifiedDomain - DomainName *your_domain_name*

    Exempel:

    Bekräfta MsolEmailVerifiedDomain - DomainName contoso.com

En lyckad utmaning returnerar du uppmaningen utan fel.

## <a name="how-do-i-control-self-service-settings"></a>Hur kan jag styra självbetjäning inställningar?
Administratörer har två självbetjäning kontroller idag. De kan styra:

* Huruvida användare kan ansluta till katalogen via e-post.
* Huruvida användare kan licensiera själva för program och tjänster.

### <a name="how-can-i-control-these-capabilities"></a>Hur kan du styra dessa funktioner?
En administratör kan konfigurera de här funktionerna med dessa Azure AD-cmdlet Set-MsolCompanySettings-parametrar:

* **AllowEmailVerifiedUsers** styr om en användare kan skapa eller Anslut till en ohanterad katalog. Om du anger parametern $false inga e-post verifierade användare kan ansluta till katalogen.
* **AllowAdHocSubscriptions** styr möjligheten för användare att utföra självbetjäning logga in. Om du anger parametern $false kan ingen användare utföra självbetjäningsregistrering.

### <a name="how-do-the-controls-work-together"></a>Hur fungerar kontrollerna som tillsammans?
Dessa två parametrar kan användas tillsammans för att definiera mer exakt kontroll över självbetjäning logga in. Till exempel följande kommando gör att användarna kan utföra självbetjäning logga in, men endast om de användarna som redan har ett konto i Azure AD (användare som behöver en verifierad e-post konto skapas kan med andra ord utför självbetjäning logga in):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

I följande flödesschema förklarar alla olika kombinationer för dessa parametrar och de resulterande villkor för katalogen och självbetjäning logga in.

![][1]

Mer information och exempel på hur du använder dessa parametrar finns [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="see-also"></a>Se även
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
