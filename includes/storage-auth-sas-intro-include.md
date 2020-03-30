---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75371792"
---
Med en SAS -signatur (Shared Access Signature) kan du bevilja begränsad åtkomst till behållare och blobbar i ditt lagringskonto. När du skapar en SAS anger du dess begränsningar, inklusive vilka Azure Storage-resurser en klient får åtkomst till, vilka behörigheter de har för dessa resurser och hur länge SAS är giltigt.

Varje SAS är signerad med en nyckel. Du kan signera en SAS på två sätt:

- Med en nyckel som skapats med Azure Active Directory (Azure AD) autentiseringsuppgifter. En SAS som är signerad med Azure AD-autentiseringsuppgifter är en *SAS-användardelegation.*
- Med lagringskontonyckeln. Både en *service-SAS* och ett *konto SAS* är signerade med lagringskontonyckeln.

En SAS för användardelegation erbjuder överlägsen säkerhet till en SAS som är signerad med lagringskontonyckeln. Microsoft rekommenderar att du använder en SAS-användardelegation när det är möjligt. Mer information finns i [Bevilja begränsad åtkomst till data med SIGNATURER för delad åtkomst (SAS)](../articles/storage/common/storage-sas-overview.md).
