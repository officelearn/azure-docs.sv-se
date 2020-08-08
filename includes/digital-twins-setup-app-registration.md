---
author: baanders
description: inkludera fil för steget åtkomst behörigheter i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 45f55e6c18d7cc551a6b96504ad2ce2ec3f84d86
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009685"
---
När du har konfigurerat en Azure Digitals-instans är det vanligt att interagera med den instansen via ett klient program. För att kunna skapa ett fungerande klient program måste du kontrol lera att klient programmet kommer att kunna autentisera mot Azures digitala dubbla. Det gör du genom att konfigurera en [Azure Active Directory (Azure AD)-](../articles/active-directory/fundamentals/active-directory-whatis.md) **app-registrering** för din klient app som ska användas.

Den här appens registrering är den plats där du konfigurerar åtkomst behörigheter till [Azure Digitals dubbla API: er](../articles/digital-twins/how-to-use-apis-sdks.md). Senare kommer klient programmet att autentiseras mot appens registrering och därför beviljas de konfigurerade åtkomst behörigheterna till API: erna.

>[!TIP]
> Som prenumerations ägare/-administratör kanske du föredrar att ställa in en ny app-registrering för varje ny Azure Digital-instansen, *eller* bara göra detta en gång, och upprätta en enda app-registrering som delas mellan alla Azure Digital-instanser i prenumerationen.

### <a name="create-the-registration"></a>Skapa registreringen