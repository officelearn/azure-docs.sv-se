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
ms.date: 08/24/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 283c9903501a1e497e4dde81146d21edb869e9e2
ms.contentlocale: sv-se
ms.lasthandoff: 08/28/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Hur Azure-prenumerationer är associerade med Azure Active Directory
Det här avsnittet innehåller information om relationen mellan en Azure-prenumeration och Azure AD (Active Directory Azure) och hur du lägger till en befintlig prenumeration till din Azure Active Directory-katalog.

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Din Azure-prenumerations relation till Azure AD
Din Azure-prenumeration har en förtroenderelation med Azure AD, vilket innebär att den har förtroende för katalogen när det gäller att autentisera användare, tjänster och enheter. Flera prenumerationer kan ha förtroende för samma katalog, men varje prenumeration har bara förtroende för en katalog. 

Förtroendet som en prenumeration har för en katalog skiljer sig från relationen som den har med andra resurser i Azure (webbplatser, databaser och så vidare). Om en prenumeration går ut stoppas även åtkomsten till de övriga resurser som är associerade med prenumerationen. Men en Azure Active Directory-katalog finns kvar i Azure och du kan associera en annan prenumeration med katalogen och hantera kataloganvändarna med den nya prenumerationen.

![diagram över hur prenumerationer är associerade](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Alla användare har en enda arbetskatalog som autentiserar dem, men de kan även vara gäster i andra kataloger. I Azure Active Directory kan du bara se de kataloger som ditt användarkonto är medlem eller gäst i.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD och molntjänstprenumerationer
Azure AD innehåller de grundläggande katalog- och identitetshanteringsfunktionerna bakom de flesta av Microsofts molntjänster, inklusive:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Du får Azure AD-tjänsten kostnadsfritt när du registrerar dig för någon av dessa Microsoft-molntjänster. Om du vill lägga till ytterligare en Azure-prenumeration för en Azure Active Directory-katalog måste du vara inloggad med ett Microsoft-konto. Om du loggar in på Azure med ett arbets- eller skolkonto kan du inte lägga till en Azure-prenumeration för en befintlig katalog eftersom ditt arbets- eller skolkonto inte kan autentiseras direkt av Azure. 

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Så här lägger du till en befintlig prenumeration i din Azure Active Directory-katalog
Du måste logga in med ett konto som finns i både den aktuella katalogen som prenumerationen är associerad till och i den katalog som du vill lägga till den i. 

1. Logga in i [Azure-kontocentret](https://account.windowsazure.com/Home/Index) med ett konto som är kontoadministratör för prenumerationen vars äganderätt du vill överföra.
2. Se till att den användare som ska vara prenumerationsägare finns i målkatalogen.
3. Klicka på **överföra äganderätten till prenumerationen**.
4. Ange mottagaren. Mottagaren hämtar automatiskt ett e-postmeddelande med en länk för godkännande.
5. Mottagaren klickar på länken och följer instruktionerna, inklusive att ange sin betalningsinformation. När mottagaren lyckas överförs prenumerationen. 
6. Standardkatalogen för prenumerationen ändras till den katalog där användaren finns.


## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Förslag för att hantera både en prenumeration och en katalog
De administrativa rollerna för en Azure-prenumeration hanterar resurser som är knutna till Azure-prenumerationen. I det här avsnittet beskrivs skillnaderna mellan Azure-prenumerationsadministratörer och Azure AD-katalogadministratörer. Administrativa roller och andra förslag om att använda dem för att hantera din prenumeration beskrivs i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles.md).

Som standard tilldelas du rollen som tjänstadministratör när du registrerar dig. Om andra behöver logga in och komma åt tjänster med samma prenumeration kan du lägga till dem som medadministratörer. 

Azure AD har en annan uppsättning administrativa roller för att hantera katalog- och identitetsrelaterade funktioner. Exempelvis kan den globala administratören för en katalog lägga till användare och grupper i katalogen eller kräver Multi-Factor Authentication för användare. En användare som skapar en katalog tilldelas rollen som global administratör och kan tilldela administrativa roller till andra användare. Administrativa roller i Azure AD används också av andra tjänster som Office 365 och Microsoft Intune. 

Azure-prenumerationsadministratörer och Azure AD-katalogadministratörer är två separata roller. 
* Azure-prenumerationsadministratörer kan hantera resurser i Azure och kan använda Azure AD på Azure-portalen (eftersom själva Azure-portalen är en Azure-resurs). 
* Katalogadministratörer kan endast hantera egenskaper i Azure AD-katalogen.

En person kan ha båda rollerna men det är inget krav. En global katalogadministratör kanske inte kan tilldelas rollen som tjänstadministratör eller medadministratör för en Azure-prenumeration eller tvärtom. Utan att vara administratör för prenumerationen kan användaren logga in på Azure-portalen, men kan inte hantera katalogerna för den prenumerationen på portalen. Den här användaren kan emellertid hantera kataloger med andra verktyg, till exempel Azure Active Directory PowerShell eller administrationscentret för Office 365.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du ändrar administratörer för en Azure-prenumeration finns i [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](active-directory-understanding-resource-access.md)
* Mer information om hur du tilldelar roller i Azure AD finns i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

