---
title: Jämför standardbehörigheterna för användare i Azure Active Directory | Microsoft Docs
description: Jämför medlem, Gäst, app ägare och gruppbehörigheter ägare
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/29/2018
ms.author: curtand
ms.reviewer: vincesm
ms.openlocfilehash: a9bf9748de5f390f95b8b672e0cf77afa5c49581
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="default-user-permissions-in-azure-active-directory"></a>Standardbehörigheterna för användare i Azure Active Directory

Alla användare som beviljas en uppsättning standardbehörigheter som i Azure Active Directory (AD Azure). En användares behörighet består typ av användare, deras [rollmedlemskap](https://docs.microsoft.com/azure/active-directory/active-directory-users-assign-role-azure-portal), och deras ägarskap för enskilda objekt. Den här artikeln beskriver dessa standardbehörigheter och innehåller en jämförelse av medlem och Gäst användarens standardinställningar.

## <a name="member-and-guest-users"></a>Medlemmen och Gäst-användare
Behörigheter som standard emot beror på om användaren är medlem interna innehavarens (medlemsanvändare) eller om användaren är B2B-samarbete gäst (gästanvändaren). Läs mer om B2B-samarbete [vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md) mer information om gästanvändare). 
* Användare kan registrera program, hantera sina egna profil foto och mobila telefonnummer många, ändra sina egna lösenord och bjuda in B2B gäster. Dessutom kan kan användare läsa all kataloginformation (med några få undantag). 
* Azure AD B2B-gästanvändare har begränsade behörigheter för katalogen. Gästanvändare kan inte bläddra till exempel information från innehavaren utanför sin egen profilinformation. Gästanvändare kan hämta information om en annan användare genom att tillhandahålla User Principal Name eller objectId. Gäst kan inte se någon information om andra klient-objekt, till exempel grupper och program.

Standardbehörigheter för gäster är begränsade som standard. Gäster kan läggas till administratörsroller som beviljar dem fullständig Läs- och skrivbehörighet i rollen. Det finns en ytterligare begränsning tillgängliga möjligheten för gäster att bjuda in andra gäster. Ange **gäster kan bjuda in** till **nr** förhindrar att gäster bjuda in andra gäster. Se [delegera inbjudningar för B2B-samarbete](active-directory-b2b-delegate-invitations.md) att lära dig hur. Om du vill ge gästanvändare samma behörigheter som användare som standard ange **gäst användarnas behörigheter begränsas** till **nr**. Den här inställningen ger alla medlem användarbehörighet att gästanvändare som standard, samt att gäster som ska läggas till administrativa roller.

## <a name="compare-member-and-guest-default-permissions"></a>Jämför medlem och Gäst standardbehörigheter

**Område** | **Behörigheter för medlemmen** | **Gästen användarbehörigheter**
------------ | --------- | ----------
Användare och kontakter | Läsa alla offentliga egenskaper för användare och kontakter<br>Bjud in gäster<br>Ändra eget lösenord<br>Hantera egna mobiltelefonnummer<br>Hantera egna foto<br>Ogiltigförklara egna uppdaterings-tokens | Läsa egna egenskaper<br>Läs visningsnamn, e-post, inloggningsnamn, foto, användarens huvudnamn och användaregenskaper typ av andra användare och kontakter<br>Ändra eget lösenord
Grupper   | Skapa säkerhetsgrupper<br>Skapa grupper för Office 365<br>Läsa alla egenskaper i grupper<br>Läsa icke-dolda gruppmedlemskap<br>Läsa dolda Office 365-gruppmedlemskap för domänanslutna grupp<br>Hantera egenskaper, ägarskap och medlemskap i grupper som företagsägda<br>Lägga till gäster i företagsägda grupper<br>Hantera inställningar för dynamiskt medlemskap<br>Ta bort företagsägda grupper<br>Återställning som ägs av Office 365-grupper | Läsa alla egenskaper i grupper<br>Läsa icke-dolda gruppmedlemskap<br>Läs dolda medlemskap i Office 365 för sammanslagna grupper<br>Hantera företagsägda grupper<br>Lägg till gäster företagsägda grupper (om tillåts)<br>Ta bort företagsägda grupper<br>Återställning som ägs av Office 365-grupper           
Program | Registrera (skapa) nytt program<br>Skrivskyddade egenskaper för registrerats och företagsprogram<br>Hantera egenskaper för program, tilldelningar och autentiseringsuppgifter för företagsägda program<br>Skapa eller ta bort programmet lösenord för användare<br>Ta bort företagsägda program<br>Återställa företagsägda program | Skrivskyddade egenskaper för registrerats och företagsprogram<br>Hantera egenskaper för program, tilldelningar och autentiseringsuppgifter för företagsägda program<br>Ta bort företagsägda program<br>Återställa företagsägda program
Enheter | Läsa alla egenskaper för enheter<br>Hantera alla egenskaper för företagsägda enheter<br> | Inga behörigheter<br>Ta bort företagsägda enheter<br>
Katalog | Läsa alla företagsinformation<br>Läsa alla domäner<br>Läsa alla partner kontrakt | Läsa visningsnamn och verifierats domäner
Roller och omfattningar | Läsa alla administrativa roller och medlemskap<br>Läsa alla egenskaper och medlemskap i administrativa enheter | Inga behörigheter              
Prenumerationer | Läsa alla prenumerationer<br>Aktivera tjänsten Plan medlem | Inga behörigheter
Principer | Läsa alla egenskaper för principer<br>Hantera alla egenskaper för företagsägda princip | Inga behörigheter

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Att begränsa standardbehörigheter för användare


