---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "79126731"
---
## <a name="disable-email-verification"></a>Inaktivera e-postverifiering

Som standard verifierar Azure Active Directory B2C (Azure AD B2C) din kunds e-postadress för lokala konton (konton för användare som registrerar sig för e-postadress eller användar namn). Azure AD B2C garanterar giltiga e-postadresser genom att kräva att kunderna verifierar dem under registrerings processen. Det förhindrar också skadliga aktörer från att använda automatiserade processer för att generera bedrägliga konton i dina program.

Vissa programutvecklare föredrar att hoppa över e-postverifiering under registrerings processen och får i stället kunder att verifiera sin e-postadress senare. Azure AD B2C kan konfigureras för att inaktivera e-postverifiering för att stödja detta. Detta skapar en smidigare registrerings process och ger utvecklare flexibiliteten att särskilja kunder som har verifierat sin e-postadress från kunder som inte har det.

> [!WARNING]
> Att inaktivera e-postverifiering i registrerings processen kan leda till skräp post. Om du inaktiverar standard Azure AD B2C e-postverifieringen från e-post, rekommenderar vi att du implementerar ett system för ersättnings verifiering.
