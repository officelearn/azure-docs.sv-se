---
title: Använda Azure Storage-resursprovidern för att komma åt hanteringsresurser
description: Azure Storage-resursleverantören är en tjänst som ger åtkomst till hanteringsresurser för Azure Storage. Du kan använda Azure Storage-resursprovidern för att skapa, uppdatera, hantera och ta bort resurser som lagringskonton, privata slutpunkter och kontoåtkomstnycklar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972355"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Använda Azure Storage-resursprovidern för att komma åt hanteringsresurser

Azure Resource Manager är Azures tjänst för distribution och hantering. Azure Storage-resursleverantören är en tjänst som baseras på Azure Resource Manager och som ger åtkomst till hanteringsresurser för Azure Storage. Du kan använda Azure Storage-resursprovidern för att skapa, uppdatera, hantera och ta bort resurser som lagringskonton, privata slutpunkter och kontoåtkomstnycklar. Mer information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

Du kan använda Azure Storage-resursprovidern för att utföra åtgärder som att skapa eller ta bort ett lagringskonto eller hämta en lista över lagringskonton i en prenumeration. Om du vill auktorisera begäranden mot Azure Storage-resursprovidern använder du Azure Active Directory (Azure AD). I den här artikeln beskrivs hur du tilldelar behörigheter till hanteringsresurser och pekar på exempel som visar hur du gör förfrågningar mot Azure Storage-resursprovidern.

## <a name="management-resources-versus-data-resources"></a>Hanteringsresurser jämfört med dataresurser

Microsoft tillhandahåller två REST-API:er för att arbeta med Azure Storage-resurser. Dessa API:er utgör grunden för alla åtgärder som du kan utföra mot Azure Storage. Azure Storage REST API kan du arbeta med data i ditt lagringskonto, inklusive blob, kö, fil och tabelldata. Azure Storage-resursprovidern REST API kan du arbeta med lagringskontot och relaterade resurser.

En begäran som läser eller skriver blob-data kräver andra behörigheter än en begäran som utför en hanteringsåtgärd. RBAC ger finkornig kontroll över behörigheter till båda typerna av resurser. När du tilldelar en RBAC-roll till ett säkerhetsobjekt ska du se till att du förstår vilka behörigheter huvudmannen ska beviljas. En detaljerad referens som beskriver vilka åtgärder som är associerade med varje inbyggd RBAC-roll finns [i Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

Azure Storage stöder användning av Azure AD för att auktorisera begäranden mot Blob- och kölagring. Information om RBAC-roller för blob- och ködataåtgärder finns i [Auktorisera åtkomst till blobbar och köer med Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>Tilldela hanteringsbehörigheter med rollbaserad åtkomstkontroll (RBAC)

Varje Azure-prenumeration har en associerad Azure Active Directory som hanterar användare, grupper och program. En användare, grupp eller ett program kallas också för säkerhetsobjekt i samband med [Microsofts identitetsplattform](/azure/active-directory/develop/). Du kan bevilja åtkomst till resurser i en prenumeration till ett säkerhetsobjekt som definieras i Active Directory med hjälp av rollbaserad åtkomstkontroll (RBAC).

När du tilldelar en RBAC-roll till ett säkerhetsobjekt anger du också det scope där behörigheterna som beviljas av rollen gäller. För hanteringsåtgärder kan du tilldela en roll på prenumerationsnivå, resursgrupp eller lagringskonto. Du kan tilldela en RBAC-roll till ett säkerhetsobjekt med hjälp av [Azure-portalen,](https://portal.azure.com/) [Azure CLI-verktygen,](../../cli-install-nodejs.md) [PowerShell](/powershell/azureps-cmdlets-docs)eller [REST API för Azure Storage-resursprovidern](/rest/api/storagerp).

Mer information om RBAC finns i [Vad är RBAC (Role-based Access Control) för Azure-resurser?](../../role-based-access-control/overview.md) [Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](../../role-based-access-control/rbac-and-directory-admin-roles.md)

### <a name="built-in-roles-for-management-operations"></a>Inbyggda roller för ledningsverksamhet

Azure tillhandahåller inbyggda roller som ger behörighet till anropa hanteringsåtgärder. Azure Storage innehåller också inbyggda roller som är specifikt för användning med Azure Storage-resursleverantören.

Inbyggda roller som ger behörighet att anropa lagringshanteringsåtgärder inkluderar de roller som beskrivs i följande tabell:

|    RBAC-roll    |    Beskrivning    |    Inkluderar du åtkomst till kontonycklar?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Ägare** | Kan hantera alla lagringsresurser och åtkomst till resurser.  | Ja, ger behörighet att visa och återskapa lagringskontonycklarna. |
| **Deltagare**  | Kan hantera alla lagringsresurser, men kan inte hantera tilldelning till resurser. | Ja, ger behörighet att visa och återskapa lagringskontonycklarna. |
| **Läsare** | Kan visa information om lagringskontot, men kan inte visa kontonycklarna. | Nej. |
| **Lagringskontodeltagare** | Kan hantera lagringskontot, få information om prenumerationens resursgrupper och resurser samt skapa och hantera distributioner av prenumerationsresurser. | Ja, ger behörighet att visa och återskapa lagringskontonycklarna. |
| **Administratör för användaråtkomst** | Kan hantera åtkomst till lagringskontot.   | Ja, tillåter en säkerhetsobjektnamn att tilldela några behörigheter till sig själva och andra. |
| **Virtuell datordeltagare** | Kan hantera virtuella datorer, men inte det lagringskonto som de är anslutna till.   | Ja, ger behörighet att visa och återskapa lagringskontonycklarna. |

Den tredje kolumnen i tabellen anger om den inbyggda rollen stöder **Microsoft.Storage/storageAccounts/listkeys/action**. Den här åtgärden ger behörighet att läsa och återskapa lagringskontonycklarna. Behörigheter för åtkomst till Azure Storage-hanteringsresurser innehåller inte heller behörigheter för åtkomst till data. Men om en användare har åtkomst till kontonycklarna kan de använda kontonycklarna för att komma åt Azure Storage-data via auktorisering av delad nyckel.

### <a name="custom-roles-for-management-operations"></a>Anpassade roller för hanteringsåtgärder

Azure stöder också att definiera anpassade RBAC-roller för åtkomst till hanteringsresurser. Mer information om anpassade roller finns i [Anpassade roller för Azure-resurser](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Kodexempel

Kodexempel som visar hur du auktoriserar och anropar hanteringsåtgärder från Azure Storage-hanteringsbiblioteken finns i följande exempel:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager jämfört med klassiska distributioner

Resource Manager och de klassiska distributionsmodellerna är två olika sätt att distribuera och hantera dina Azure-lösningar. Microsoft rekommenderar att du använder distributionsmodellen för Azure Resource Manager när du skapar ett nytt lagringskonto. Om möjligt rekommenderar Microsoft också att du återskapar befintliga klassiska lagringskonton med Resource Manager-modellen. Även om du kan skapa ett lagringskonto med den klassiska distributionsmodellen är den klassiska modellen mindre flexibel och kommer så småningom att vara inaktuell.

Mer information om Azure-distributionsmodeller finns i [Resource Manager och klassisk distribution](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
- [Vad är rollbaserad åtkomstkontroll (RBAC) för Azure-resurser?](../../role-based-access-control/overview.md)
- [Skalbarhetsmål för Azure Storage-resursprovidern](scalability-targets-resource-provider.md)
