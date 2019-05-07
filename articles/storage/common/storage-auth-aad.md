---
title: Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory | Microsoft Docs
description: Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66051bd0f8be349f748c72218d538bba273be8f6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147257"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory

Azure Storage stöder autentisering och auktorisering med Azure Active Directory (AD) för tjänsterna Blob och kö. Med Azure AD, kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja åtkomst till användare, grupper eller program tjänstens huvudnamn. 

Autentisera användare eller program med hjälp av autentiseringsuppgifter för Azure AD tillhandahåller överlägsen säkerhet och användarvänlighet över annan typ av auktorisering. Du kan fortsätta använda delad nyckel auktorisering med program, kringgår med hjälp av Azure AD behovet av att spara din åtkomstnyckel med din kod. Du kan också fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerad åtkomst till resurser i ditt storage-konto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller oroa dig om du återkallar en komprometterad SAS. Microsoft rekommenderar att du använder Azure AD-autentisering för dina Azure Storage-program när det är möjligt.

Autentisering och auktorisering med Azure AD-autentiseringsuppgifter är tillgängliga för alla Allmänt och Blob storage-konton i alla offentliga regioner och nationella moln. Storage-konton som skapats med stöd för Azure Resource Manager deployment model Azure AD-auktorisering.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Översikt över Azure AD för blobbar och köer

När ett säkerhetsobjekt (en användare, grupp eller program) försöker få åtkomst till en blob eller kön resurs, måste begäran ha behörighet, om det inte en blob som är tillgängliga för anonym åtkomst. Med Azure AD är åtkomst till en resurs en tvåstegsprocess. Först autentiseras säkerhetsobjektets identitet och en OAuth 2.0-token returneras. Därefter token skickas som en del av en begäran till tjänsten Blob eller en kö och används av tjänsten för att godkänna åtkomst till den angivna resursen.

Autentisering-steget kräver att ett program begär en OAuth 2.0-åtkomsttoken vid körning. Om ett program körs från inom en Azure-enhet som en Azure-dator, en skalningsuppsättning för virtuell dator eller en Azure Functions-app, den kan använda en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) att komma åt blobar eller köer. Läs hur du godkänner begäranden som görs av en hanterad identitet till tjänsten Azure Blob eller kön i [autentisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md).

Steg för auktorisering kräver att en eller flera RBAC-roller måste tilldelas till säkerhetsobjektet. Azure Storage tillhandahåller RBAC-roller som omfattar vanliga uppsättningar av behörigheter för blob-och kö. De roller som tilldelas till säkerhetsobjekt avgör vilka behörigheter som huvudkontot har. Läs mer om att tilldela RBAC-roller för Azure Storage i [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).

Interna program och webbprogram som gör begäranden till tjänsten Azure Blob eller en kö kan också autentisera med Azure AD. Läs hur du begär en åtkomsttoken och använda den för att godkänna begäranden för blob-eller kön i [autentisera med Azure AD från ett Azure Storage-program](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomsträttigheter

Azure Active Directory (Azure AD) auktoriserar åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för åtkomst till blob-och kö. Du kan också definiera anpassade roller för åtkomst till blob-och kö.

När en RBAC-roll tilldelas till en Azure AD-säkerhetsobjekt, Azure beviljar åtkomst till att dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till nivån för prenumerationen, resursgruppen, storage-konto eller en enskild behållare eller en kö. En Azure AD-säkerhetsobjekt kan vara en användare, en grupp, tjänstens huvudnamn för ett program eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Inbyggda RBAC-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Om du vill lära dig mer om att tilldela en inbyggd RBAC-roll till säkerhetsobjekt, finns i följande artiklar:

- [Bevilja åtkomst till Azure blob och kö data med RBAC i Azure portal](storage-auth-aad-rbac-portal.md)
- [Bevilja åtkomst till Azure blob och kö data med RBAC med Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till Azure blob och kö data med RBAC med hjälp av PowerShell](storage-auth-aad-rbac-powershell.md)

Mer information om hur inbyggda roller definieras för Azure Storage, se [förstå rolldefinitioner](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview). Information om hur du skapar anpassade RBAC-roller finns i [skapa anpassade roller för rollbaserad åtkomstkontroll i](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Åtkomstbehörighet för dataåtgärder

Mer information om de behörigheter som krävs för att anropa specifika åtgärder för Blob eller Queue service finns [behörigheter för att anropa blob och kö dataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Resurs-omfång

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Åtkomst till data med en Azure AD-konto

Åtkomst till blob eller kön data via Azure portal, PowerShell eller Azure CLI kan auktoriseras med hjälp av användarens Azure AD-konto eller med hjälp av kontoåtkomstnycklar (autentisering med delad nyckel).

### <a name="data-access-from-the-azure-portal"></a>Åtkomst till data från Azure portal

Azure-portalen kan använda Azure AD-konto eller kontonycklar för åtkomst till blob och kö data i ett Azure storage-konto. Vilka Azure-portalen använder auktoriseringsschema beror på de RBAC-roller som har tilldelats dig.

När du försöker komma åt data blob eller kön, Azure-portalen kontrollerar om du har tilldelats en RBAC-roll med **Microsoft.Storage/storageAccounts/listkeys/action**. Om du har tilldelats en roll med den här åtgärden, använder Azure-portalen kontonyckeln för åtkomst till blob och kö data via delade nyckel auktorisering. Om du inte har tilldelats en roll med den här åtgärden, försöker Azure-portalen komma åt data med hjälp av Azure AD-konto.

Data från Azure portal med din Azure AD-konto du behöver behörighet att komma åt blob-och kön att få åtkomst till blob eller en kö och du behöver också behörighet att navigera genom lagringskontoresurserna i Azure-portalen. De inbyggda rollerna som tillhandahålls av Azure Storage bevilja åtkomst till blob och kö resurser, men de ge inte behörigheter till lagringskontoresurserna. Därför åtkomst till portalen kräver även tilldelningen av en Azure Resource Manager-roll som den [läsare](../../role-based-access-control/built-in-roles.md#reader) roll, begränsad till storage-konto eller högre. Den **läsare** rollen ger de mest begränsade behörigheterna, men en annan Azure Resource Manager-roll som ger åtkomst till hantering av lagringsresurser-konto är också tillåtet. Mer information om hur du tilldelar behörigheter till användare för åtkomst till data i Azure-portalen med ett Azure AD-konto finns [bevilja åtkomst till Azure blob och kö data med RBAC i Azure-portalen](storage-auth-aad-rbac-portal.md).

Azure-portalen anger vilka auktoriseringsschema används när du navigerar till en behållare eller en kö. Läs mer om dataåtkomst i portalen, [använder Azure portal för att få åtkomst till blob eller kön data](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Åtkomst till data från PowerShell eller Azure CLI

Azure CLI och PowerShell stöd för logga in med autentiseringsuppgifter för Azure AD. När du har loggat in körs din session under autentiseringsuppgifterna. Mer information finns i [kör Azure CLI eller PowerShell-kommandon med autentiseringsuppgifter för Azure AD åtkomst till blob-eller kön](storage-auth-aad-script.md).

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Azure AD-autentisering över SMB för Azure Files

Azure Files stöder autentisering med Azure AD via SMB för domänanslutna datorer endast (förhandsversion). Läs om hur du använder Azure AD via SMB för Azure Files i [översikt av Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Autentisera åtkomsten till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md)
- [Autentisera med Azure Active Directory från ett program för åtkomst till blobar och köer](storage-auth-aad-app.md)
- [Azure Storage-support för Azure Active Directory-baserad åtkomstkontroll som är allmänt tillgänglig](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
