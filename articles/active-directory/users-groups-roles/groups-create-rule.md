---
title: Skapa eller redigera en dynamisk grupp och få status - Azure AD | Microsoft-dokument
description: Så här skapar eller uppdaterar du en gruppmedlemskapsregel i Azure-portalen och kontrollerar dess bearbetningsstatus.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266382"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Skapa eller uppdatera en dynamisk grupp i Azure Active Directory

I Azure Active Directory (Azure AD) kan du använda regler för att bestämma gruppmedlemskap baserat på användar- eller enhetsegenskaper. Den här artikeln beskriver hur du konfigurerar en regel för en dynamisk grupp i Azure-portalen.
Dynamiskt medlemskap stöds för säkerhetsgrupper eller Office 365-grupper. När en gruppmedlemskapsregel tillämpas utvärderas användar- och enhetsattribut för matchningar med medlemskapsregeln. När ett attribut ändras för en användare eller enhet bearbetas alla dynamiska gruppregler i organisationen för medlemskapsändringar. Användare och enheter läggs till eller tas bort om de uppfyller villkoren för en grupp. Säkerhetsgrupper kan användas för antingen enheter eller användare, men Office 365-grupper kan bara vara användargrupper.

## <a name="rule-builder-in-the-azure-portal"></a>Regelbyggare i Azure-portalen

Azure AD tillhandahåller en regelbyggare för att skapa och uppdatera dina viktiga regler snabbare. Regelbyggaren stöder konstruktionen upp till fem uttryck. Regelverktyget gör det enklare att skapa en regel med några enkla uttryck, men den kan inte användas för att återskapa alla regler. Om regelverktyget inte stöder den regel som du vill skapa kan du använda textrutan.

Här är några exempel på avancerade regler eller syntax som vi rekommenderar att du konstruerar med hjälp av textrutan:

- Regel med fler än fem uttryck
- Regeln om direktrapporter
- Ange [operatörsprioritet](groups-dynamic-membership.md#operator-precedence)
- [Regler med komplexa uttryck](groups-dynamic-membership.md#rules-with-complex-expressions); till exempel`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Regelbyggaren kanske inte kan visa vissa regler som har skapats i textrutan. Du kan se ett meddelande när regelverktyget inte kan visa regeln. Regelverktyget ändrar inte den syntax, validering eller bearbetning som stöds av dynamiska gruppregler på något sätt.

![Lägga till medlemskapsregel för en dynamisk grupp](./media/groups-create-rule/update-dynamic-group-rule.png)

Exempel på syntax, egenskaper, operatorer och värden som stöds för en medlemskapsregel finns [i Dynamiska medlemskapsregler för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Så här skapar du en gruppmedlemskapsregel

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som finns i rollen Global administratör, Intune-administratör eller Användaradministratör i klienten.
1. Sök efter och välj **Grupper**.
1. Välj **Alla grupper**och välj Ny **grupp**.

   ![Markera kommandot för att lägga till en ny grupp](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. På sidan **Grupp** anger du ett namn och en beskrivning för den nya gruppen. Välj en **medlemstyp** för antingen användare eller enheter och välj sedan **Lägg till dynamisk fråga**. Regelverktyget stöder upp till fem uttryck. Om du vill lägga till fler än fem uttryck måste du använda textrutan.

   ![Lägga till medlemskapsregel för en dynamisk grupp](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Så här ser du de anpassade tilläggsegenskaperna som är tillgängliga för din medlemsfråga:
   1. Välj **Hämta egenskaper för anpassat tillägg**
   1. Ange program-ID:t och välj sedan **Uppdatera egenskaper**.
1. När du har skapat regeln väljer du **Spara**.
1. Välj **Skapa** på sidan **Ny grupp** om du vill skapa gruppen.

Om regeln du angav inte är giltig visas en förklaring till varför regeln inte kunde bearbetas i ett Azure-meddelande i portalen. Läs den noggrant för att förstå hur du åtgärdar regeln.

## <a name="to-update-an-existing-rule"></a>Så här uppdaterar du en befintlig regel

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som finns i rollen Global administratör, Gruppadministratör, Intune-administratör eller Användaradministratör i klienten.
1. Välj **Grupper alla** > **grupper**.
1. Välj en grupp för att öppna profilen.
1. Välj **Dynamiska medlemskapsregler**på profilsidan för gruppen . Regelverktyget stöder upp till fem uttryck. Om du vill lägga till fler än fem uttryck måste du använda textrutan.

   ![Lägga till medlemskapsregel för en dynamisk grupp](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Så här ser du de anpassade tilläggsegenskaperna som är tillgängliga för medlemskapsregeln:
   1. Välj **Hämta egenskaper för anpassat tillägg**
   1. Ange program-ID:t och välj sedan **Uppdatera egenskaper**.
1. När du har uppdaterat regeln väljer du **Spara**.

## <a name="turn-on-or-off-welcome-email"></a>Aktivera eller inaktivera välkomstmeddelande

När en ny Office 365-grupp skapas skickas ett välkomstmeddelande till de användare som läggs till i gruppen. Senare, om några attribut för en användare eller enhet ändras, bearbetas alla dynamiska gruppregler i organisationen för medlemskapsändringar. Användare som läggs till får sedan också välkomstmeddelandet. Du kan inaktivera det här beteendet i [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Kontrollera bearbetningsstatus för en regel

Du kan se statusen för medlemskapsbearbetning och det senast uppdaterade datumet på sidan **Översikt** för gruppen.
  
  ![visning av dynamisk gruppstatus](./media/groups-create-rule/group-status.png)

Följande statusmeddelanden kan visas för **status för medlemskapsbearbetning:**

- **Utvärdering**: Gruppändringen har tagits emot och uppdateringarna utvärderas.
- **Bearbetning:** Uppdateringar bearbetas.
- **Uppdateringen är klar**: Bearbetningen har slutförts och alla tillämpliga uppdateringar har gjorts.
- **Bearbetningsfel**: Bearbetningen kunde inte slutföras på grund av ett fel som utvärderade medlemskapsregeln.
- **Uppdatera pausad**: Uppdateringar av dynamiska medlemskapsregeln har pausats av administratören. MembershipRuleProcessingState är inställt på "Pausad".

Följande statusmeddelanden kan visas för **medlemskap senast uppdaterad** status:

- &lt;**Datum och tid**&gt;: Sista gången medlemskapet uppdaterades.
- **Pågår**: Uppdateringar pågår för närvarande.
- **Okänd**: Den senaste uppdateringstiden kan inte hämtas. Gruppen kan vara ny.

Om ett fel uppstår när medlemskapsregeln för en viss grupp bearbetas visas en avisering högst upp på **sidan Översikt** för gruppen. Om inga väntande dynamiska medlemskapsuppdateringar kan bearbetas för alla grupper i klienten i mer än 24 timmar visas en avisering överst i **Alla grupper**.

![varningar för bearbetning av felmeddelanden](./media/groups-create-rule/processing-error.png)

De här artiklarna innehåller ytterligare information om grupper i Azure Active Directory.

- [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
