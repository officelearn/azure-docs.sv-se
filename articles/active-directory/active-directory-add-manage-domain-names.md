---
title: "Hantera anpassade domännamn i Azure Active Directory | Microsoft Docs"
description: "Management-begrepp och instruktioner för att hantera anpassade domäner i Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cf3523bd-9ee0-439e-963d-ccea038867b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 5ae19bb370064de96cf466ca09b13d02563d65a4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Hantera anpassade domännamn i Azure Active Directory
Ett domännamn kan vara en viktig identifierare för många directory-resurser som en del av:

* Ett användarnamn eller e-postadress för en användare
* Adressen för en grupp
* App-ID URI för ett program

En resurs i Azure Active Directory (Azure AD) kan innehålla ett domännamn som redan har verifierats som ägs av den katalog som innehåller resursen. Endast en global administratör kan utföra hanteringsuppgifter för domänen i Azure AD.

> [!IMPORTANT]
> Microsoft rekommenderar att du hanterar Azure AD via [Azure AD administratörscenter](https://aad.portal.azure.com) på Azure Portal istället för via den klassiska Azure-portalen som nämns i den här artikeln. Att hantera dina domännamn i Azure AD-administrationscentret, se [hantera anpassade domännamn i Azure Active Directory](active-directory-domains-manage-azure-portal.md).

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Ange namnet på primär domän för din Azure AD-katalog
När din katalog har skapats, är det ursprungliga domännamnet, till exempel contoso.onmicrosoft.com, också primära domännamnet för din katalog. Den primära domänen är standarddomännamnet för en ny användare när du skapar en ny användare i den [klassiska Azure-portalen](https://manage.windowsazure.com/), eller andra portaler, till exempel administrationsportalen för Office 365. Detta förenklar processen för en administratör för att skapa nya användare i portalen.

Ändra det primära domännamnet för din katalog:

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com/) med ett användarkonto som är en global administratör i Azure AD-katalogen.
2. Välj **Active Directory** i det vänstra navigeringsfältet.
3. Öppna din katalog.
4. Välj den **domäner** fliken.
5. Välj den **ändra primära** knappen i kommandofältet.
6. Välj den domän som du vill att den nya primära domänen för din katalog.

Du kan ändra det primära domännamnet för din katalog ska alla verifierade anpassade domäner som inte är federerat. Primär domän för din katalog kommer ändra användarnamn för eventuella befintliga användare.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Lägg till anpassade domännamn i din Azure AD
Du kan lägga till upp till 900 anpassade domännamn varje Azure AD-katalog. Processen för att [lägga till ytterligare ett eget domännamn](active-directory-add-domain.md) är densamma för det första anpassade domännamnet.

## <a name="add-subdomains-of-a-custom-domain"></a>Lägger till underdomäner i en anpassad domän
Om du vill lägga till en tredje nivån domännamn, till exempel 'europe.contoso.com' i katalogen bör du först lägga till och kontrollera andranivådomän, t.ex contoso.com. Underdomänen verifieras automatiskt av Azure AD. Om du vill se underdomänen som du just lagt har verifierats, uppdatera sidan i webbläsaren visas domänerna i katalogen.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Vad du gör om du ändrar DNS-registratorns för ditt domännamn
Om du ändrar DNS-registratorns för ditt domännamn kan fortsätta du att använda ditt domännamn med Azure AD själva utan avbrott och utan ytterligare konfigurationsåtgärder. Om du använder ditt domännamn med Office 365, Intune eller andra tjänster som är beroende av anpassade domännamn i Azure AD finns i dokumentationen för dessa tjänster.

## <a name="delete-a-custom-domain-name"></a>Ta bort ett anpassat domännamn
Du kan ta bort ett anpassat domännamn från din Azure AD, om organisationen inte längre använder domännamnet, eller om du behöver använda domännamnet med en annan Azure AD.

Om du vill ta bort ett anpassat domännamn måste kontrollera du först att inga resurser i din katalog är beroende av domännamnet. Du kan inte ta bort ett domännamn från katalogen om:

* Alla användare har ett användarnamn, e-postadress eller proxyadress som inkluderar domännamnet.
* Valfri grupp har en e-postadress eller proxyadress som innehåller namnet på en domän.
* Alla program i din Azure AD har en app-ID-URI som innehåller namnet på en domän.

Du måste ändra eller ta bort alla dessa resurser i Azure AD-katalogen innan du kan ta bort det anpassade domännamnet.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Använd PowerShell eller Graph API för att hantera domännamn
De flesta hanteringsuppgifter för domännamn i Azure Active Directory kan också utföras med Microsoft PowerShell eller via programmering med Azure AD Graph API.

* [Använda PowerShell för att hantera domännamn i Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Hantera domännamn i Azure AD med hjälp av Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Nästa steg
* [Lär dig mer om domännamn i Azure AD](active-directory-add-domain-concepts.md)
* [Hantera egna domännamn](active-directory-add-manage-domain-names.md)

