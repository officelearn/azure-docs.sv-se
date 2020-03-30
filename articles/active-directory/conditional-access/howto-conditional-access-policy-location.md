---
title: Villkorlig åtkomst - Blockera åtkomst efter plats - Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att blockera åtkomst till resurser efter IP-plats
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
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295187"
---
# <a name="conditional-access-block-access-by-location"></a>Villkorlig åtkomst: Blockera åtkomst efter plats

Med platsvillkoret i Villkorlig åtkomst kan du styra åtkomsten till dina molnappar baserat på en användares nätverksplats. Platsvillkoret används ofta för att blockera åtkomst från länder där din organisation vet att trafiken inte ska komma från.

## <a name="define-locations"></a>Definiera platser

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till**namngivna platser**för Azure Active**Directory-säkerhets** >  **Azure Active Directory** > **villkorlig åtkomst** > .
1. Välj **Ny plats**.
1. Ge din plats ett namn.
1. Välj **IP-intervall** om du känner till de specifika externt tillgängliga IPv4-adressintervall som utgör den platsen eller **Länder/Regioner**.
   1. Ange **IP-intervall** eller välj **länder/regioner** för den plats du anger.
      * Om du väljer Länder/Regioner kan du välja att inkludera okända områden.
1. Välj **Spara**

Mer information om platsvillkoret i villkorlig åtkomst finns i artikeln [Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

1. Logga in på **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Ge din policy ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på sina principer.
1. Under **Tilldelningar**väljer du **Användare och grupper**
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Välj **Done** (Klar).
1. Under **Molnappar eller åtgärder** > **Inkludera**väljer du **Alla molnappar**och väljer **Klar**.
1. Under **förhållanden** > **plats**.
   1. Ange **Konfigurera** till **Ja**
   1. **Inkludera** välj **markerade platser**
   1. Välj den blockerade plats som du skapade för din organisation.
   1. Klicka på **Välj** > **Klar** > **Done**.
1. Under **Villkor** > **klientappar (förhandsversion)** ställer du in **Konfigurera** till **Ja**och väljer **Klar**.
1. Under **Åtkomstkontroller** > **Blockera**och välj **Välj**.
1. Bekräfta dina inställningar och ställ in **Aktivera princip** på **På**.
1. Välj **Skapa** för att skapa för att aktivera din princip.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)
