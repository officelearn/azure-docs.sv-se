---
title: Skapa en dynamisk grupp och kontrol lera status-Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: cb4f9d2f78857231d0ecd81a2538a75b4b8a2f74
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650310"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Skapa en dynamisk grupp och kontrol lera status

I Azure Active Directory (Azure AD) kan du använda regler för att fastställa grupp medlemskap baserat på användar-eller enhets egenskaper. Den här artikeln beskriver hur du konfigurerar en regel för en dynamisk grupp i Azure Portal.
Dynamiskt medlemskap stöds för säkerhets grupper eller Office 365-grupper. När en grupp medlemskaps regel används utvärderas användar-och enhets attribut för matchningar med medlemskaps regeln. När ett attributändringar ändras för en användare eller enhet bearbetas alla dynamiska grupp regler i organisationen för medlemskaps ändringar. Användare och enheter läggs till eller tas bort om de uppfyller villkoren för en grupp. Säkerhets grupper kan användas för antingen enheter eller användare, men Office 365-grupper kan bara vara användar grupper.

Exempel på syntax, stödda egenskaper, operatorer och värden för en medlemskaps regel finns i [regler för dynamiska medlemskap för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Så här skapar du en grupp medlemskaps regel

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som finns i rollen global administratör, Intune-administratör eller användar administratör i klienten.
2. Välj **grupper**.
3. Välj **alla grupper**och välj sedan **ny grupp**.

   ![Välj kommandot för att lägga till en ny grupp](./media/groups-create-rule/new-group-creation.png)

4. På sidan **grupp** anger du ett namn och en beskrivning för den nya gruppen. Välj en **medlemskaps typ** för antingen användare eller enheter och välj sedan **Lägg till dynamisk fråga**. Regel verktyget stöder upp till fem uttryck. Om du vill lägga till ett sjätte eller efterföljande uttryck måste du använda text rutan.

   ![Lägg till medlemskaps regel för en dynamisk grupp](./media/groups-create-rule/add-dynamic-group-rule.png)

5. För att se de anpassade tilläggs egenskaper som är tillgängliga för din medlemskaps fråga
   1. Välj **Hämta anpassade tilläggs egenskaper**
   2. Ange program-ID och välj sedan **Uppdatera egenskaper**.
6. När du har skapat regeln väljer du **Spara**.
7. Skapa gruppen genom att välja **skapa** på sidan **ny grupp** .

Om regeln du angav inte är giltig visas en förklaring av varför regeln inte kunde bearbetas i ett Azure-meddelande i portalen. Läs det noggrant och lär dig hur du åtgärdar regeln.

## <a name="turn-on-or-off-welcome-email"></a>Aktivera eller inaktivera välkomst meddelande

När en ny Office 365-grupp skapas, skickas ett välkomst meddelande till de användare som läggs till i gruppen. Senare, om några attribut för en användare eller enhet ändras, bearbetas alla dynamiska grupp regler i organisationen för medlemskaps ändringar. Användare som läggs till får även välkomst meddelandet. Du kan inaktivera det här beteendet i [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

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

* &lt;**Datum och tid**&gt;: Den senaste gången som medlemskapet uppdaterades.
* **Pågår**: Uppdateringar pågår just nu.
* **Okänd**: Det går inte att hämta den senaste uppdaterings tiden. Gruppen kan vara ny.

Om ett fel inträffar när medlemskaps regeln för en grupp bearbetas visas en avisering överst på **sidan Översikt** för gruppen. Om inga väntande dynamiska medlemskaps uppdateringar kan bearbetas för alla grupper i klient organisationen för mer än 24 timmar, visas en avisering överst i **alla grupper**.

![bearbetar aviseringar om fel meddelande](./media/groups-create-rule/processing-error.png)

De här artiklarna innehåller ytterligare information om grupper i Azure Active Directory.

* [Visa befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
