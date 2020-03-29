---
title: 'Azure AD Connect-synkronisering: Aktivera AD-papperskorgen | Microsoft-dokument'
description: I det här avsnittet rekommenderas användning av AD-papperskorgen med Azure AD Connect.
services: active-directory
keywords: AD Papperskorgen, oavsiktlig borttagning, källankare
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60382903"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect-synkronisering: Aktivera AD-papperskorgen
Vi rekommenderar att du aktiverar AD-papperskorgen för dina lokala aktiva kataloger, som är synkroniserade med Azure AD. 

Om du av misstag har tagit bort ett lokalt AD-användarobjekt och återställt det med funktionen återställer Azure AD motsvarande Azure AD-användarobjekt.  Information om ad-papperskorgen finns i artikeln [Scenarioöversikt för återställning av borttagna Active Directory-objekt](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Fördelar med att aktivera AD-papperskorgen
Den här funktionen hjälper till att återställa Azure AD-användarobjekt genom att göra följande:

* Om du av misstag har tagit bort ett lokalt AD-användarobjekt tas motsvarande Azure AD-användarobjekt bort i nästa synkroniseringscykel. Som standard håller Azure AD det borttagna Azure AD-användarobjektet i mjukt borttaget tillstånd i 30 dagar.

* Om du har aktiverat funktionen AD-papperskorgen kan du återställa det borttagna lokala AD-användarobjektet utan att ändra värdet för källankaret. När det återställda lokala AD-användarobjektet synkroniseras med Azure AD återställs motsvarande azure AD-användarobjekt med mjukt borttaget. Information om source anchor-attribut finns i artikeln [Azure AD Connect: Design concepts](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Om du inte har aktiverat funktionen AD-papperskorgen kan du behöva skapa ett AD-användarobjekt för att ersätta det borttagna objektet. Om Azure AD Connect-synkroniseringstjänsten är konfigurerad för att använda systemgenererat AD-attribut (till exempel ObjectGuid) för attributet Source Anchor, har det nyligen skapade AD-användarobjektet inte samma källankarevärde som det borttagna AD-användarobjektet. När det nyligen skapade AD-användarobjektet synkroniseras med Azure AD skapar Azure AD ett nytt Azure AD-användarobjekt i stället för att återställa det borttagna Azure AD-användarobjektet.

> [!NOTE]
> Som standard håller Azure AD borttagna Azure AD-användarobjekt i mjukt borttaget tillstånd i 30 dagar innan de tas bort permanent. Administratörer kan dock påskynda borttagningen av sådana objekt. När objekten har tagits bort permanent kan de inte längre återställas, även om den lokala AD-papperskorgen-funktionen är aktiverad.

## <a name="next-steps"></a>Nästa steg
**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
