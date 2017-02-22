---
title: "Hur Azure-prenumerationer är associerade med Azure Active Directory | Microsoft Docs"
description: Inloggning i Microsoft Azure och relaterad information, till exempel relationen mellan en Azure-prenumeration och Azure Active Directory.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2d428e0e3aaf8fd4a2138648411da644ccd308f6
ms.openlocfilehash: 4eee1b3f30c9e47983af5c1dfa60fe0deb8c2cc8


---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Hur Azure-prenumerationer är associerade med Azure Active Directory
Det här avsnittet innehåller information om hur du loggar in i Microsoft Azure och relaterad information, till exempel relationen mellan en Azure-prenumeration och Azure AD (Active Directory Azure).

## <a name="accounts-that-you-can-use-to-sign-in"></a>Konton som du kan använda för att logga in
Vi ska börja med de konton som du kan använda för att logga in. Det finns två typer: ett Microsoft-konto (kallades tidigare Microsoft Live-ID) och ett arbets- eller skolkonto, som är ett konto som lagras i Azure AD.

| Microsoft-konto | Azure AD-konto |
| --- | --- |
| Konsumentidentitetssystemet som körs av Microsoft |Företagsidentitetssystemet som körs av Microsoft |
| Autentisering för konsumentinriktade tjänster, till exempel Hotmail och MSN |Autentisering för företagsinriktade tjänster, till exempel Office 365 |
| Konsumenterna skapar sina egna Microsoft-konton, exempelvis när de registrerar sig för e-post |Företag och organisationer skapar och hanterar sina egna arbets- eller skolkonton |
| Identiteter skapas och lagras i Microsofts kontosystem |Identiteter skapas med hjälp av Azure eller en annan tjänst som Office 365 och lagras i en Azure AD-instans som tilldelats organisationen |

Även om Azure ursprungligen endast tillät åtkomst av användare med Microsoft-konton tillåts numera åtkomst av användare från *båda* systemen. Vi gjorde detta genom att konfigurera alla Azure-egenskaper att lita på Azure AD för autentisering, genom att låta Azure AD autentisera organisationsanvändare och genom att skapa en federationsrelation där Azure AD litar på konsumentidentitetssystemet för Microsoft-konton för autentisering av konsumentanvändare. Resultatet är att Azure AD kan autentisera Microsoft-gästkonton och ”interna” Azure AD-konton.

Här loggar till exempel en användare med ett Microsoft-konto in på den klassiska Azure-portalen.

> [!NOTE]
> För att kunna logga in på den klassiska Azure-portalen måste msmith@hotmail.com ha en prenumeration på Azure. Kontot måste antingen vara en tjänstadministratör eller en medadministratör för prenumerationen.
>
>

![][1]

Eftersom den här Hotmail-adressen är ett konsumentkonto autentiseras inloggningen av konsumentidentitetssystemet för Microsoft-konton. Azure AD-identitetssystemet litar på autentiseringen som utförs av Microsofts kontosystem och utfärdar en token för att få åtkomst till Azure-tjänster.

## <a name="how-an-azure-subscription-is-related-to-azure-ad"></a>Hur en Azure-prenumeration är kopplad till Azure AD
Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Du kan se vilken katalog som är betrodd av din prenumeration på fliken Inställningar. Du kan [redigera prenumerationsinställningarna](active-directory-understanding-resource-access.md) om du vill ändra vilken katalog prenumerationen litar på.

Den här förtroenderelationen mellan en prenumeration och en katalog skiljer sig från relationen mellan en prenumeration och alla andra resurser i Azure (webbplatser, databaser och så vidare), som är mer som underordnade resurser till en prenumeration. Om en prenumeration går ut stoppas även åtkomsten till de resurser som är associerade med prenumerationen. Men katalogen finns kvar i Azure och du kan associera en annan prenumeration med katalogen och fortsätta hantera kataloganvändarna.

Azure AD-tillägget som du ser i din prenumeration fungerar inte heller på samma sätt som andra tillägg på den klassiska Azure-portalen. Andra tillägg på den klassiska Azure-portalen är begränsade till Azure-prenumerationen. Vad du ser i Azure AD-tillägget varierar inte baserat på prenumerationen – det visar bara kataloger baserat på den inloggade användaren.

Alla användare har en enda arbetskatalog som autentiserar dem, men de kan även vara gäster i andra kataloger. I Azure AD-tillägget ser du alla kataloger som ditt användarkonto är medlem i. Kataloger som ditt konto inte är medlem i visas inte. En katalog kan utfärda token för arbets- eller skolkonton i Azure AD eller för Microsoft-kontoanvändare (eftersom Azure AD är federerat med Microsoft-kontosystemet).

