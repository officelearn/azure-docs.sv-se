---
title: "Konfigurera principer för Azure Active Directory enhetsbaserad villkorlig åtkomst | Microsoft Docs"
description: "Lär dig hur du konfigurerar principer för Azure Active Directory enhetsbaserad villkorlig åtkomst."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 949cf120b9b0ae0815b492166ffc19cb0412efbd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Konfigurera principer för Azure Active Directory enhetsbaserad villkorlig åtkomst

Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan finjustera hur behöriga användare kan komma åt dina resurser. Exempelvis kan du begränsa åtkomst till vissa resurser till betrodda enheter. En princip för villkorlig åtkomst som kräver en betrodd enhet kallas även för principen för enhetsbaserad villkorlig åtkomst.

Det här avsnittet ger information om hur du konfigurerar principer för enhetsbaserad villkorlig åtkomst för Azure AD-anslutna program. 


## <a name="before-you-begin"></a>Innan du börjar

Enhetsbaserad villkorlig åtkomst ties **villkorlig åtkomst i Azure AD** och **Azure AD-enhetshantering tillsammans**. Om du inte är bekant med något av dessa områden ännu, bör du först läsa i följande avsnitt:

- **[Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md)**  -det här avsnittet ger en översikt över relaterade terminologi och villkorlig åtkomst.

- **[Introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)**  -det här avsnittet innehåller en översikt över de olika alternativ som du måste ansluta enheter med Azure AD. 


## <a name="trusted-devices"></a>Betrodda enheter

I en mobile första, molnet först värld kan Azure Active Directory enkel inloggning till enheter, appar och tjänster från var som helst. För vissa kanske resurser i din miljö och bevilja åtkomst till rätt användare inte tillräckligt bra. Förutom rätt användare kan du även kräva en betrodd enhet som används för att komma åt en resurs. I din miljö kan du definiera en betrodd enhet är baserad på följande komponenter:

- Den [enhetsplattformar](active-directory-conditional-access-azure-portal.md#device-platforms) på en enhet
- Om en enhet är kompatibel
- Om en enhet är ansluten till domänen 

Den [enhetsplattformar](active-directory-conditional-access-azure-portal.md#device-platforms) kännetecknas av operativsystemet som körs på enheten. Du kan begränsa åtkomst till vissa resurser till specifika enhetsplattformar i principen för enhetsbaserad villkorlig åtkomst.



Du kan kräva betrodda enheter har markerats som kompatibel i en princip med enhetsbaserad villkorlig åtkomst.

![Molnappar](./media/active-directory-conditional-access-policy-connected-applications/24.png)

Enheter kan markeras som kompatibla i katalogen genom att:

- Intune 
- En mobil enhet från tredje part hanteras system som hanterar Windows 10-enheter via Azure AD-integrering 
 
  

Endast enheter som är anslutna till Azure AD kan markeras som kompatibel. För att ansluta en enhet till Azure Active Directory, har du följande alternativ: 

- Azure AD som registrerade
- Azure AD ansluten
- Azure AD-hybridlösning ansluten

    ![Molnappar](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Om du har en lokal Active Directory (AD) storleken kan överväga du att enheter som inte är ansluten till Azure AD, men ansluten till din AD vara betrodd.

![Molnappar](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>Nästa steg

Innan du konfigurerar en princip för enhetsbaserad villkorlig åtkomst i din miljö bör du ta en titt på den [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md).

