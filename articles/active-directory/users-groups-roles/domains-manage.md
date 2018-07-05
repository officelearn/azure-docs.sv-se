---
title: Hantera anpassade domännamn i Azure Active Directory | Microsoft Docs
description: Management-relaterade begrepp och instruktioner för att hantera ett domännamn i Azure Active Directory
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
ms.openlocfilehash: b503ef4c5cd922d4f7b58940cfc98a83a78ae986
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450319"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Hantera anpassade domännamn i Azure Active Directory
Ett domännamn är en viktig del av identifieraren för många katalogresurser: det är en del av ett användarnamn eller e-postadress för en användare, en del av adressen för en grupp, och kan vara en del av app-ID-URI för ett program. En resurs i Azure Active Directory (Azure AD) kan innehålla ett domännamn som redan har verifierats som ägs av katalogen som innehåller resursen. Endast en global administratör kan utföra hanteringsåtgärder för domänen i Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Ange namnet på primär domän för din Azure AD-katalog
När katalogen har skapats är det ursprungliga domännamnet, t.ex contoso.onmicrosoft.com, även namnet på primära domänen. Den primära domänen är standard-domännamnet för en ny användare när du skapar en ny användare. Ange namn på en primär domän effektiviserar processen för en administratör att skapa nya användare i portalen. Så här ändrar du namnet på primära domänen:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Azure Active Directory**.
3. Välj **anpassade domännamn**.
     
   ![Öppna användarhantering](./media/domains-manage/add-custom-domain.png)
4. Välj namnet på den domän som du vill ska vara den primära domänen.
5. Välj den **göra primärt** kommando. Bekräfta valet när du tillfrågas.
   
   ![Göra ett domännamn primärt](./media/domains-manage/make-primary-domain.png)

Du kan ändra det primära domännamnet för din katalog är en verifierad anpassad domän som inte är federerat. Ändra den primära domänen för din katalog ändras inte användarnamnen för alla befintliga användare.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Lägger till anpassade domännamn i Azure AD-klienten
Du kan lägga till upp till 900 hanterade domännamn. Om du konfigurerar alla domäner för federering med lokala Active Directory kan du lägga till till högst 450 domännamn i varje katalog. Mer information finns i [federerade och hanterade domännamn](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Lägg till underdomäner i en anpassad domän
Om du vill lägga till en tredje nivån domännamn, till exempel 'europe.contoso.com' i katalogen ska du först lägga till och verifiera domänen på den andra nivån, till exempel contoso.com. Underdomänen verifieras automatiskt av Azure AD. Uppdatera sidan i webbläsaren som visar domänerna om du vill se att underdomänen som du just lade till har verifierats.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Vad du gör om du ändrar DNS-registratorns för ditt anpassade domännamn
Om du ändrar DNS-registratorns för ditt anpassade domännamn kan du fortsätta att använda ditt anpassade domännamn med själva Azure AD utan avbrott och utan ytterligare konfigurationsåtgärder. Om du använder ett anpassat domännamn med Office 365, Intune eller andra tjänster som förlitar sig på anpassade domännamn i Azure AD finns i dokumentationen för dessa tjänster.

## <a name="delete-a-custom-domain-name"></a>Ta bort ett anpassat domännamn
Du kan ta bort ett anpassat domännamn från din Azure AD, om din organisation inte längre använder domännamnet, eller om du behöver använda det domännamnet med en annan Azure AD.

Om du vill ta bort ett anpassat domännamn, måste du först kontrollera att inga resurser i din katalog är beroende av domännamnet. Du kan inte ta bort ett domännamn från din katalog om:

* Alla användare har ett användarnamn, e-postadress eller proxyadress som innehåller domännamnet.
* En grupp har en e-postadress eller proxyadress som innehåller domännamnet.
* Alla program i din Azure AD har en app-ID-URI som innehåller domännamnet.

Du måste ändra eller ta bort alla sådana resurser i Azure AD-katalogen innan du kan ta bort det anpassade domännamnet.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Använd PowerShell eller Graph API för att hantera domännamn
De flesta hanteringsuppgifter för domännamn i Azure Active Directory kan också göras med hjälp av Microsoft PowerShell eller via programmering med hjälp av Azure AD Graph API.

* [Använda PowerShell för att hantera domännamn i Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Med Graph API för att hantera domännamn i Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Nästa steg
* [Lägga till anpassade domännamn](../fundamentals/add-custom-domain.md)

