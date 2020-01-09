---
title: Auktorisera data åtgärder
titleSuffix: Azure Storage
description: Lär dig mer om de olika sätten att bevilja åtkomst till Azure Storage, inklusive Azure Active Directory, auktorisering av delad nyckel eller signaturer för delad åtkomst (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 783e8666e2602f9251d81e976a27fbce099defa2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460531"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Auktorisera åtkomst till data i Azure Storage

Varje gång du kommer åt data i ditt lagrings konto skickar klienten en begäran över HTTP/HTTPS till Azure Storage. Varje begäran till en säker resurs måste auktoriseras, så att tjänsten säkerställer att klienten har den behörighet som krävs för åtkomst till data.

I följande tabell beskrivs de alternativ som Azure Storage erbjudanden för att auktorisera åtkomst till resurser:

|  |Delad nyckel (lagrings konto nyckel)  |Signatur för delad åtkomst (SAS)  |Azure Active Directory (Azure AD)  |Anonym offentlig Läs behörighet  |
|---------|---------|---------|---------|---------|
|Azure-blobbar     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |[Stöds](storage-auth-aad.md)         |[Stöds](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |Stöds inte         |[Stöds endast med AAD Domain Services](../files/storage-files-active-directory-overview.md)         |Stöds inte         |
|Azure Files (REST)     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |Stöds inte         |Stöds inte         |
|Azure Queues     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |[Stöds](storage-auth-aad.md)         |Stöds inte         |
|Azure-tabeller     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |Stöds inte         |Stöds inte         |

Varje Authorization-alternativ beskrivs kortfattat nedan:

- **Azure Active Directory (Azure AD)-integration** för blobbar och köer. Azure AD tillhandahåller rollbaserad åtkomst kontroll (RBAC) för detaljerad kontroll över en klients åtkomst till resurser i ett lagrings konto. Mer information om Azure AD-integrering för blobbar och köer finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

- **Azure AD Domain Services (DS)-integration (för hands version)** för filer. Azure Files stöder Identity-baserad auktorisering över Server Message Block (SMB) via Azure AD DS. Du kan använda RBAC för detaljerad kontroll över en klients åtkomst till Azure Files resurser i ett lagrings konto. Mer information om Azure AD-integrering för filer med domän tjänster finns i [Översikt över stöd för autentisering med Azure Files Azure Active Directory Domain Service (AAD DS) för SMB-åtkomst (för hands version)](../files/storage-files-active-directory-overview.md).

- **Auktorisering av delad nyckel** för blobbar, filer, köer och tabeller. En klient som använder delad nyckel skickar ett huvud till varje begäran som är signerad med lagrings kontots åtkomst nyckel. Mer information finns i [auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key/).
- **Signaturer för delad åtkomst** för blobbar, filer, köer och tabeller. Signaturer för delad åtkomst (SAS) ger begränsad delegerad åtkomst till resurser i ett lagrings konto. Att lägga till begränsningar i tidsintervallet för vilka signaturen är giltig eller behörigheter som den ger ger flexibilitet vid hantering av åtkomst. Mer information finns i [använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md).
- **Anonym offentlig Läs behörighet** för behållare och blobbar. Auktorisering krävs inte. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../blobs/storage-manage-access-to-resources.md).  

Som standard är alla resurser i Azure Storage skyddade, och de är bara tillgängliga för kontots ägare. Även om du kan använda någon av de behörighets strategier som beskrivs ovan för att ge klienterna åtkomst till resurser i ditt lagrings konto, rekommenderar Microsoft att använda Azure AD när det är möjligt för maximal säkerhet och enkel användning.

## <a name="next-steps"></a>Nästa steg

- [Ge åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md)
- [Auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key/)
- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](storage-sas-overview.md)
