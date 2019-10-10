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
ms.openlocfilehash: 3ef8ab4b24d223f372ae3704b00ba6ff090fb039
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169986"
---
# <a name="conditional-access-block-legacy-authentication"></a>Villkorlig åtkomst: blockera äldre autentisering

På grund av den ökade risken som är associerad med äldre autentiseringsprotokoll rekommenderar Microsoft att organisationer blockerar autentiseringsbegäranden med dessa protokoll och kräver modern autentisering.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst för att blockera äldre autentiseringsbegäranden.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
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
   1. Kontrol lera bara rutorna **mobilappar och skriv bords klienter** > **andra klienter**.
   2. Välj **Done** (Klar).
1. Under **åtkomst kontroller**@no__t 1-**tilldelning**väljer du **blockera åtkomst**.
   1. Välj **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
