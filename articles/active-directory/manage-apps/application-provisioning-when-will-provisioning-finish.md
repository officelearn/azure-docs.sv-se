---
title: Användaretablering för ett Azure AD-galleriprogram är att ta timmar eller mer | Microsoft Docs
description: Ta reda på varför etablering i ditt program kan ta lite längre tid än väntat
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 2b46fecc44803130e4f79667d0e083314c4419c2
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357799"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Användaretablering för ett Azure AD-galleriprogram är att ta timmar eller mer

När du först aktiverar automatisk etablering för ett program, kan den första synkroniseringen ta allt från 20 minuter till flera timmar beroende på storleken på Azure AD-katalog och antalet användare inom omfånget för etablering. 

Efterföljande synkroniseringar efter den första synkroniseringen vara snabbare, som lagrar vattenstämplar som representerar tillståndet för båda systemen efter den inledande synkroniseringen, förbättra prestanda för efterföljande synkroniseringar för etableringstjänsten.

## <a name="how-to-improve-provisioning-performance"></a>Förbättra prestandan för etablering

Om den första synkroniseringen tar mer än ett par timmar, finns det en sak som du kan göra för att förbättra prestanda:

-   **Omfångsfilter för användaren.** Omfångsfilter kan du finjustera de data som etableringstjänsten extraherar från Azure AD genom att filtrera bort användare baserat på specifika attributvärden. Läs mer på Omfångsfilter [attributbaserade programetablering med Omfångsfilter](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Nästa steg
[Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md)

