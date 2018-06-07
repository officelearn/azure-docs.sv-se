---
title: Auktorisera åtkomst till Azure Storage | Microsoft Docs
description: Lär dig mer om olika sätt att auktorisera åtkomst till Azure Storage, inklusive Azure Active Directory, delad nyckel autentisering eller signaturer för delad åtkomst.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 46bb332e28d7503e543ca3c99fa1099ef17f34c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660995"
---
# <a name="authorizing-access-to-azure-storage"></a>Auktorisera åtkomst till Azure Storage

Varje gång som du kommer åt data i ditt lagringskonto skickar klienten en begäran via HTTP/HTTPS till Azure Storage. Varje begäran till en säker resurs måste auktoriseras så att tjänsten garanterar att klienten har behörighet att komma åt data. Azure Storage erbjuder dessa alternativ för att auktorisera åtkomst för att skydda resurser:

- **Azure Active Directory (Azure AD)-integration (förhandsgranskning)** för blobbar och köer. Azure AD innehåller rollbaserad åtkomstkontroll (RBAC) för detaljerad kontroll över en klients åtkomst till resurser i ett lagringskonto. Mer information finns i [autentisera förfrågningar till Azure Storage med hjälp av Azure Active Directory (förhandsgranskning)](storage-auth-aad.md).
- **Delad nyckel auktorisering** för blobbar, filer, köer och tabeller. En klient som använder delad nyckel skickar ett huvud med varje begäran som har signerats med åtkomstnyckeln för lagringskontot. Mer information finns i [auktorisera med delad nyckel](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/).
- **Signaturer för delad åtkomst** för blobbar, filer, köer och tabeller. Signaturer för delad åtkomst (SAS) ger begränsad delegerad åtkomst till resurser i ett lagringskonto. Lägga till begränsningar på tidsintervall för vilket signaturen är giltig eller behörigheter beviljas ger flexibilitet vid hantering av åtkomst. Mer information finns i [använder signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Anonym offentlig läsbehörighet** för behållare och blobbar. Auktorisering krävs inte. Mer information finns i [Hantera anonym läsbehörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md).  

Som standard alla resurser i Azure Storage är skyddade och är bara tillgängliga för ägare. Men du kan använda någon av de auktorisering strategier som beskrivs ovan som ger klienterna tillgång till resurser i ditt lagringskonto, Microsoft rekommenderar att du använder Azure AD när det är möjligt för maximal säkerhet och enkel användning. 



