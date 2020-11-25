---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028552"
---
Key Vault har stöd för upp till 1024 åtkomstprincipposter där varje post kan ha en specifik uppsättning behörigheter för ett visst säkerhetsobjekt. På grund av den här begränsningen rekommenderar vi att du tilldelar åtkomst principer till grupper av användare, där det är möjligt, i stället för enskilda användare. Med hjälp av grupper blir det mycket enklare att hantera behörigheter för flera personer i din organisation. Mer information finns i [Hantera app-och resurs åtkomst med hjälp av Azure Active Directory grupper](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Fullständig information om Key Vault åtkomst kontroll finns i [Azure Key Vault säkerhet: identitets-och åtkomst hantering](../articles/key-vault/general/overview-security.md#identity-and-access-management).