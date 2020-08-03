---
author: baanders
description: inkludera fil för steget åtkomst behörigheter i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 6f43a55b3a409a84e3baf99dae24af7616ea4385
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408370"
---
Azure Digitals dubbla använder [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) för rollbaserad åtkomst kontroll (RBAC). Det innebär att innan en användare kan göra data planet anrop till din Azure Digital-instansen, måste användaren tilldelas en roll med rätt behörighet för den.

För Azure Digitals dubbla är den här rollen _**Azure Digitals-ägare (för hands version)**_. Du kan läsa mer om roller och säkerhet i [*begrepp: säkerhet för Azure Digitals dubbla lösningar*](../articles/digital-twins/concepts-security.md).

I det här avsnittet visas hur du skapar en roll tilldelning för en användare i din Azure Digitals-instans med hjälp av användarens e-post i Azure AD-klienten på din Azure-prenumeration. Beroende på din roll i din organisation kan du konfigurera den här behörigheten åt dig själv eller ställa in den på uppdrag av någon annan som ska hantera Azure Digitals-instansen.

### <a name="assign-the-role"></a>Tilldela rollen

Om du vill ge en användare behörighet att hantera en Azure Digitals-instans måste du tilldela dem rollen _**Azure Digitals-ägare (förhands granskning)**_ i instansen.

> [!NOTE]
> Observera att den här rollen skiljer sig från rollen Azure AD- *ägare* , som också kan tilldelas i omfånget för Azure Digital-instansen. Detta är två distinkta hanterings roller och Azure AD- *ägaren* beviljar inte åtkomst till data Plans funktioner som beviljas med *Azure Digitals-ägare (för hands version)*.