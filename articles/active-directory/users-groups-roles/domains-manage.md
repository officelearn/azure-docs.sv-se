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
ms.date: 10/05/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: d5f926ac41bb90ba716e0c52b790a60fd74e0631
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854923"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Hantera anpassade domännamn i Azure Active Directory

Ett domännamn är en viktig del av identifieraren för många katalogresurser: det är en del av ett användarnamn eller e-postadress för en användare, en del av adressen för en grupp, och ibland är en del av app-ID-URI för ett program. En resurs i Azure Active Directory (Azure AD) kan innehålla ett domännamn som ägs av den katalog som innehåller resursen. Endast en Global administratör kan hantera domäner i Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Ange namnet på primär domän för din Azure AD-katalog

När katalogen har skapats är det ursprungliga domännamnet, t.ex contoso.onmicrosoft.com, även namnet på primära domänen. Den primära domänen är standard-domännamnet för en ny användare när du skapar en ny användare. Ange namn på en primär domän effektiviserar processen för en administratör att skapa nya användare i portalen. Så här ändrar du namnet på primära domänen:

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en Global administratör för katalogen.
2. Välj **Azure Active Directory**.
3. Välj **Egna domännamn**.
  
   ![Öppna användarhantering](./media/domains-manage/add-custom-domain.png)
4. Välj namnet på den domän som du vill ska vara den primära domänen.
5. Välj den **göra primärt** kommando. Bekräfta valet när du tillfrågas.
  
   ![Göra ett domännamn primärt](./media/domains-manage/make-primary-domain.png)

Du kan ändra det primära domännamnet för din katalog är en verifierad anpassad domän som inte är federerat. Ändra den primära domänen för din katalog, ändras inte användarnamnet för alla befintliga användare.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Lägger till anpassade domännamn i Azure AD-klienten

Du kan lägga till upp till 900 hanterade domännamn. Om du konfigurerar alla domäner för federering med lokala Active Directory, kan du lägga till upp till 450 domännamn i varje katalog.

## <a name="add-subdomains-of-a-custom-domain"></a>Lägg till underdomäner i en anpassad domän

Om du vill lägga till en tredje nivån domännamn, till exempel 'europe.contoso.com' i katalogen ska du först lägga till och verifiera domänen på den andra nivån, till exempel contoso.com. Underdomänen verifieras automatiskt av Azure AD. Uppdatera domänlistan i webbläsaren för att se att du har lagt till underdomänen är verifierad.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Vad du gör om du ändrar DNS-registratorns för ditt anpassade domännamn

Om du ändrar DNS-registratorer, finns det inga ytterligare konfigurationer i Azure AD. Du kan fortsätta med domännamnet med Azure AD utan avbrott. Om du använder ett anpassat domännamn med Office 365, Intune eller andra tjänster som förlitar sig på anpassade domännamn i Azure AD finns i dokumentationen för dessa tjänster.

## <a name="delete-a-custom-domain-name"></a>Ta bort ett anpassat domännamn

Du kan ta bort ett anpassat domännamn från din Azure AD, om din organisation inte längre använder domännamnet, eller om du behöver använda det domännamnet med en annan Azure AD.

Om du vill ta bort ett anpassat domännamn, måste du först kontrollera att inga resurser i din katalog är beroende av domännamnet. Du kan inte ta bort ett domännamn från din katalog om:

* Alla användare har ett användarnamn, e-postadress eller proxyadress som innehåller domännamnet.
* En grupp har en e-postadress eller proxyadress som innehåller domännamnet.
* Alla program i din Azure AD har en app-ID-URI som innehåller domännamnet.

Du måste ändra eller ta bort alla sådana resurser i Azure AD-katalogen innan du kan ta bort det anpassade domännamnet.

### <a name="forcedelete-option"></a>ForceDelete alternativet

