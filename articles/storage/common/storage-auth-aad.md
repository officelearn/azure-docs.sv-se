---
title: Auktorisera åtkomst till blobbar och köer med Active Directory
titleSuffix: Azure Storage
description: Auktorisera åtkomst till Azure-blobbar och köer med Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255371"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Auktorisera åtkomst till blobbar och köer med Azure Active Directory

Azure Storage stöder användning av Azure Active Directory (Azure AD) för att auktorisera begäranden till Blob- och kölagring. Med Azure AD kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja behörigheter till ett säkerhetsobjekt, som kan vara ett användar-, grupp- eller programtjänsthuvudnamn. Säkerhetsobjektet autentiseras av Azure AD för att returnera en OAuth 2.0-token. Token kan sedan användas för att auktorisera en begäran mot Blob eller Kölagring.

Att auktorisera begäranden mot Azure Storage med Azure AD ger överlägsen säkerhet och användarvänlighet över auktorisering av delad nyckel. Microsoft rekommenderar att du använder Azure AD-auktorisering med blob- och köprogram när det är möjligt för att minimera potentiella säkerhetsproblem som är inbyggda i Delad nyckel.

Auktorisering med Azure AD är tillgänglig för alla generella och Blob-lagringskonton i alla offentliga regioner och nationella moln. Endast lagringskonton som skapats med Azure Resource Manager-distributionsmodellen stöder Azure AD-auktorisering.

Blob-lagring stöder dessutom att skapa SIGNATURER för delad åtkomst (SAS) som är signerade med Azure AD-autentiseringsuppgifter. Mer information finns i [Bevilja begränsad åtkomst till data med signaturer för delad åtkomst](storage-sas-overview.md).

Azure Files stöder auktorisering med AD (förhandsversion) eller Azure AD DS (GA) endast via SMB för domänanslutna virtuella datorer. Mer information om hur du använder AD (förhandsversion) eller Azure AD DS (GA) över SMB för Azure-filer finns i [Översikt över Azure Files identitetsbaserat autentiseringsstöd för SMB-åtkomst](../files/storage-files-active-directory-overview.md).

Auktorisering med Azure AD stöds inte för Azure Table Storage. Använd Delad nyckel för att auktorisera begäranden till tabelllagring.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Översikt över Azure AD för blobbar och köer

När ett säkerhetsobjekt (en användare, grupp eller ett program) försöker komma åt en blob eller köresurs måste begäran auktoriseras, såvida det inte är en blob som är tillgänglig för anonym åtkomst. Med Azure AD är åtkomst till en resurs en tvåstegsprocess. Först autentiseras säkerhetsobjektets identitet och en OAuth 2.0-token returneras. Därefter skickas token som en del av en begäran till Blob- eller kötjänsten och används av tjänsten för att auktorisera åtkomst till den angivna resursen.

Autentiseringssteget kräver att ett program begär en OAuth 2.0-åtkomsttoken vid körning. Om ett program körs inifrån en Azure-entitet, till exempel en Azure VM, en skaluppsättning för virtuella datorer eller en Azure Functions-app, kan det använda en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för att komma åt blobbar eller köer. Mer information om hur du auktoriserar begäranden som görs av en hanterad identitet till Azure Blob eller Queue-tjänsten finns i [Auktorisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure Resources](storage-auth-aad-msi.md).

Auktoriseringssteget kräver att en eller flera RBAC-roller tilldelas säkerhetsobjektet. Azure Storage tillhandahåller RBAC-roller som omfattar vanliga behörighetsuppsättningar för blob- och ködata. De roller som tilldelas ett säkerhetsobjekt avgör vilka behörigheter huvudmannen ska ha. Mer information om hur du tilldelar RBAC-roller för Azure Storage finns i [Hantera åtkomsträttigheter till lagringsdata med RBAC](storage-auth-aad-rbac.md).

Inbyggda program och webbprogram som gör förfrågningar till Azure Blob- eller Kötjänsten kan också auktorisera åtkomst med Azure AD. Mer information om hur du begär en åtkomsttoken och använder den för att auktorisera begäranden om blob- eller ködata finns i [Auktorisera åtkomst till Azure Storage med Azure AD från ett Azure Storage-program](storage-auth-aad-app.md).

## <a name="assign-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomsträttigheter

