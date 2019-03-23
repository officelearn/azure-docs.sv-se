---
title: Auktorisera åtkomst till Azure Storage | Microsoft Docs
description: Läs mer om olika sätt att auktorisera åtkomst till Azure Storage, inklusive Azure Active Directory, autentisering med delad nyckel eller signaturer för delad åtkomst.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ba4d83d620a597c9a59f1a3c7f2f9d6722ba42d8
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368372"
---
# <a name="authorizing-access-to-azure-storage"></a>Auktorisera åtkomst till Azure Storage

Varje gång som du kommer åt data i ditt lagringskonto, din klient gör en begäran via HTTP/HTTPS till Azure Storage. Varje begäran till en säker resurs måste auktoriseras så att tjänsten säkerställer att klienten har behörighet att komma åt data. Azure Storage erbjuder dessa alternativ för att auktorisera åtkomst för att skydda resurser:

- **Integrering med Azure Active Directory (Azure AD)** för blobbar och köer. Azure AD tillhandahåller rollbaserad åtkomstkontroll (RBAC) för detaljerad kontroll över en klients åtkomst till resurser i ett lagringskonto. Mer information finns i [autentisering av förfrågningar till Azure Storage med Azure Active Directory](storage-auth-aad.md).
- **Delad nyckel auktorisering** för blobar, filer, köer och tabeller. En klient som använder delad nyckel skickar ett huvud med varje begäran som har signerats med åtkomstnyckeln för lagringskontot. Mer information finns i [auktorisera med delad nyckel](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Signaturer för delad åtkomst** för blobar, filer, köer och tabeller. Signaturer för delad åtkomst (SAS) ger begränsad delegerad åtkomst till resurser i ett lagringskonto. Att lägga till begränsningar på tidsintervall där signaturen är giltig eller behörigheter som den ger ger flexibilitet vid hantering av åtkomst. Mer information finns i [använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Anonym offentlig läsbehörighet** för behållare och blobbar. Auktorisering krävs inte. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../blobs/storage-manage-access-to-resources.md).  

Som standard alla resurser i Azure Storage är skyddade och är endast tillgängliga för kontoinnehavaren. Men du kan använda någon av de auktorisering strategier som beskrivs ovan och ge klienterna åtkomst till resurser i ditt storage-konto, Microsoft rekommenderar att du använder Azure AD när det är möjligt för maximal säkerhet och användarvänlighet. 



