---
title: Villkorlig åtkomst - Blockera äldre autentisering - Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att blockera äldre autentiseringsprotokoll
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295223"
---
# <a name="conditional-access-block-legacy-authentication"></a>Villkorlig åtkomst: Blockera äldre autentisering

På grund av den ökade risken som är associerad med äldre autentiseringsprotokoll rekommenderar Microsoft att organisationer blockerar autentiseringsbegäranden med hjälp av dessa protokoll och kräver modern autentisering.

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst för att blockera äldre autentiseringsbegäranden. Den här principen sätts i [läge endast för rapport](howto-conditional-access-report-only.md) för att starta så att administratörer kan avgöra vilken inverkan de kommer att ha på befintliga användare. När administratörer är bekväma med att principen gäller som de vill kan de växla till **På** eller arrangera distributionen genom att lägga till specifika grupper och utesluta andra.

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Under **Uteslut**väljer du **Användare och grupper** och väljer alla konton som måste ha möjlighet att använda äldre autentisering. Uteslut minst ett konto för att förhindra att du blir utelåst. Om du inte utesluter något konto kan du inte skapa den här principen.
   1. Välj **Done** (Klar).
1. Under **Molnappar eller -åtgärder**väljer du **Alla molnappar**.
   1. Välj **Done** (Klar).
1. Under **Villkor** > **klientappar (förhandsgranskning)** anger du **Konfigurera** till **Ja**.
   1. Markera bara rutorna **Mobilappar och skrivbordsklienter** > **Andra klienter**.
   1. Välj **Done** (Klar).
1. Under **Åtkomstkontroller** > **Bevilja**väljer du Blockera **åtkomst**.
   1. Välj **Välj**.
1. Bekräfta dina inställningar och ange **Aktivera princip** till **endast rapport**.
1. Välj **Skapa** för att skapa för att aktivera din princip.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)
