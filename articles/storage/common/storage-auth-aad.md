---
title: Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory | Microsoft Docs
description: Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ff543b7275ab05a83b1be1d156cbc6059a3b5430
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369905"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory

Azure Storage stöder autentisering och auktorisering med Azure Active Directory (AD) för tjänsterna Blob och kö. Med Azure AD, kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja åtkomst till användare, grupper eller program tjänstens huvudnamn. 

Autentisera användare eller program med hjälp av autentiseringsuppgifter för Azure AD tillhandahåller överlägsen säkerhet och användarvänlighet över annan typ av auktorisering. Du kan fortsätta använda delad nyckel auktorisering med program, kringgår med hjälp av Azure AD behovet av att spara din åtkomstnyckel med din kod. Du kan också fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerad åtkomst till resurser i ditt storage-konto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller oroa dig om du återkallar en komprometterad SAS. Microsoft rekommenderar att du använder Azure AD-autentisering för dina Azure Storage-program när det är möjligt.

Autentisering och auktorisering med Azure AD-autentiseringsuppgifter är tillgängliga för alla generell användning v2, general-purpose v1 och Blob storage-konton i alla offentliga regioner. Storage-konton som skapats med stöd för Azure Resource Manager deployment model Azure AD-auktorisering.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Översikt över Azure AD för blobbar och köer

Det första steget i att använda Azure AD-integrering med Azure Storage är att tilldela RBAC-roller för storage-data till tjänstens huvudnamn (en användare, grupp eller program tjänstens huvudnamn) eller hanterade identiteter för Azure-resurser. RBAC-roller omfattar vanliga uppsättningar av behörigheter för behållare och köer. Läs mer om att tilldela RBAC-roller för Azure Storage i [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).

Om du vill använda Azure AD för att auktorisera åtkomst till lagringsresurser i dina program, måste du begära en OAuth 2.0-åtkomsttoken från din kod. Läs hur du begär en åtkomsttoken och använda den för att godkänna begäranden till Azure Storage i [autentisera med Azure AD från ett Azure Storage-program](storage-auth-aad-app.md). Om du använder en hanterad identitet, se [autentisera åtkomst till blobbar och köer med Azure hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md).

Azure CLI och PowerShell stöder nu loggat in med en Azure AD-identitet. När du har loggat in med en Azure AD-identitet körs sessionen under den identiteten. Mer information finns i [använda en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-roller för blobbar och köer

Azure Active Directory (Azure AD) auktoriserar åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för åtkomst till behållare eller köer. 

När en RBAC-roll tilldelas till en Azure AD-säkerhetsobjekt, Azure beviljar åtkomst till att dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till nivån för prenumerationen, resursgruppen, storage-konto eller en enskild behållare eller en kö. En Azure AD-säkerhetsobjekt kan vara en användare, en grupp, tjänstens huvudnamn för ett program eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Läs hur du tilldelar en inbyggd roll i Azure-portalen i [bevilja åtkomst till Azure-behållare och köer med RBAC i Azure-portalen](storage-auth-aad-rbac.md).

### <a name="access-permissions-granted-by-rbac-roles"></a>Åtkomstbehörigheter som ges av RBAC-roller 

Mer information om de behörigheter som krävs för att anropa åtgärder för Azure Storage finns [behörigheter för att anropa REST-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Nästa steg

- [Bevilja åtkomst till Azure-behållare och köer med RBAC i Azure portal](storage-auth-aad-rbac.md)
- [Autentisera med Azure Active Directory från ett program för åtkomst till blobar och köer](storage-auth-aad-app.md)
- [Autentisera åtkomsten till blobbar och köer med hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md)
- [Använda en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell](storage-auth-aad-script.md)