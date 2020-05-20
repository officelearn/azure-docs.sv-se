---
title: 'Azure AD Connect: funktioner i förhands granskning | Microsoft Docs'
description: I det här avsnittet beskrivs mer detaljer som är i för hands version i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396e1d9e6ad474d053ca803218d55396c073845d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680180"
---
# <a name="more-details-about-features-in-preview"></a>Mer information om funktioner i förhands granskning
I det här avsnittet beskrivs hur du använder funktioner som för hands version.

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect Sync v2 Endpoint API (offentlig för hands version) 

Vi har distribuerat en ny slut punkt (API) för Azure AD Connect som förbättrar prestandan för synkroniseringstjänsten i Azure Active Directory. Genom att använda den nya v2-slutpunkten får du märkbara prestanda vinster vid export och import till Azure AD. Den nya slut punkten stöder också synkronisering av grupper med upp till 250 000 medlemmar. Med den här slut punkten kan du också skriva tillbaka O365 Unified-grupper, utan högsta medlemskaps gräns, till din lokala Active Directory när tillbakaskrivning av grupp är aktive rad.   Mer information finns i [Azure AD Connect Sync v2 Endpoint API (offentlig för hands version)](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Tillbakaskrivning av användare
> [!IMPORTANT]
> Funktionen för förhands granskning av användare togs bort från uppdateringen augusti 2015 till Azure AD Connect. Om du har aktiverat den, bör du inaktivera den här funktionen.
>
>

## <a name="next-steps"></a>Nästa steg
Fortsätt med din [anpassade installation av Azure AD Connect](how-to-connect-install-custom.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