Det här diagrammet visar en prenumeration för Michael Smith när han har registrerat sig med ett arbetskonto för Contoso.

![][2]

## <a name="how-to-manage-a-subscription-and-a-directory"></a>Hantera en prenumeration och en katalog
De administrativa rollerna för en Azure-prenumeration hanterar resurser som är knutna till Azure-prenumerationen. Rollerna och bästa praxis för att hantera din prenumeration beskrivs i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles.md).

Som standard tilldelas du rollen som tjänstadministratör när du registrerar dig. Om andra behöver logga in och komma åt tjänster med samma prenumeration kan du lägga till dem som medadministratörer. Tjänstadministratören och medadministratörer kan vara antingen Microsoft-konton eller arbets- eller skolkonton från katalogen som Azure-prenumerationen är associerad med.

Azure AD har en annan uppsättning administrativa roller för att hantera katalog- och identitetsrelaterade funktioner. Exempelvis kan den globala administratören för en katalog lägga till användare och grupper i katalogen eller kräver Multi-Factor Authentication för användare. En användare som skapar en katalog tilldelas rollen som global administratör och kan tilldela administratörsroller till andra användare.

Som med prenumerationsadministratörer kan de administrativa rollerna i Azure AD antingen vara Microsoft-konton eller arbets- eller skolkonton. Administrativa roller i Azure AD används också av andra tjänster som Office 365 och Microsoft Intune. Mer information finns i [Tilldela administratörsroller](active-directory-assign-admin-roles.md).

Men det som vi vill understryka är att Azure-prenumerationsadministratörer och Azure AD-katalogadministratörer är två separata begrepp. Azure-prenumerationsadministratörer kan hantera resurser i Azure och kan visa Active Directory-tillägget på den klassiska Azure-portalen (eftersom den klassiska Azure-portalen är en Azure-resurs). Katalogadministratörer kan hantera egenskaper i katalogen.

En person kan ha båda rollerna men det är inget krav. En användare kan tilldelas rollen som global katalogadministratör men inte tilldelas rollen som tjänstadministratör eller medadministratörer för en Azure-prenumeration. Användaren måste vara administratör för prenumerationen för att kunna logga in på den klassiska Azure-portalen. Men användaren kan utföra administrationsuppgifter för katalogen med hjälp av andra verktyg som Azure AD PowerShell eller administrationscenter för Office 365.

## <a name="why-cant-i-manage-the-directory-with-my-current-user-account"></a>Varför kan jag inte hantera katalogen med mitt aktuella användarkonto?
Ibland kan en användare försöka logga in på den klassiska Azure-portalen med ett arbets- eller skolkonto innan han eller hon har registrerat sig för en Azure-prenumeration. I så fall får användaren ett meddelande om att det inte finns någon prenumeration för det kontot. Meddelandet innehåller en länk för att starta en kostnadsfri utvärderingsprenumeration.

När användaren har registrerat sig för den kostnadsfria utvärderingsversionen visas katalogen för organisationen på den klassiska Azure-portalen men användaren kan inte hantera den (dvs. kan inte lägga till användare eller redigera befintliga användaregenskaper) eftersom han eller hon inte är en global katalogadministratör. Användaren kan använda den klassiska Azure-portalen och kan se Azure Active Directory-tillägget, men ytterligare behörighet som global administratör krävs för att hantera katalogen.

## <a name="using-your-work-or-school-account-to-manage-an-azure-subscription-that-was-created-by-using-a-microsoft-account"></a>Hantera en Azure-prenumeration som skapats med hjälp av ett Microsoft-konto med ditt arbets- eller skolkonto
Som bästa praxis bör du [registrera dig för Azure som en organisation](sign-up-organization.md) och använda ett arbets- eller skolkonto för att hantera resurser i Azure. Arbets- eller skolkonton är att föredra eftersom de kan hanteras centralt av organisationen som har utfärdat dem, de har fler funktioner än Microsoft-konton och de autentiseras direkt av Azure AD. Samma konto ger åtkomst till andra Microsoft-onlinetjänster som erbjuds till företag och organisationer, t.ex. Office 365 eller Microsoft Intune. Om du redan har ett konto som du använder med dessa andra egenskaper vill du förmodligen använda detta konto med Azure. Du har också redan en Active Directory-instans med dessa egenskaper som du vill att din Azure-prenumeration ska lita på.

