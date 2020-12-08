---
title: Förstå OIDC-baserad enkel inloggning (SSO) för appar i Azure Active Directory
description: Förstå OIDC-baserad enkel inloggning (SSO) för appar i Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperfq2
ms.openlocfilehash: 487bf3c83be16ee80838b3bbe8bf8532eb6ffe12
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780067"
---
# <a name="understand-oidc-based-single-sign-on"></a>Förstå OIDC-baserad enkel inloggning
I [snabb starts serien](view-applications-portal.md) för program hantering har du lärt dig hur du använder Azure AD som identitets leverantör (IdP) för ett program. Den här artikeln går till mer information om appar som använder OpenID Connect standard för att implementera enkel inloggning. 

## <a name="before-you-begin"></a>Innan du börjar
Processen för att lägga till en app till din Azure Active Directory-klient beror på typen av enkel inloggning för programmet som implementeras. Mer information om de alternativ för enkel inloggning som är tillgängliga för appar som kan använda Azure AD för identitets hantering finns i [alternativ för enkel inloggning](sso-options.md). Den här artikeln beskriver OIDC-baserade appar.


## <a name="basic-oidc-configuration"></a>Grundläggande konfiguration av OIDC
I [snabb starts serien](add-application-portal-setup-oidc-sso.md)finns det en artikel om hur du konfigurerar enkel inloggning. I det här dokumentet får du lära dig hur du lägger till en OIDC-baserad app till din Azure-klient.

Det är bra att lägga till en app som använder OIDC-standarden för enkel inloggning är att konfigurationen är minimal. Här är en kort video som visar hur du lägger till en OIDC-baserad app till din klient organisation.

Lägga till en OIDC-baserad app i Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Läs mer om användar-och administratörs medgivande i [förstå användar-och administratörs medgivande](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Nästa steg

- [Snabb starts serie för program hantering](add-application-portal-setup-oidc-sso.md)
- [Alternativ för enkel inloggning](sso-options.md)
- [Anvisningar: Loggar in valfri Azure Active Directory-användare med programmönstret för flera klienter](../develop/howto-convert-app-to-be-multi-tenant.md)
