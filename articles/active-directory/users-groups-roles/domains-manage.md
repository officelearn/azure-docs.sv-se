---
title: Lägga till och verifiera anpassade domännamn – Azure Active Directory | Microsoft-dokument
description: Hanteringskoncept och instruktioner för att hantera ett domännamn i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2395aa5984de2a9fe41e4778d16aba69bfef5192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559241"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Hantera anpassade domännamn i Din Active Directory i Azure

Ett domännamn är en viktig del av identifieraren för många katalogresurser: det är en del av ett användarnamn eller en e-postadress för en användare, en del av adressen för en grupp och är ibland en del av app-ID-URI för ett program. En resurs i Azure Active Directory (Azure AD) kan innehålla ett domännamn som ägs av katalogen som innehåller resursen. Endast en global administratör kan hantera domäner i Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Ange det primära domännamnet för din Azure AD-katalog

När katalogen skapas är det ursprungliga domännamnet, till exempel "contoso.onmicrosoft.com", också det primära domännamnet. Den primära domänen är standarddomännamnet för en ny användare när du skapar en ny användare. Om du anger ett primärt domännamn effektiviseras processen för att en administratör ska kunna skapa nya användare i portalen. Så här ändrar du det primära domännamnet:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Azure Active Directory**.
3. Välj **Egna domännamn**.
  
   ![Öppna sidan för användarhantering](./media/domains-manage/add-custom-domain.png)
4. Välj namnet på den domän som du vill ska vara den primära domänen.
5. Markera kommandot **Gör primärt.** Bekräfta ditt val när du uppmanas att göra det.
  
   ![Gör ett domännamn till det primära](./media/domains-manage/make-primary-domain.png)

Du kan ändra det primära domännamnet för katalogen så att det blir en verifierad anpassad domän som inte är federerad. Om du ändrar den primära domänen för katalogen ändras inte användarnamnet för befintliga användare.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Lägga till anpassade domännamn i din Azure AD-klient

Du kan lägga till upp till 900 hanterade domännamn. Om du konfigurerar om alla dina domäner för federation med lokal Active Directory kan du lägga till upp till 450 domännamn i varje katalog.

## <a name="add-subdomains-of-a-custom-domain"></a>Lägga till underdomäner för en anpassad domän

Om du vill lägga till ett domännamn på tredje nivån, till exempel "europe.contoso.com" i katalogen, bör du först lägga till och verifiera domänen på andra nivån, till exempel contoso.com. Underdomänen verifieras automatiskt av Azure AD. Om du vill se att underdomänen du har lagt till är verifierad uppdaterar du domänlistan i webbläsaren.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Så här gör du om du ändrar DNS-registratorn för ditt eget domännamn

Om du ändrar DNS-registratorer finns det inga ytterligare konfigurationsuppgifter i Azure AD. Du kan fortsätta använda domännamnet med Azure AD utan avbrott. Om du använder ditt anpassade domännamn med Office 365, Intune eller andra tjänster som är beroende av anpassade domännamn i Azure AD läser du dokumentationen för dessa tjänster.

## <a name="delete-a-custom-domain-name"></a>Ta bort ett eget domännamn

Du kan ta bort ett anpassat domännamn från din Azure AD om din organisation inte längre använder domännamnet, eller om du behöver använda domännamnet med en annan Azure AD.

Om du vill ta bort ett eget domännamn måste du först se till att inga resurser i katalogen är beroende av domännamnet. Du kan inte ta bort ett domännamn från katalogen om:

* Alla användare har ett användarnamn, en e-postadress eller en proxyadress som innehåller domännamnet.
* Alla grupper har en e-postadress eller proxyadress som innehåller domännamnet.
* Alla program i din Azure AD har ett app-ID URI som innehåller domännamnet.

Du måste ändra eller ta bort en sådan resurs i din Azure AD-katalog innan du kan ta bort det anpassade domännamnet.

### <a name="forcedelete-option"></a>Alternativet ForceDelete

