---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126731"
---
## <a name="disable-email-verification"></a>Inaktivera e-postverifiering

Som standard verifierar Azure Active Directory B2C (Azure AD B2C) kundens e-postadress för lokala konton (konton för användare som registrerar sig med e-postadress eller användarnamn). Azure AD B2C säkerställer giltiga e-postadresser genom att kräva att kunderna verifierar dem under registreringsprocessen. Det förhindrar också skadliga aktörer från att använda automatiserade processer för att generera bedrägliga konton i dina program.

Vissa programutvecklare föredrar att hoppa över e-postverifiering under registreringsprocessen och i stället låta kunder verifiera sin e-postadress senare. För att stödja detta kan Azure AD B2C konfigureras för att inaktivera e-postverifiering. Detta skapar en smidigare registreringsprocess och ger utvecklare flexibiliteten att skilja kunder som har verifierat sin e-postadress från kunder som inte har det.

> [!WARNING]
> Om du inaktiverar e-postverifiering i registreringsprocessen kan det leda till skräppost. Om du inaktiverar standardverifieringen av e-postbehörande azure AD B2C rekommenderar vi att du implementerar ett ersättningsverifieringssystem.
