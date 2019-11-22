---
title: Användar etablering i Azure AD Gallery-appen tar flera timmar eller mer
description: Så här tar du reda på varför etableringen av programmet kan ta längre tid än förväntat
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7425731a8d4adde11cd3f15df2cd27cd8541f615
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275702"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Användar etablering i ett Azure AD Gallery-program tar flera timmar eller mer

När du först aktiverar automatisk etablering för ett program kan den första cykeln ta var som helst från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. 

Efterföljande synkroniseringar efter den första cykeln går snabbare, eftersom etablerings tjänsten lagrar vattenstämplar som representerar båda systemens tillstånd efter den första cykeln, vilket förbättrar prestandan för efterföljande synkroniseringar.

## <a name="how-to-improve-provisioning-performance"></a>Förbättra etablerings prestanda

Om den första cykeln tar längre tid än några timmar kan du förbättra prestandan genom att göra något av följande:

-   **Användar omfångs filter.** Med omfångs filter kan du finjustera de data som etablerings tjänsten extraherar från Azure AD genom att filtrera ut användare baserat på särskilda attributvärden. Mer information om omfångs filter finns i [attribut-baserad program etablering med omfångs filter](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)

