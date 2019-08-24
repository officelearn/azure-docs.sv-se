---
title: Villkorlig åtkomst – kräver kompatibla enheter – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva kompatibla enheter
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: da1a5f54e5e989f661770d518a6753b831b59bd4
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990518"
---
# <a name="conditional-access-require-compliant-devices"></a>Villkorlig åtkomst: Kräv kompatibla enheter

Organisationer som har distribuerat Microsoft Intune kan använda den information som returneras från sina enheter för att identifiera enheter som uppfyller kraven för efterlevnad, till exempel:

* Kräver en PIN-kod för att låsa upp
* Kräver enhets kryptering
* Kräver en lägsta eller högsta operativ system version
* En enhet som kräver en enhet är inte jailbrokade eller rotad

Den här policyn för efterlevnad vidarebefordras till Azure AD där villkorlig åtkomst kan fatta beslut om att bevilja eller blockera åtkomst till resurser.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst för att kräva att enheter som har åtkomst till resurser markeras som kompatibla med organisationens efterlevnadsprinciper för Intune.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare**.
   1. Under **exkludera**väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Done** (Klar).
1. Under **molnappar eller åtgärder** > **inkluderar**väljer du **alla molnappar**.
   1. Om du måste utesluta vissa program från principen kan du välja dem från fliken **exkludera** under **Välj exkluderade molnappar** och välj **Välj**.
   1. Välj **Done** (Klar).
1. Under **åtkomst kontroller** > **tilldelar**väljer **du Kräv att enheten ska markeras som kompatibel**.
   1. Välj **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)

[Efterlevnadsprinciper för enheter fungerar med Azure AD](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
