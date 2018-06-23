---
title: Användaretablering till ett program för Azure AD-galleriet är ta timmar eller mer | Microsoft Docs
description: Ta reda på varför etablering i tillämpningsprogrammet kan ta längre tid än väntat
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: dc582ff3dac8f128972f070309d5c8a4ce21fb70
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335391"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Användaretablering till ett program för Azure AD-galleriet är ta timmar eller mer

När du först aktiverar automatisk etablering för ett program, kan den första synkroniseringen ta allt från 20 minuter till flera timmar, beroende på storleken på Azure AD-katalog och antalet användare i omfånget för etablering. 

Efterföljande synkroniseringar efter den inledande synkroniseringen att snabbare, eftersom etablering tjänsten lagrar vattenstämplar som representerar tillståndet för båda systemen efter den första synkroniseringen, förbättra prestanda för efterföljande synkroniseringar.

## <a name="how-to-improve-provisioning-performance"></a>Hur vi kan förbättra prestanda för etablering

Om den första synkroniseringen tar flera timmar, finns det en sak som du kan göra för att förbättra prestanda:

-   **Målgrupp Användarfilter.** Målgrupp filter kan du fininställa de data som tjänsten etablering extraheras från Azure AD genom att filtrera bort användare baserat på specifika attributvärden. Mer information om Omfångsfilter finns [attributbaserad programmet etablering med Omfångsfilter](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Nästa steg
[Automatisera användaren etablering och avetablering för SaaS-program med Azure Active Directory](active-directory-saas-app-provisioning.md)

