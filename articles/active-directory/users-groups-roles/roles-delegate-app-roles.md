---
title: Delegera programadministratörshanterings perms - Azure AD | Microsoft-dokument
description: Bevilja behörigheter för hantering av programåtkomst i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253044"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegera appregistreringsbehörigheter i Azure Active Directory

I den här artikeln beskrivs hur du använder behörigheter som beviljas av anpassade roller i Azure Active Directory (Azure AD) för att åtgärda dina programhanteringsbehov. I Azure AD kan du delegera behörigheter för att skapa och hantera program på följande sätt:

- [Begränsa vem som kan skapa program](#restrict-who-can-create-applications) och hantera de program de skapar. Som standard i Azure AD kan alla användare registrera programregistreringar och hantera alla aspekter av program som de skapar. Detta kan begränsas till att endast tillåta valda personer som behörighet.
- [Tilldela en eller flera ägare till ett program](#assign-application-owners). Det här är ett enkelt sätt att ge någon möjlighet att hantera alla aspekter av Azure AD-konfiguration för ett visst program.
- [Tilldela en inbyggd administrativ roll](#assign-built-in-application-admin-roles) som ger åtkomst till att hantera konfiguration i Azure AD för alla program. Det här är det rekommenderade sättet att ge IT-experter åtkomst till omfattande programkonfigurationsbehörigheter utan att bevilja åtkomst för att hantera andra delar av Azure AD som inte är relaterade till programkonfiguration.
- [Skapa en anpassad roll](#create-and-assign-a-custom-role-preview) som definierar mycket specifika behörigheter och tilldela den till någon antingen till omfattningen av ett enda program som en begränsad ägare eller i katalogomfånget (alla program) som en begränsad administratör.

Det är viktigt att överväga att bevilja åtkomst med hjälp av en av ovanstående metoder av två skäl. För det första minskar om du delegerar möjligheten att utföra administrativa uppgifter globala administratörskostnader. För det andra, med hjälp av begränsade behörigheter förbättrar din säkerhetsposition och minskar risken för obehörig åtkomst. Delegeringsproblem och allmänna riktlinjer diskuteras i [delegerad administration i Azure Active Directory](roles-concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Begränsa vem som kan skapa program

Som standard i Azure AD kan alla användare registrera programregistreringar och hantera alla aspekter av program som de skapar. Alla har också möjlighet att samtycka till appar som får åtkomst till företagsdata för deras räkning. Du kan välja att selektivt bevilja dessa behörigheter genom att ange de globala växlarna till Nej och lägga till de valda användarna i rollen Programutvecklare.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Så här inaktiverar du standardmöjligheten att skapa programregistreringar eller samtycke till program

1. Logga in på din Azure AD-organisation med ett konto som är kvalificerat för rollen Global administratör i din Azure AD-organisation.
1. Ange ett eller båda av följande:

    - På [sidan Användarinställningar för din organisation](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)anger du inställningen **Användare kan registrera program** till Nej. Detta inaktiverar standardmöjligheten för användare att skapa programregistreringar.
    - På [användarinställningarna för företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)anger du **att användarna kan godkänna program som använder företagsdata för deras räkning** och anger nej. Detta inaktiverar standardmöjligheten för användare att samtycka till program som använder företagsdata för deras räkning.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Bevilja individuella behörigheter för att skapa och godkänna program när standardmöjligheten är inaktiverad

Tilldela rollen Programutvecklare för att ge möjlighet att skapa programregistreringar när inställningen **Användare kan registrera program** är inställd på Nr. Den här rollen ger också behörighet att medgivande för ens egen räkning när **användarna kan samtycka till att appar som använder företagsdata för deras räkning** är inställt på Nej. När en användare skapar en ny programregistrering läggs de automatiskt till som den första ägaren när en användare skapar en ny programregistrering. Ägarbehörigheter ger användaren möjlighet att hantera alla aspekter av en programregistrering eller ett företagsprogram som de äger.

## <a name="assign-application-owners"></a>Tilldela programägare

Att tilldela ägare är ett enkelt sätt att ge möjligheten att hantera alla aspekter av Azure AD-konfiguration för en viss programregistrering eller företagsprogram. När en användare skapar en ny programregistrering läggs de automatiskt till som den första ägaren när en användare skapar en ny programregistrering. Ägarbehörigheter ger användaren möjlighet att hantera alla aspekter av en programregistrering eller ett företagsprogram som de äger. Den ursprungliga ägaren kan tas bort och ytterligare ägare kan läggas till.

### <a name="enterprise-application-owners"></a>Ägare av företagsprogram

Som ägare kan en användare hantera den organisationsspecifika konfigurationen av företagsprogrammet, till exempel konfigurationen för enkel inloggning, etablering och användartilldelningar. En ägare kan också lägga till eller ta bort andra ägare. Till skillnad från globala administratörer kan ägare bara hantera de företagsprogram de äger.

I vissa fall innehåller företagsprogram som skapats från programgalleriet både ett företagsprogram och en programregistrering. När detta är sant lägger du till en ägare i företagsprogrammet automatiskt ägaren till motsvarande programregistrering som ägare.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Så här tilldelar du en ägare till ett företagsprogram

1. Logga in på [din Azure AD-organisation](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är kvalificerat för programadministratören eller molnprogramadministratören för organisationen.
1. På  [sidan Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)för organisationen väljer du en app för att öppna sidan Översikt för appen.
1. Välj **Ägare** om du vill visa listan över ägare för appen.
1. Välj **Lägg till** om du vill välja en eller flera ägare som ska läggas till i appen.

> [!IMPORTANT]
> Användare och tjänstehuvudnamn kan vara ägare till programregistreringar. Endast användare kan vara ägare till företagsprogram. Grupper kan inte tilldelas som ägare av någon av dem.
>
> Ägare kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Programmet kan ha fler behörigheter än ägaren, och därmed skulle vara en behörighetshöjning över vad ägaren har åtkomst till som en användare eller tjänst huvudnamn. En programägare kan eventuellt skapa eller uppdatera användare eller andra objekt när de personifierar programmet, beroende på programmets behörigheter.

## <a name="assign-built-in-application-admin-roles"></a>Tilldela inbyggda programadministratörsroller

Azure AD har en uppsättning inbyggda administratörsroller för att bevilja åtkomst till hantering av konfiguration i Azure AD för alla program. Dessa roller är det rekommenderade sättet att ge IT-experter åtkomst till att hantera breda programkonfigurationsbehörigheter utan att bevilja åtkomst för att hantera andra delar av Azure AD som inte är relaterade till programkonfiguration.

- Programadministratör: Användare i den här rollen kan skapa och hantera alla aspekter av företagsprogram, programregistreringar och programproxyinställningar. Den här rollen ger också möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph. Användare som tilldelats den här rollen läggs inte till som ägare när nya programregistreringar eller företagsprogram skapas.
- Cloud Application Administrator: Användare i den här rollen har samma behörigheter som rollen Programadministratör, exklusive möjligheten att hantera programproxy. Användare som tilldelats den här rollen läggs inte till som ägare när nya programregistreringar eller företagsprogram skapas.

Mer information och om du vill visa beskrivningen för dessa roller finns i [Tillgängliga roller](directory-assign-admin-roles.md#available-roles).

Följ instruktionerna i [tilldela roller till användare med Azure Active Directory-instruktioner](../fundamentals/active-directory-users-assign-role-azure-portal.md) för att tilldela programadministratören eller molnprogramadministratörsrollerna.

> [!IMPORTANT]
> Programadministratörer och cloud-programadministratörer kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Programmet kan ha behörigheter som är en behörighetshöjning över administratörsrollens behörigheter. En administratör i den här rollen kan eventuellt skapa eller uppdatera användare eller andra objekt när de personifierar programmet, beroende på programmets behörigheter.
> Ingen av rollerna ger möjlighet att hantera inställningar för villkorlig åtkomst.

## <a name="create-and-assign-a-custom-role-preview"></a>Skapa och tilldela en anpassad roll (förhandsgranskning)

Att skapa anpassade roller och tilldela anpassade roller är separata steg:

- [Skapa en anpassad *rolldefinition* ](roles-create-custom.md) och [lägg till behörigheter i den från en förinställd lista](roles-custom-available-permissions.md). Det här är samma behörigheter som används i de inbyggda rollerna.
- [Skapa en *rolltilldelning* ](roles-assign-powershell.md) för att tilldela den anpassade rollen.

Med den här separationen kan du skapa en enda rolldefinition och sedan tilldela den många gånger vid olika *scope*. En anpassad roll kan tilldelas i organisationsomfattande omfång, eller så kan den tilldelas i omfånget om ett enda Azure AD-objekt. Ett exempel på ett objektomfattning är en enda appregistrering. Med hjälp av olika scope kan samma rolldefinition tilldelas Sally över alla appregistreringar i organisationen och sedan till Naveen över endast appregistreringen Contoso Expense Reports.

Tips när du skapar och använder anpassade roller för att delegera programhantering:
- Anpassade roller ger bara åtkomst i de senaste appregistreringsbladen i Azure AD-portalen. De ger inte åtkomst i de äldre appregistreringsbladen.
- Anpassade roller ger inte åtkomst till Azure AD-portalen när användarinställningen "Begränsa åtkomst till Azure AD-administrationsportalen" är inställd på Ja.
- Appregistreringar som användaren har åtkomst till med hjälp av rolltilldelningar visas bara på fliken Alla program på sidan Appregistrering. De visas inte på fliken Ägda program.

Mer information om grunderna i anpassade roller finns i [översikten över anpassade roller](roles-custom-overview.md)samt hur du [skapar en anpassad roll](roles-create-custom.md) och hur du [tilldelar en roll](roles-assign-powershell.md).

## <a name="next-steps"></a>Nästa steg

- [Undertyper och behörigheter för programregistrering](roles-custom-available-permissions.md)
- [Referens för Azure AD-administratörsroll](directory-assign-admin-roles.md)
