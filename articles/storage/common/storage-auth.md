---
title: Auktorisera dataåtgärder
titleSuffix: Azure Storage
description: Lär dig mer om de olika sätten att bevilja åtkomst till Azure Storage, inklusive Azure Active Directory, auktorisering av delad nyckel eller signaturer för delad åtkomst (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7deb773fae6ba56b6f601983ffd2b07d887e1480
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565768"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Auktorisera åtkomst till data i Azure Storage

Varje gång du kommer åt data i ditt lagrings konto skickar klienten en begäran över HTTP/HTTPS till Azure Storage. Varje begäran till en säker resurs måste vara auktoriserad, så att tjänsten säkerställer att klienten har de behörigheter som krävs för att komma åt data.

I följande tabell beskrivs de alternativ som Azure Storage erbjudanden för att auktorisera åtkomst till resurser:

|  |Delad nyckel (lagrings konto nyckel)  |Signatur för delad åtkomst (SAS)  |Azure Active Directory (Azure AD)  |Active Directory (för hands version)|Anonym offentlig Läs behörighet  |
|---------|---------|---------|---------|---------|---------|
|Azure-blobar     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |[Stöds](storage-auth-aad.md)         |Stöds inte|[Stöds](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |Stöds inte         |[Stöds endast med AAD Domain Services](../files/storage-files-active-directory-overview.md)         |[Stöds, autentiseringsuppgifterna måste synkroniseras med Azure AD](../files/storage-files-active-directory-overview.md)|Stöds inte         |
|Azure Files (REST)     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |Stöds inte         |Stöds inte |Stöds inte         |
|Azure Queues     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |[Stöds](storage-auth-aad.md)         |Stöds inte | Stöds inte         |
|Azure-tabeller     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |Stöds inte         |Stöds inte| Stöds inte         |

Varje Authorization-alternativ beskrivs kortfattat nedan:

- **Azure Active Directory (Azure AD)-integration** för blobbar och köer. Azure AD tillhandahåller rollbaserad åtkomst kontroll (RBAC) för detaljerad kontroll över en klients åtkomst till resurser i ett lagrings konto. Mer information om Azure AD-integrering för blobbar och köer finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

- **Azure Active Directory Domain Services-autentisering (Azure AD DS)** för filer. Azure Files stöder Identity-baserad auktorisering över Server Message Block (SMB) via Azure AD DS. Du kan använda RBAC för detaljerad kontroll över en klients åtkomst till Azure Files resurser i ett lagrings konto. Mer information om Azure Files autentisering med hjälp av domän tjänster finns i vår [Översikt](../files/storage-files-active-directory-overview.md).

- **Active Directory (AD) autentisering (för hands version)** för filer. Azure Files stöder Identity-baserad auktorisering via SMB via AD. AD-domänen kan finnas på lokala datorer eller i virtuella Azure-datorer. SMB-åtkomst till filer stöds med hjälp av AD-autentiseringsuppgifter från domänanslutna datorer, antingen lokalt eller i Azure. Du kan använda RBAC för åtkomst kontroll på resurs nivå och NTFS-DACL för katalog-/filnivå-behörighet. Mer information om Azure Files autentisering med hjälp av domän tjänster finns i vår [Översikt](../files/storage-files-active-directory-overview.md).

- **Auktorisering av delad nyckel** för blobbar, filer, köer och tabeller. En klient som använder delad nyckel skickar ett huvud till varje begäran som är signerad med lagrings kontots åtkomst nyckel. Mer information finns i [auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key/).
- **Signaturer för delad åtkomst** för blobbar, filer, köer och tabeller. Signaturer för delad åtkomst (SAS) ger begränsad delegerad åtkomst till resurser i ett lagrings konto. Att lägga till begränsningar i tidsintervallet för vilka signaturen är giltig eller behörigheter som den ger ger flexibilitet vid hantering av åtkomst. Mer information finns i [använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md).
- **Anonym offentlig Läs behörighet** för behållare och blobbar. Auktorisering krävs inte. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../blobs/storage-manage-access-to-resources.md).  

Som standard är alla resurser i Azure Storage skyddade, och de är bara tillgängliga för kontots ägare. Även om du kan använda någon av de behörighets strategier som beskrivs ovan för att ge klienterna åtkomst till resurser i ditt lagrings konto, rekommenderar Microsoft att använda Azure AD när det är möjligt för maximal säkerhet och enkel användning.

## <a name="next-steps"></a>Nästa steg

- [Ge åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md)
- [Auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key/)
- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](storage-sas-overview.md)