Azure Active Directory (Azure AD) godkänner åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga behörighetsuppsättningar som används för att komma åt blob- och ködata. Du kan också definiera anpassade roller för åtkomst till blob- och ködata.

När en RBAC-roll tilldelas ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till prenumerationsnivån, resursgruppen, lagringskontot eller en enskild behållare eller kö. Ett Azure AD-säkerhetsobjekt kan vara en användare, en grupp, ett programtjänsthuvudnamn eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Inbyggda RBAC-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Mer information om hur du tilldelar en inbyggd RBAC-roll till ett säkerhetsobjekt finns i någon av följande artiklar:

- [Bevilja åtkomst till blob- och ködata i Azure med RBAC på Azure-portalen](storage-auth-aad-rbac-portal.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC med hjälp av Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC med hjälp av PowerShell](storage-auth-aad-rbac-powershell.md)

Mer information om hur inbyggda roller definieras för Azure Storage finns i [Förstå rolldefinitioner](../../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade RBAC-roller finns i [Skapa anpassade roller för Azure Role-Based Access Control](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Komma åt behörigheter för dataåtgärder

Information om de behörigheter som krävs för att anropa specifika Blob- eller Kötjänståtgärder finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Resursomfattning

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Komma åt data med ett Azure AD-konto

Åtkomst till blob- eller ködata via Azure-portalen, PowerShell eller Azure CLI kan auktoriseras antingen med hjälp av användarens Azure AD-konto eller med hjälp av kontoåtkomstnycklarna (auktorisering av delad nyckel).

### <a name="data-access-from-the-azure-portal"></a>Dataåtkomst från Azure-portalen

Azure-portalen kan använda antingen ditt Azure AD-konto eller kontoåtkomstnycklarna för att komma åt blob- och ködata i ett Azure-lagringskonto. Vilket auktoriseringsschema som Azure-portalen använder beror på vilka RBAC-roller som har tilldelats dig.

När du försöker komma åt blob- eller ködata kontrollerar Azure-portalen först om du har tilldelats en RBAC-roll med **Microsoft.Storage/storageAccounts/listkeys/action**. Om du har tilldelats en roll med den här åtgärden använder Azure-portalen kontonyckeln för åtkomst till blob- och ködata via auktorisering av delad nyckel. Om du inte har tilldelats en roll med den här åtgärden försöker Azure-portalen komma åt data med ditt Azure AD-konto.

Om du vill komma åt blob- eller ködata från Azure-portalen med ditt Azure AD-konto behöver du behörigheter för att komma åt blob- och ködata, och du behöver också behörigheter för att navigera genom lagringskontoresurserna i Azure-portalen. De inbyggda rollerna som tillhandahålls av Azure Storage ger åtkomst till blob- och köresurser, men de ger inte behörigheter till lagringskontoresurser. Därför kräver åtkomst till portalen också tilldelning av en Azure Resource Manager-roll, till exempel [reader-rollen,](../../role-based-access-control/built-in-roles.md#reader) begränsad till lagringskontots nivå eller högre. **Rollen Läsare** ger de mest begränsade behörigheterna, men en annan Azure Resource Manager-roll som ger åtkomst till lagringskontohanteringsresurser är också godtagbar. Mer information om hur du tilldelar behörigheter till användare för dataåtkomst i Azure-portalen med ett Azure AD-konto finns i [Bevilja åtkomst till Azure-blob och ködata med RBAC i Azure-portalen](storage-auth-aad-rbac-portal.md).

Azure-portalen anger vilket auktoriseringsschema som används när du navigerar till en behållare eller kö. Mer information om dataåtkomst i portalen finns i [Använda Azure-portalen för att komma åt blob- eller ködata](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Dataåtkomst från PowerShell eller Azure CLI

Azure CLI och PowerShell stöder inloggning med Azure AD-autentiseringsuppgifter. När du har loggat in körs sessionen under dessa autentiseringsuppgifter. Mer information finns i [Kör Azure CLI- eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter för åtkomst till blob- eller ködata](authorize-active-directory-powershell.md).

## <a name="next-steps"></a>Nästa steg

- [Auktorisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md)
- [Auktorisera med Azure Active Directory från ett program för åtkomst till blobbar och köer](storage-auth-aad-app.md)
- [Azure Storage-stöd för Azure Active Directory-baserad åtkomstkontroll är i allmänhet tillgängligt](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