Standardbehörigheter för medlemmen som användare kan begränsas på följande sätt. Mer information finns i [appar, behörigheter och medgivande i Azure Active Directory](active-directory-apps-permissions-consent.md).

Behörighet | Inställningen förklaring
---------- | ------------
Möjlighet att skapa säkerhetsgrupper | Ange det här alternativet Nej hindrar användare från att skapa säkerhetsgrupper. Globala administratörer eller användare konto kan du fortfarande skapa säkerhetsgrupper. Se [Azure Active Directory-cmdlets för att konfigurera inställningarna för](active-directory-accessmanagement-groups-settings-cmdlets.md) att lära dig hur.
Möjligheten att skapa grupper för Office 365 | Ange det här alternativet Nej hindrar användare från att skapa grupper för Office 365. Det här alternativet kan för vissa en utvald grupp användare att skapa grupper för Office 365. Globala administratörer eller användare konto kommer fortfarande att kunna skapa grupper för Office 365. Se [Azure Active Directory-cmdlets för att konfigurera inställningarna för](active-directory-accessmanagement-groups-settings-cmdlets.md) att lära dig hur.
Möjlighet att samtycka till program | Ange det här alternativet Nej hindrar användare från principer för program. Globala administratörer kommer fortfarande att kunna samtycka till program. Mer information finns i [appar, behörigheter och medgivande i Azure Active Directory](active-directory-apps-permissions-consent.md).
Möjligheten att lägga till appar i galleriet åtkomstpanelen | Ange det här alternativet Nej förhindrar att användare lägger till galleriet appar sina åtkomstpanelen.
Möjligheten att registrera (skapa) program | Ange det här alternativet Nej förhindrar att icke-administratörer att skapa program. Globala administratörer kommer fortfarande att kunna skapa program. Mer information finns i [appar, behörigheter och medgivande i Azure Active Directory](active-directory-apps-permissions-consent.md).
Administratörer och användare med gästrollen bjuder in bjuda in gäster | Ange det här alternativet Nej förhindrar att alla användare uppmanar gästerna. Se Konfigurera standardbehörigheter för användare. Mer information finns i [appar, behörigheter och medgivande i Azure Active Directory](active-directory-apps-permissions-consent.md).
Medlemmar kan bjuda in gäster | Ange detta till ingen förhindrar att användare uppmanar gästerna. Globala administratörer, användare Kontoadministratörer och Gäst Inviters kommer fortfarande att kunna bjuda in gäster. Mer information finns i [appar, behörigheter och medgivande i Azure Active Directory](active-directory-apps-permissions-consent.md).
Begränsa åtkomst till Azure AD-administrationsportalen | Ange det här alternativet Nej förhindrar användare från att komma åt Azure Active Directory-portalen.
Möjlighet att läsa andra användare | Den här inställningen är endast tillgängligt i PowerShell. Att $false förhindrar att alla icke-administratörer läsa information för användare i katalogen. Detta förhindrar inte att läsa information för användare i andra Microsoft-tjänster som Exchange Online. Den här inställningen är avsedd för särskilda omständigheter och inställningen detta $false inte rekommenderas.

## <a name="object-ownership"></a>Objektägarskap

### <a name="application-registration-owner-permissions"></a>Programbehörigheter registrering ägare
När en användare som registrerar ett program, läggs de automatiskt som ägare till programmet. De kan hantera metadata för programmet som en ägare, till exempel namn och behörigheter appen manuellt. De kan också hantera klient-specifik konfiguration av program, till exempel SSO konfigurations- och tilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera program som de äger. Om du vill tilldela en ägare för registrering av program, se [Azure Active Directory-appregistrering](active-directory-app-registration.md).

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Ägare gruppbehörigheter

När en användare skapar en grupp, läggs de automatiskt som en ägare till gruppen. Som ägare, kan de hantera egenskaper för gruppen, till exempel namn, samt hantera medlemskap. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer och användare Kontoadministratörer kan ägare bara hantera grupper som de äger. Om du vill tilldela en gruppägare finns [hantera ägare för en grupp](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du ändrar administratörer för en Azure-prenumeration finns i [Lägga till eller ändra Azure-administratörsroller](../billing-add-change-azure-subscription-administrator.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns [hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera användare](active-directory-create-users.md)
