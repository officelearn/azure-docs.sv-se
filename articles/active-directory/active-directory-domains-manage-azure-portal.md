---
title: Hantera anpassade domännamn i Azure Active Directory | Microsoft Docs
description: Management-begrepp och instruktioner för att hantera ett domännamn i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: 81c2371d5dbb17399071c80ff4e8b81813ed014c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762402"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Hantera anpassade domännamn i Azure Active Directory
Ett domännamn är en viktig del av identifierare för många katalogresurserna: det är en del av ett användarnamn eller e-postadress för en användare, en del av adressen för en grupp, och kan vara en del av app-ID URI för ett program. En resurs i Azure Active Directory (Azure AD) kan innehålla ett domännamn som redan har verifierats som ägs av katalogen som innehåller resursen. Endast en global administratör kan utföra hanteringsuppgifter för domänen i Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Ange namnet på primär domän för din Azure AD-katalog
När din katalog har skapats, är det ursprungliga domännamnet, till exempel contoso.onmicrosoft.com, också namnet på primära domänen. Den primära domänen är standarddomännamnet för en ny användare när du skapar en ny användare. Inställningsnamn primär domän förenklar processen för en administratör för att skapa nya användare i portalen. Ändra namnet på primära domänen:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Azure Active Directory**.
3. Välj **anpassade domännamn**.
     
   ![Öppna användarhantering](./media/active-directory-domains-manage-azure-portal/add-custom-domain.png)
4. Välj namnet på den domän som du vill ska vara den primära domänen.
5. Välj den **gör primär** kommando. Bekräfta valet när du uppmanas göra det.
   
   ![Gör ett domännamn primär](./media/active-directory-domains-manage-azure-portal/make-primary-domain.png)

Du kan ändra det primära domännamnet för din katalog ska alla verifierade anpassade domäner som inte är federerat. Primär domän för din katalog kommer ändra användarnamn för eventuella befintliga användare.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Lägg till anpassade domännamn i Azure AD-klient
Du kan lägga till upp till 900 hanterade domännamn. Du kan lägga till till högst 450 domännamn i varje katalog om du konfigurerar dina domäner för federering med lokala Active Directory. Mer information finns i [federerat och hanterade domännamn](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Lägger till underdomäner i en anpassad domän
Om du vill lägga till en tredje nivån domännamn, till exempel 'europe.contoso.com' i katalogen bör du först lägga till och kontrollera andranivådomän, t.ex contoso.com. Underdomänen verifieras automatiskt av Azure AD. Uppdatera sidan i webbläsaren visas domänerna om du vill se underdomänen som du just lagt har verifierats.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Vad du gör om du ändrar DNS-registratorns för ditt domännamn
Om du ändrar DNS-registratorns för ditt domännamn kan fortsätta du att använda ditt domännamn med Azure AD själva utan avbrott och utan ytterligare konfigurationsåtgärder. Om du använder ditt domännamn med Office 365, Intune eller andra tjänster som är beroende av anpassade domännamn i Azure AD finns i dokumentationen för dessa tjänster.

## <a name="delete-a-custom-domain-name"></a>Ta bort ett anpassat domännamn
Du kan ta bort ett anpassat domännamn från din Azure AD, om organisationen inte längre använder domännamnet, eller om du behöver använda domännamnet med en annan Azure AD.

Om du vill ta bort ett anpassat domännamn måste kontrollera du först att inga resurser i din katalog är beroende av domännamnet. Du kan inte ta bort ett domännamn från katalogen om:

* Alla användare har ett användarnamn, e-postadress eller proxyadress som innehåller namnet på en domän.
* Valfri grupp har en e-postadress eller proxyadress som innehåller namnet på en domän.
* Alla program i din Azure AD har en app-ID-URI som innehåller namnet på en domän.

Du måste ändra eller ta bort alla dessa resurser i Azure AD-katalogen innan du kan ta bort det anpassade domännamnet.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Använd PowerShell eller Graph API för att hantera domännamn
De flesta hanteringsuppgifter för domännamn i Azure Active Directory kan också utföras med Microsoft PowerShell eller via programmering med Azure AD Graph API.

* [Använda PowerShell för att hantera domännamn i Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Hantera domännamn i Azure AD med hjälp av Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Nästa steg
* [Lägga till anpassade domännamn](add-custom-domain.md)