Du kan **tvinga bort** ett domännamn i Azure AD [Admin Center](https://aad.portal.azure.com) eller använda Microsoft [Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). De här alternativen använder en asynkron åtgärd och uppdaterar allauser@contoso.comreferenser från det anpassade domännamnet som " " till det ursprungliga standarddomännamnet som "user@contoso.onmicrosoft.com." 

Om du vill anropa **ForceDelete** i Azure-portalen måste du se till att det finns färre än 1 000 referenser till domännamnet, och alla referenser där Exchange är etableringstjänsten måste uppdateras eller tas bort i [Administrationscenter](https://outlook.office365.com/ecp/)för Exchange . Detta inkluderar Exchange Mail-aktiverade säkerhetsgrupper och distribuerade listor. Mer information finns i [Ta bort e-postaktiverade säkerhetsgrupper](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). **ForceDelete-åtgärden** lyckas inte heller om något av följande är sant:

* Du har köpt en domän via prenumerationstjänster för Office 365-domän
* Du är en partner som administrerar på uppdrag av en annan kund hyresgäst

Följande åtgärder utförs som en del av **ForceDelete-åtgärden:**

* Byter namn på UPN, EmailAddress och ProxyAddress för användare med referenser till det anpassade domännamnet till det ursprungliga standarddomännamnet.
* Byter namn på e-postadressen för grupper med referenser till det anpassade domännamnet till det ursprungliga standarddomännamnet.
* Byter namn på identifierare för program med referenser till det anpassade domännamnet till det ursprungliga standarddomännamnet.

Ett fel returneras när:

* Antalet objekt som ska bytas namn är större än 1000
* Ett av programmen som ska döpas om är en app med flera innehavare

### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Varför misslyckas domänborttagningen med ett fel som anger att jag har Exchange-behärskade grupper i det här domännamnet?** <br>
**A.** Idag etableras vissa grupper som E-postaktiverade säkerhetsgrupper och distribuerade listor av Exchange och måste rensas manuellt i [Exchange Admin Center (EAC).](https://outlook.office365.com/ecp/) Det kan finnas kvardröjande ProxyAddresses som förlitar sig på det anpassade domännamnet och måste uppdateras manuellt till ett annat domännamn. 

**F: Jag är inloggad\@som admin contoso.com men jag kan inte ta bort domännamnet "contoso.com"?**<br>
**A.** Du kan inte referera till det anpassade domännamn som du försöker ta bort i ditt användarkontonamn. Kontrollera att det globala administratörskontot använder det ursprungliga standarddomännamnet (.onmicrosoft.com) till exempel admin@contoso.onmicrosoft.com. Logga in med ett annat globalt admin@contoso.onmicrosoft.com administratörskonto som eller ett annat anpassat domännamn admin@fabrikam.comsom "fabrikam.com" där kontot finns .

**F: Jag klickade på knappen `In Progress` Ta bort domän och ser status för borttagningsåtgärden. Hur lång tid tar det? Vad händer om det misslyckas?**<br>
**A.** Borttagningsdomänåtgärden är en asynkron bakgrundsaktivitet som byter namn på alla referenser till domännamnet. Det bör slutföras inom en minut eller två. Om domänborttagning misslyckas, se till att du inte har:

* Appar som konfigurerats på domännamnet med appIdentifierURI
* Alla e-postaktiverade grupper som refererar till det anpassade domännamnet
* Mer än 1000 referenser till domännamnet

Om du upptäcker att något av villkoren inte har uppfyllts rensar du referenserna manuellt och försöker ta bort domänen igen.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Använda PowerShell eller Microsoft Graph API för att hantera domännamn

De flesta hanteringsuppgifter för domännamn i Azure Active Directory kan också slutföras med Microsoft PowerShell eller programmässigt med Hjälp av Microsoft Graph API.

* [Använda PowerShell för att hantera domännamn i Azure AD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Domänresurstyp](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>Nästa steg

* [Lägga till anpassade domännamn](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Ta bort Exchange-e-postaktiverade säkerhetsgrupper i Administrationscenter för Exchange på ett anpassat domännamn i Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete ett eget domännamn med Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
