---
title: Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory (förhandsversion) | Microsoft Docs
description: Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory (förhandsversion).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: a918a44042df13c8b799d823656c4cd878f8f618
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426659"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Autentisera åtkomsten till Azure BLOB-objekt och köer med hjälp av Azure Active Directory (förhandsversion)

Azure Storage stöder autentisering och auktorisering med Azure Active Directory (AD) för tjänsterna Blob och kö. Med Azure AD, kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja åtkomst till användare, grupper eller program tjänstens huvudnamn. 

Autentisera användare eller program med hjälp av autentiseringsuppgifter för Azure AD tillhandahåller överlägsen säkerhet och användarvänlighet över annan typ av auktorisering. Du kan fortsätta använda delad nyckel auktorisering med program, kringgår med hjälp av Azure AD behovet av att spara din åtkomstnyckel med din kod. Du kan också fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerad åtkomst till resurser i ditt storage-konto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller oroa dig om du återkallar en komprometterad SAS. Microsoft rekommenderar att du använder Azure AD-autentisering för dina Azure Storage-program när det är möjligt.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>Om förhandsversionen av

Tänk på följande saker övervägas om förhandsversionen av:

- Azure AD-integrering är tillgängligt för tjänsterna Blob och kö endast i förhandsversionen.
- Azure AD-integrering är tillgängligt för GPv1, GPv2 och Blob storage-konton i alla offentliga regioner. 
- Endast lagringskonton som skapats med distributionsmodellen i Resource Manager stöds. 
- Stöd för anroparen identitetsinformation i Azure Storage Analytics loggning kommer snart.
- Azure AD-auktorisering av åtkomst till resurser i standardlagringskonton stöds för närvarande. Auktorisering av åtkomst till sidblobar i premium storage-konton kommer snart att stödjas.
- Azure Storage stöder både inbyggda och anpassade RBAC-roller. Du kan tilldela roller begränsade till prenumerationen, resursgruppen, storage-konto eller en enskild behållare eller en kö.
- Azure Storage-klientbibliotek som för närvarande stöd för Azure AD-integrering är:
    - [NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [BLOB, Queue och filer](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="get-started-with-azure-ad-for-storage"></a>Kom igång med Azure AD för lagring

Det första steget i att använda Azure AD-integrering med Azure Storage är att tilldela RBAC-roller för storage-data till tjänstens huvudnamn (en användare, grupp eller program tjänstens huvudnamn) eller hanterade identiteter för Azure-resurser. RBAC-roller omfattar vanliga uppsättningar av behörigheter för behållare och köer. Läs mer om RBAC-roller för Azure Storage i [hantera åtkomsträttigheter till storage-data med RBAC (förhandsversion)](storage-auth-aad-rbac.md).

Om du vill använda Azure AD för att auktorisera åtkomst till lagringsresurser i dina program, måste du begära en OAuth 2.0-åtkomsttoken från din kod. Läs hur du begär en åtkomsttoken och använda den för att godkänna begäranden till Azure Storage i [autentisera med Azure AD från ett Azure Storage-program (förhandsversion)](storage-auth-aad-app.md). Om du använder en hanterad identitet, se [autentisera åtkomst till blobbar och köer med Azure hanterade identiteter för Azure-resurser (förhandsgranskning)](storage-auth-aad-msi.md).

Azure CLI och PowerShell stöder nu loggat in med en Azure AD-identitet. När du har loggat in med en Azure AD-identitet körs sessionen under den identiteten. Mer information finns i [använda en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell (förhandsversion)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Nästa steg

Ytterligare information om Azure AD-integrering för Azure-Blobbar och köer finns i Azure Storage-teamets blogg publicerar, [meddelande om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
