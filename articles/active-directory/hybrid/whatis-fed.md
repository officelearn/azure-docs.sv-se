---
title: Vad är federation med Azure AD? | Microsoft Docs
description: Beskriver federation med Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 0556a01490786ec4101176bc0437661723ce9b4f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110467"
---
# <a name="what-is-federation-with-azure-ad"></a>Vad är federation med Azure AD?

Federation är en samling av domäner som har upprättat förtroende. Förtroendenivån kan variera, men omfattar vanligtvis autentisering och nästan alltid auktorisering. En typisk federation kan innehålla ett antal organisationer som har upprättat förtroende för delad åtkomst till en uppsättning resurser.

Du kan federera din lokala miljö med Azure AD och använda den här federationstjänsten för autentisering och auktorisering.  Med en här inloggningsmetoden sker all användarautentisering lokalt.  Med den här metoden kan administratörer implementera mer rigorösa nivåer av åtkomstkontroll. Federation med AD FS och PingFederate är tillgänglig.

![Federerad identitet](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Om du bestämmer dig för att använda federation med Active Directory Federation Services (AD FS) kan du även konfigurera synkronisering av hashlösenord som en reservåtgärd för de fall då det inte går att nå AD FS-infrastrukturen.


## <a name="next-steps"></a>Nästa steg

- [Vad är hybrididentitet?](whatis-phs.md)
- [Vad är Azure AD Connect och Connect Health?](whatis-azure-ad-connect.md)
- [Vad är synkronisering av lösenordshash?](whatis-phs.md)
- [Vad är federation?](whatis-fed.md)
- [Vad är enkel inloggning?](how-to-connect-sso.md)
- [Så här fungerar federation](how-to-connect-fed-whatis.md)
- [Federation med PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)