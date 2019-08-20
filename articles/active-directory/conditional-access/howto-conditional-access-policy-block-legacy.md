---
title: Villkorlig åtkomst – blockera äldre autentisering – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att blockera bakåtkompatibla autentiseringsprotokoll
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
ms.openlocfilehash: d802e48231a03fbf3fee9439894371188f31d5e1
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576659"
---
# <a name="conditional-access-block-legacy-authentication"></a>Villkorlig åtkomst: Blockera äldre autentisering

På grund av den ökade risken som är associerad med äldre autentiseringsprotokoll rekommenderar Microsoft att organisationer blockerar autentiseringsbegäranden med dessa protokoll och kräver modern autentisering.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst som kräver att de tilldelade administrativa rollerna utför Multi-Factor Authentication.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare**.
   1. Under **exkludera**väljer **du användare och grupper** och väljer alla konton som måste upprätthålla möjligheten att använda äldre autentisering. 
   1. Välj **Done** (Klar).
1. Under **molnappar eller åtgärder** > **inkluderar**väljer du **alla molnappar**.
   1. Om du måste utesluta vissa program från principen kan du välja dem från fliken **exkludera** under **Välj exkluderade molnappar** och välj **Välj**.
   1. Välj **Done** (Klar).
1. Under **villkor** > **klient program (för hands version)** anger du **Konfigurera** till **Ja**.
   1. Kontrol lera endast de **mobila apparna och skriv bords klienterna** > **andra klienter**.
   2. Välj **Done** (Klar).
1. Under **åtkomst kontroller** > **bevilja**väljer du **blockera åtkomst**.
   1. Välj **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