Du kan **ForceDelete** ett domännamn i den [Azure AD-administrationscentret](https://aad.portal.azure.com) eller med hjälp av [Microsoft Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/domain_forcedelete). Dessa alternativ använder en asynkron åtgärd och uppdatera alla referenser från det anpassade domännamnet som ”user@contoso.com” så här det inledande standarddomännamnet som ”user@contoso.onmicrosoft.com”. 

Att anropa **ForceDelete** Azure-portalen måste du se till att det finns färre än 1 000 referenser till domännamnet och alla referenser där Exchange är etableringstjänsten måste uppdateras eller tas bort i den [ Administrationscenter för Exchange](https://outlook.office365.com/ecp/). Detta inkluderar Exchange Mail-Enabled säkerhetsgrupper och distribuerade listor. Mer information finns i [tar bort e-postaktiverade säkerhetsgrupper](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Dessutom den **ForceDelete** åtgärden inte lyckas om något av följande stämmer:

* Du har köpt en domän via Office 365-prenumeration domäntjänster
* Du är en partner administrera åt en annan kundklient

Följande åtgärder utförs som en del av den **ForceDelete** igen:

* Byter namn på de UPN, e-postadress och ProxyAddress av användare med referenser till det anpassade domännamnet till det ursprungliga standarddomännamnet.
* Byter namn på e-postadress i grupper med referenser till det anpassade domännamnet till det ursprungliga standarddomännamnet.
* Byter namn på identifieruris finns av program med referenser till det anpassade domännamnet till det ursprungliga standarddomännamnet.

Ett fel returneras när:

* Antalet objekt som ska byta namn är fler än 1 000
* En av de program som ska ändras är en app för flera klienter

### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Varför misslyckas domänborttagning med ett fel som anger att jag har lärt dig att Exchange-grupper på det här domännamnet?** <br>
**S:** idag vissa grupper som brevlåda säkerhetsgrupper och distribuerade listor har etablerats av Exchange och behöver rengöras manuellt i [Exchange Admin Center (UK)](https://outlook.office365.com/ecp/). Det kan kvarstående ProxyAddresses som förlitar sig på det anpassade domännamnet och måste uppdateras manuellt till ett annat domännamn. 

**F: Jag är inloggad som admin@contoso.com men jag kan inte ta bort domänen namnet ”contoso.com”?**<br>
**S:** du kan inte referera till det anpassade domännamnet som du försöker att ta bort i ditt användarkonto. Kontrollera att kontot som Global administratör använder den initiala standarddomännamnet (. onmicrosoft.com) som admin@contoso.onmicrosoft.com. Logga in med en annan Global administratör-konto som till exempel admin@contoso.onmicrosoft.com eller ett annat anpassat domännamn som ”fabrikam.com” där kontot som är admin@fabrikam.com.

**F: jag klickade på borttagningsknappen för domänen och se `In Progress` status för borttagningen. Hur lång tid tar det? Vad händer om den inte?**<br>
**S:** åtgärden ta bort domänen är en asynkron bakgrundsaktiviteten som byter namn på alla referenser till domännamnet. Det bör vara klart inom en minut. Om domänborttagning misslyckas, kontrollerar du att det inte finns:

* Appar som har konfigurerats på domännamnet med appIdentifierURI
* Alla e-postaktiverad grupp som refererar till det anpassade domännamnet
* Högst 1000 referenser till domännamnet

Om du upptäcker att något av villkoren inte har uppfyllts, manuellt Rensa referenserna och försök att ta bort domänen igen.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Använd PowerShell eller Graph API för att hantera domännamn

De flesta hanteringsuppgifter för domännamn i Azure Active Directory kan också göras med hjälp av Microsoft PowerShell eller via programmering med hjälp av Azure AD Graph API.

* [Använda PowerShell för att hantera domännamn i Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Med Graph API för att hantera domännamn i Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Nästa steg

* [Lägga till anpassade domännamn](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Ta bort Exchange-e-postaktiverade säkerhetsgrupper i administrationscentret för Exchange på ett anpassat domännamn i Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [Uppdatera program referens till en annan domän i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad#updating-an-application)
* [ForceDelete ett anpassat domännamn med Microsoft Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/domain_forcedelete)