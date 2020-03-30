---
title: Användaretablering till Azure AD Gallery-appen tar timmar eller mer
description: Så här tar du reda på varför etableringen till din ansökan kan ta längre tid än förväntat
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522653"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Användaretablering till ett Azure AD Gallery-program tar timmar eller mer

När du först aktiverar automatisk etablering för ett program kan den inledande cykeln ta allt från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. 

Efterföljande synkroniseringar efter den inledande cykeln går snabbare, eftersom etableringstjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande cykeln, vilket förbättrar prestanda för efterföljande synkroniseringar.

## <a name="how-to-improve-provisioning-performance"></a>Så här förbättrar du etableringsprestanda

Om den inledande cykeln tar mer än några timmar, det finns en sak du kan göra för att förbättra prestanda:

-   **Användarformningsfilter.** Med omfångsfilter kan du finjustera de data som etableringstjänsten extraherar från Azure AD genom att filtrera bort användare baserat på specifika attributvärden. Mer information om omfångsfilter finns i [Attributbaserad programetablering med omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)

