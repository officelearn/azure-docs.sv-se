---
title: Auktorisera dataåtgärder
titleSuffix: Azure Storage
description: Lär dig mer om olika sätt att auktorisera åtkomst till Azure Storage, inklusive Azure Active Directory, Shared Key auktorisering eller signaturer för delad åtkomst (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 53eca8a0b9e7cc9abb8f89cd56fca5df28f2de0f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521935"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Auktorisera åtkomst till data i Azure Storage

Varje gång du öppnar data i ditt lagringskonto gör klienten en begäran via HTTP/HTTPS till Azure Storage. Varje begäran till en säker resurs måste auktoriseras, så att tjänsten säkerställer att klienten har den behörighet som krävs för åtkomst till data.

I följande tabell beskrivs de alternativ som Azure Storage erbjuder för att godkänna åtkomst till resurser:

|  |Delad nyckel (lagringskontonyckel)  |Signatur för delad åtkomst (SAS)  |Azure Active Directory (Azure AD)  |Active Directory (förhandsgranskning) |Anonym läsåtkomst för allmänheten  |
|---------|---------|---------|---------|---------|---------|
|Azure-blobar     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |[Stöds](storage-auth-aad.md)         |Stöds inte|[Stöds](../blobs/storage-manage-access-to-resources.md)         |
|Azure-filer (SMB)     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |Stöds inte         |[Stöds, endast med AAD Domain Services](../files/storage-files-active-directory-overview.md)         |[Autentiseringsuppgifter som stöds måste synkroniseras till Azure AD](../files/storage-files-active-directory-overview.md)|Stöds inte         |
|Azure-filer (REST)     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |Stöds inte         |Stöds inte |Stöds inte         |
|Azure Queues     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |[Stöds](storage-auth-aad.md)         |Stöds inte | Stöds inte         |
|Azure-tabeller     |[Stöds](/rest/api/storageservices/authorize-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |Stöds inte         |Stöds inte| Stöds inte         |

Varje auktoriseringsalternativ beskrivs kortfattat nedan:

- **Azure Active Directory (Azure AD) integration** för blobbar och köer. Azure AD tillhandahåller rollbaserad åtkomstkontroll (RBAC) för kontroll över en klients åtkomst till resurser i ett lagringskonto. Mer information om Azure AD-integrering för blobbar och köer finns i [Auktorisera åtkomst till Azure-blobbar och köer med Azure Active Directory](storage-auth-aad.md).

- **Azure Active Directory Domain Services (Azure AD DS) autentisering** för Azure-filer. Azure Files stöder identitetsbaserad auktorisering via Server Message Block (SMB) via Azure AD DS. Du kan använda RBAC för finkornig kontroll över en klients åtkomst till Azure Files-resurser i ett lagringskonto. Mer information om Azure Files-autentisering med domäntjänster finns i vår [översikt](../files/storage-files-active-directory-overview.md).

- **Active Directory (AD) autentisering (förhandsversion)** för Azure-filer. Azure Files stöder identitetsbaserad auktorisering via SMB via AD. Din AD-domäntjänst kan finnas på lokala datorer eller i virtuella Azure-datorer. SMB-åtkomst till filer stöds med AD-autentiseringsuppgifter från domänanslutna datorer, antingen lokalt eller i Azure. Du kan använda RBAC för åtkomstkontroll på resursnivå och NTFS-DACLs för tvingande behörighet på katalog/filnivå. Mer information om Azure Files-autentisering med domäntjänster finns i vår [översikt](../files/storage-files-active-directory-overview.md).

- **Auktorisering** för delad nyckel för blobbar, filer, köer och tabeller. En klient som använder delad nyckel skickar ett huvud med varje begäran som signeras med åtkomstnyckeln för lagringskontot. Mer information finns i [Auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key/).
- **Signaturer** för delad åtkomst för blobbar, filer, köer och tabeller. Signaturer för delad åtkomst (SAS) ger begränsad delegerad åtkomst till resurser i ett lagringskonto. Om du lägger till begränsningar för det tidsintervall som signaturen är giltig för eller om behörigheter som den ger ger flexibilitet i hanteringen av åtkomst. Mer information finns i [Använda SIGNATURER för delad åtkomst (SAS)](storage-sas-overview.md).
- **Anonym offentlig läsåtkomst** för behållare och blobbar. Auktorisering krävs inte. Mer information finns i [Hantera anonym läsåtkomst till behållare och blobbar](../blobs/storage-manage-access-to-resources.md).  

Som standard är alla resurser i Azure Storage skyddade och är endast tillgängliga för kontoägaren. Även om du kan använda någon av de auktoriseringsstrategier som beskrivs ovan för att ge klienter åtkomst till resurser i ditt lagringskonto, rekommenderar Microsoft att du använder Azure AD när det är möjligt för maximal säkerhet och användarvänlighet.

## <a name="next-steps"></a>Nästa steg

- [Auktorisera åtkomst till Azure-blobbar och köer med Azure Active Directory](storage-auth-aad.md)
- [Auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key/)
- [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SIGNATURER för delad åtkomst (SAS)](storage-sas-overview.md)
