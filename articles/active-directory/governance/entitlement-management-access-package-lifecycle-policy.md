---
title: Ändra livscykelinställningar för ett åtkomstpaket i Azure AD-berättigandehantering - Azure Active Directory
description: Lär dig hur du ändrar livscykelinställningar för ett åtkomstpaket i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261988"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Ändra livscykelinställningar för ett åtkomstpaket i Azure AD-berättigandehantering

Som åtkomstpakethanterare kan du ändra livscykelinställningarna för ett åtkomstpaket när som helst genom att redigera en befintlig princip. Om du ändrar utgångsdatumet för en princip ändras inte utgångsdatumet för begäranden som redan är i ett väntande godkännande eller godkänt tillstånd.

I den här artikeln beskrivs hur du ändrar livscykelinställningarna för ett befintligt åtkomstpaket.

## <a name="open-lifecycle-settings"></a>Öppna livscykelinställningar

Om du vill ändra livscykelinställningarna för ett åtkomstpaket måste du öppna motsvarande princip. Följ dessa steg för att öppna livscykelinställningarna för ett åtkomstpaket.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Principer** och sedan på den princip som har de livscykelinställningar som du vill redigera.

    Fönstret Policyinformation öppnas längst ned på sidan.

    ![Fönstret Åtkomstpaket - informationsfönstret Principinformation](./media/entitlement-management-shared/policy-details.png)

1. Klicka på **Redigera** om du vill redigera principen.

    ![Åtkomstpaket - Redigera princip](./media/entitlement-management-shared/policy-edit.png)

1. Klicka på fliken **Livscykel** för att öppna livscykelinställningarna.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Nästa steg

- [Ändra inställningar för begäran och godkännande för ett åtkomstpaket](entitlement-management-access-package-request-policy.md)