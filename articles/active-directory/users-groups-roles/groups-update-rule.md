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
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce3bce5e2656efea0a4fc3d7aa6be46f1e6926ec
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657351"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Uppdatera en dynamisk grupp för att hantera medlemskap i Azure Active Directory

I Azure Active Directory (Azure AD) kan du använda regler för att fastställa grupp medlemskap baserat på användar-eller enhets egenskaper. Den här artikeln beskriver hur du konfigurerar en regel för en dynamisk grupp i Azure Portal.
Dynamiskt medlemskap stöds för säkerhets grupper eller Office 365-grupper. När en grupp medlemskaps regel används utvärderas användar-och enhets attribut för matchningar med medlemskaps regeln. När ett attributändringar ändras för en användare eller enhet bearbetas alla dynamiska grupp regler i organisationen för medlemskaps ändringar. Användare och enheter läggs till eller tas bort om de uppfyller villkoren för en grupp.

Exempel på syntax, stödda egenskaper, operatorer och värden för en medlemskaps regel finns i [regler för dynamiska medlemskap för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Så här uppdaterar du en grupp medlemskaps regel

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som finns i rollen global administratör, Intune-administratör eller användar administratör i klienten.
1. Välj **grupper** > **alla grupper**.
1. Välj en grupp för att öppna dess profil.
1. På sidan profil för gruppen väljer du regler för **dynamiskt medlemskap**. Regel verktyget stöder upp till fem uttryck. Om du vill lägga till ett sjätte eller efterföljande uttryck måste du använda text rutan.

   ![Lägg till medlemskaps regel för en dynamisk grupp](./media/groups-update-rule/update-dynamic-group-rule.png)

1. För att se de anpassade tilläggs egenskaper som är tillgängliga för medlemskaps regeln:
   1. Välj **Hämta anpassade tilläggs egenskaper**
   2. Ange program-ID och välj sedan **Uppdatera egenskaper**.
1. När du har uppdaterat regeln väljer du **Spara**.

Om regeln du angav inte är giltig visas en förklaring av varför regeln inte kunde bearbetas i ett Azure-meddelande i portalen. Läs det noggrant och lär dig hur du åtgärdar regeln.

## <a name="check-processing-status-for-a-rule"></a>Kontrol lera bearbetnings status för en regel

Du kan se status för medlemskaps bearbetning och senaste uppdaterade datum på **översikts** sidan för gruppen.
  
  ![visning av status för dynamisk grupp](./media/groups-create-rule/group-status.png)

Följande status meddelanden kan visas för status för **medlemskaps bearbetning** :

* **Utvärdera**:  Grupp ändringen har mottagits och uppdateringarna utvärderas.
* **Bearbetar**: Uppdateringar bearbetas.
* **Uppdateringen är klar**: Bearbetningen har slutförts och alla tillämpliga uppdateringar har gjorts.
* **Bearbetnings fel**:  Det gick inte att slutföra bearbetningen på grund av ett fel när medlemskaps regeln utvärderades.
* **Uppdateringen**har pausats: Uppdateringar av dynamiska medlemskaps regler har pausats av administratören. MembershipRuleProcessingState är inställd på pausad.

Följande status meddelanden kan visas för medlemskapets **senast uppdaterade** status:

* **Datum och tid**: Den senaste gången som medlemskapet uppdaterades.
* **Pågår**: Uppdateringar pågår just nu.
* **Okänd**: Det går inte att hämta den senaste uppdaterings tiden. Gruppen kan vara ny.

Om ett fel inträffar när medlemskaps regeln för en grupp bearbetas visas en avisering överst på **sidan Översikt** för gruppen. Om inga väntande dynamiska medlemskaps uppdateringar kan bearbetas för alla grupper i klient organisationen för mer än 24 timmar, visas en avisering överst i **alla grupper**.

![bearbetar aviseringar om fel meddelande](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om hur du arbetar med dynamiska grupper i Azure AD.

* En fullständig referens till en dynamisk regel struktur finns i [syntax för dynamisk medlemskaps regel](groups-dynamic-membership.md).
* [Skapa en statisk medlemskaps grupp och Lägg till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md).
