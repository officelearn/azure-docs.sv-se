---
title: Auktorisera åtkomst till Azure Storage | Microsoft Docs
description: Lär dig mer om de olika sätten att ge åtkomst till Azure Storage, inklusive Azure Active Directory, autentisering med delad nyckel eller delade signaturer för åtkomst.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3cdbb6587871b970c0188c0b8f510cc9f1f4fe07
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985187"
---
# <a name="authorizing-access-to-azure-storage"></a>Auktorisera åtkomst till Azure Storage

Varje gång du kommer åt data i ditt lagrings konto skickar klienten en begäran över HTTP/HTTPS till Azure Storage. Varje begäran till en säker resurs måste vara auktoriserad, så att tjänsten säkerställer att klienten har de behörigheter som krävs för att komma åt data.

I följande tabell beskrivs de alternativ som Azure Storage erbjudanden för att auktorisera åtkomst till resurser:

|  |Delad nyckel (lagrings konto nyckel)  |Signatur för delad åtkomst (SAS)  |Azure Active Directory (Azure AD)  |Anonym offentlig Läs behörighet  |
|---------|---------|---------|---------|---------|
|Azure-blobbar     |[Stöds](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |[Stöds](storage-auth-aad.md)         |[Stöds](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Stöds](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Stöds inte         |[Stöds endast med AAD Domain Services](../files/storage-files-active-directory-overview.md)         |Stöds inte         |
|Azure Files (REST)     |[Stöds](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |Stöds inte         |Stöds inte         |
|Azure-köer     |[Stöds](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |[Stöds](storage-auth-aad.md)         |Stöds inte         |
|Azure-tabeller     |[Stöds](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Stöds](storage-sas-overview.md)         |Stöds inte         |Stöds inte         |

Varje Authorization-alternativ beskrivs kortfattat nedan:

- **Azure Active Directory (Azure AD)-integration** för blobbar och köer. Azure AD tillhandahåller rollbaserad åtkomst kontroll (RBAC) för detaljerad kontroll över en klients åtkomst till resurser i ett lagrings konto. Mer information om Azure AD-integrering för blobbar och köer finns i [autentisera begär anden till Azure Storage att använda Azure Active Directory](storage-auth-aad.md).

- **Azure AD Domain Services (DS)-integration (för hands version)** för filer. Azure Files stöder identitets-baserad autentisering över Server Message Block (SMB) via Azure AD DS. Detta tillhandahåller RBAC för detaljerad kontroll över en klients åtkomst till resurser i ett lagrings konto. Mer information om Azure AD-integrering för filer med domän tjänster finns i [Översikt över stöd för autentisering med Azure Files Azure Active Directory Domain Service (AAD DS) för SMB-åtkomst (för hands version)](../files/storage-files-active-directory-overview.md).

- **Auktorisering av delad nyckel** för blobbar, filer, köer och tabeller. En klient som använder delad nyckel skickar ett huvud till varje begäran som är signerad med lagrings kontots åtkomst nyckel. Mer information finns i [auktorisera med delad nyckel](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Signaturer för delad åtkomst** för blobbar, filer, köer och tabeller. Signaturer för delad åtkomst (SAS) ger begränsad delegerad åtkomst till resurser i ett lagrings konto. Att lägga till begränsningar i tidsintervallet för vilka signaturen är giltig eller behörigheter som den ger ger flexibilitet vid hantering av åtkomst. Mer information finns i [använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md).
- **Anonym offentlig Läs behörighet** för behållare och blobbar. Auktorisering krävs inte. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../blobs/storage-manage-access-to-resources.md).  

Som standard är alla resurser i Azure Storage skyddade, och de är bara tillgängliga för kontots ägare. Även om du kan använda någon av de behörighets strategier som beskrivs ovan för att ge klienterna åtkomst till resurser i ditt lagrings konto, rekommenderar Microsoft att använda Azure AD när det är möjligt för maximal säkerhet och enkel användning. 
