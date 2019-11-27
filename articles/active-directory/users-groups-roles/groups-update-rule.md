---
title: Uppdatera och hantera en dynamisk grupp regel och Felsök medlemskap – Azure Active Directory | Microsoft Docs
description: Så här skapar du en regel för grupp medlemskap i Azure Portal kontrollerar du status.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422364"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Uppdatera en dynamisk grupp för att hantera medlemskap i Azure Active Directory

I Azure Active Directory (Azure AD) kan du använda regler för att fastställa grupp medlemskap baserat på användar-eller enhets egenskaper. Den här artikeln beskriver hur du konfigurerar en regel för en dynamisk grupp i Azure Portal.
Dynamiskt medlemskap stöds för säkerhets grupper eller Office 365-grupper. När en grupp medlemskaps regel används utvärderas användar-och enhets attribut för matchningar med medlemskaps regeln. När ett attributändringar ändras för en användare eller enhet bearbetas alla dynamiska grupp regler i organisationen för medlemskaps ändringar. Användare och enheter läggs till eller tas bort om de uppfyller villkoren för en grupp.

## <a name="rule-builder-in-the-azure-portal"></a>Regel verktyg i Azure Portal

Azure AD tillhandahåller ett regel verktyg för att skapa och uppdatera viktiga regler snabbare. Regel verktyget stöder konstruktion upp till fem uttryck. Regel verktyget gör det lättare att skapa en regel med några enkla uttryck, men den kan inte användas för att återskapa varje regel. Om regel verktyget inte stöder den regel som du vill skapa kan du använda text rutan.

Här följer några exempel på avancerade regler eller syntax som vi rekommenderar att du skapar med hjälp av text rutan:

- Regel med fler än fem uttryck
- Regeln för direkt rapporter
- Ställer in [operator prioritet](groups-dynamic-membership.md#operator-precedence)
- [Regler med komplexa uttryck](groups-dynamic-membership.md#rules-with-complex-expressions); till exempel `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Regel verktyget kanske inte kan visa vissa regler som skapats i text rutan. Ett meddelande kan visas om regel verktyget inte kan visa regeln. Regel verktyget ändrar inte den syntax, validering eller bearbetning av dynamiska grupp regler som stöds på något sätt.

![Lägg till medlemskaps regel för en dynamisk grupp](./media/groups-update-rule/update-dynamic-group-rule.png)

Exempel på syntax, stödda egenskaper, operatorer och värden för en medlemskaps regel finns i [regler för dynamiska medlemskap för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Så här uppdaterar du en grupp medlemskaps regel

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som finns i rollen global administratör, grupp administratör, Intune-administratör eller användar administratör i klienten.
1. Välj **grupper** > **alla grupper**.
1. Välj en grupp för att öppna dess profil.
1. På sidan profil för gruppen väljer du regler för **dynamiskt medlemskap**. Regel verktyget stöder upp till fem uttryck. Om du vill lägga till fler än fem uttryck måste du använda text rutan.

   ![Lägg till medlemskaps regel för en dynamisk grupp](./media/groups-update-rule/update-dynamic-group-rule.png)

1. För att se de anpassade tilläggs egenskaper som är tillgängliga för medlemskaps regeln:
   1. Välj **Hämta anpassade tilläggs egenskaper**
   1. Ange program-ID och välj sedan **Uppdatera egenskaper**.
1. När du har uppdaterat regeln väljer du **Spara**.

Om regeln du angav inte är giltig visas en förklaring av varför regeln inte kunde bearbetas i ett Azure-meddelande i portalen. Läs det noggrant och lär dig hur du åtgärdar regeln.

## <a name="check-processing-status-for-a-rule"></a>Kontrol lera bearbetnings status för en regel

Du kan se status för medlemskaps bearbetning och senaste uppdaterade datum på **översikts** sidan för gruppen.
  
  ![visning av status för dynamisk grupp](./media/groups-create-rule/group-status.png)

Följande status meddelanden kan visas för status för **medlemskaps bearbetning** :

- **Utvärdering**: grupp ändringen har mottagits och uppdateringarna utvärderas.
- **Bearbetar**: uppdateringar bearbetas.
- **Uppdateringen är klar**: bearbetningen har slutförts och alla tillämpliga uppdateringar har gjorts.
- **Bearbetnings fel**: bearbetningen kunde inte slutföras på grund av ett fel när medlemskaps regeln utvärderades.
- **Uppdateringen har pausats**: uppdateringar av dynamiska medlemskaps regler har pausats av administratören. MembershipRuleProcessingState är inställd på pausad.

Följande status meddelanden kan visas för **medlemskapets senast uppdaterade** status:

- **Datum och tid**: när medlemskapet senast uppdaterades.
- **Pågår**: uppdateringar pågår just nu.
- **Okänd**: det går inte att hämta den senaste uppdaterings tiden. Gruppen kan vara ny.

Om ett fel inträffar när medlemskaps regeln för en grupp bearbetas visas en avisering överst på **sidan Översikt** för gruppen. Om inga väntande dynamiska medlemskaps uppdateringar kan bearbetas för alla grupper i klient organisationen för mer än 24 timmar, visas en avisering överst i **alla grupper**.

![bearbetar aviseringar om fel meddelande](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om hur du arbetar med dynamiska grupper i Azure AD.

- En fullständig referens till en dynamisk regel struktur finns i [syntax för dynamisk medlemskaps regel](groups-dynamic-membership.md).
- [Skapa en statisk medlemskaps grupp och Lägg till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md).
