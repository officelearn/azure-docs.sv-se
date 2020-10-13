---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: c74f2543e96087378741d6388b7c27dc4d05ddc8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89381241"
---
Key Vault har stöd för upp till 1024 åtkomstprincipposter där varje post kan ha en specifik uppsättning behörigheter för ett visst säkerhetsobjekt. På grund av den här begränsningen rekommenderar vi att du tilldelar åtkomst principer till grupper av användare, där det är möjligt, i stället för enskilda användare. Med hjälp av grupper blir det mycket enklare att hantera behörigheter för flera personer i din organisation. Mer information finns i [Hantera app-och resurs åtkomst med hjälp av Azure Active Directory grupper](/azure/active-directory/fundamentals/active-directory-manage-groups)

Fullständig information om Key Vault åtkomst kontroll finns i [Azure Key Vault säkerhet: identitets-och åtkomst hantering](/azure/key-vault/general/overview-security#identity-and-access-management). 