Arbets- eller skolkonton kan också hanteras på fler sätt än ett Microsoft-konto. Exempelvis kan en administratör återställa lösenordet för ett arbets- eller skolkonto, eller kräva Multi-Factor Authentication för det.

I vissa fall kanske du vill att en användare från din organisation ska kunna hantera resurser som är associerade med en Azure-prenumeration för ett Microsoft-konsumentkonto. Mer information om hur du går över till att låta olika konton hantera prenumerationer eller kataloger finns i [Hantera katalogen för din Office 365-prenumeration i Azure](#manage-the-directory-for-your-office-365-subscription-in-azure).

## <a name="signing-in-when-you-used-your-work-email-for-your-microsoft-account"></a>Logga in när du har använt din e-postadress för arbetet för ditt Microsoft-konto
Om du tidigare har skapat ett Microsoft-konsumentkonto med din e-postadress till arbetet som användar-ID visas en sida där du kan välja mellan Microsoft Azure-kontosystemet eller Microsoft-kontosystemet.

![][3]

Du har användarkonton med samma namn, det ena i Azure AD och det andra i Microsoft-konsumentkontosystemet. Välj kontot som är associerat med den Azure-prenumeration som du vill använda. Om du får ett fel som anger att det inte finns någon prenumeration för den här användaren valde du förmodligen bara fel alternativ. Logga ut och försök igen. Mer information om fel som kan förhindra inloggning finns i [Felsöka ”Det gick inte att hitta några prenumerationer som är kopplade till ditt konto”](https://social.msdn.microsoft.com/Forums/en-US/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement).

## <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Hantera katalogen för din Office 365-prenumeration i Azure
Anta att du har registrerat dig för Office 365 innan du registrerar dig för Azure. Nu vill du hantera katalogen för Office 365-prenumerationen på den klassiska Azure-portalen. Du kan göra på två sätt beroende på om du har registrerat dig för Azure eller inte.

### <a name="i-do-not-have-a-subscription-for-azure"></a>Jag har ingen prenumeration på Azure
I så fall [registrerar du dig bara för Azure](sign-up-organization.md) med samma arbets- eller skolkonto som du använder för att logga in i Office 365. Relevant information från Office 365-kontot fylls i automatiskt i Azure-registreringsformuläret. Ditt konto tilldelas rollen som tjänstadministratör för prenumerationen.  

### <a name="i-do-have-a-subscription-for-azure-using-my-microsoft-account"></a>Jag har en Azure-prenumeration genom mitt Microsoft-konto
Om du har registrerat dig för Office 365 med ett arbets- eller skolkonto och sedan registrerar dig för Azure med ett Microsoft-konto har du två kataloger: en för arbetet eller skolan och en standardkatalog som skapades när du registrerade dig för Azure.

Följ stegen nedan för att hantera både kataloger på den klassiska Azure-portalen.

> [!NOTE]
> Dessa steg kan bara utföras när en användare är inloggad med ett Microsoft-konto. Om användaren har loggat in med ett arbets- eller skolkonto är inte alternativet **Använd befintlig katalog** tillgängligt eftersom ett arbets- eller skolkonto endast kan autentiseras av sin hemkatalog (dvs. den katalog där arbets- eller skolkontot lagras och som ägs av arbetet eller skolan).
>
>

1. Logga in på den klassiska Azure-portalen med ditt Microsoft-konto.
2. Klicka på **Nytt** > **Apptjänster** > **Active Directory** > **Katalog** > **Skapa anpassade**.
3. Klicka på **Använd befintlig katalog** och markera **Jag är redo att bli utloggad nu** och klicka på bockmarkeringen för att slutföra åtgärden.
4. Logga in på den klassiska Azure-portalen med ett konto som har globala administratörsrättigheter för arbets- eller skolkatalogen.
5. Du tillfrågas om du vill **använda Contoso-katalogen med Azure**. Klicka på **Fortsätt**.
6. Klicka på **Logga ut nu**.
7. Logga in på den klassiska Azure-portalen igen med ditt Microsoft-konto. Båda mapparna visas i Active Directory-tillägget.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du ändrar administratörer för en Azure-prenumeration finns i [Lägga till eller ändra Azure-administratörsroller](../billing/billing-add-change-azure-subscription-administrator.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](active-directory-understanding-resource-access.md)
* Mer information om hur du tilldelar roller i Azure AD finns i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles.md)
* [Registrera dig för Azure som en organisation](sign-up-organization.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG



<!--HONumber=Feb17_HO2-->


