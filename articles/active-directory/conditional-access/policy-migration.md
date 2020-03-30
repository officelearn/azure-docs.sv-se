---
title: Migrera principer för villkorlig åtkomst – Azure Active Directory
description: Läs om vad du behöver veta för att migrera klassiska principer i Azure-portalen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185912"
---
# <a name="conditional-access-classic-policy-migration"></a>Klassisk principmigrering av principer för villkorlig åtkomst

Villkorlig åtkomst är det verktyg som används av Azure Active Directory för att sammanföra signaler, fatta beslut och tillämpa organisationsprinciper. Villkorlig åtkomst är kärnan i det nya identitetsdrivna kontrollplanet. Syftet är fortfarande detsamma, men lanseringen av den nya Azure-portalen har introducerat betydande förbättringar av hur villkorlig åtkomst fungerar.

Överväg att migrera de principer som du inte har skapat i Azure-portalen eftersom:

- Du kan nu ta itu med scenarier som du inte kunde hantera tidigare.
- Du kan minska antalet principer som du måste hantera genom att konsolidera dem.
- Du kan hantera alla principer för villkorlig åtkomst på en central plats.
- Den klassiska Azure-portalen dras tillbaka.

I den här artikeln beskrivs vad du behöver veta för att migrera dina befintliga principer för villkorlig åtkomst till det nya ramverket.

## <a name="classic-policies"></a>Klassiska policyer

I [Azure-portalen](https://portal.azure.com)finns principer för villkorlig åtkomst villkorlig åtkomst i **Azure Active Directory** > **Security** > **.** Din organisation kan också ha äldre principer för villkorlig åtkomst som inte har skapats med den här sidan. Dessa principer kallas *klassiska principer*. Klassiska principer är principer för villkorlig åtkomst som du har skapat i:

- Den klassiska Azure-portalen
- Den klassiska portalen Intune
- Portalen för skydd av Intune-appen

På sidan **Villkorlig åtkomst** kan du komma åt dina klassiska principer genom att klicka på [**Klassiska principer**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) i avsnittet **Hantera.** 

![Villkorlig åtkomst i Azure AD visar klassisk vy för principer](./media/policy-migration/71.png)

Med vyn **Klassiska principer** kan du välja att:

- Filtrera dina klassiska principer.
- Inaktivera klassiska principer.
- Granska inställningarna för en klassisk princip och inaktivera den.

   ![Klassisk principinformation, inklusive befintlig principkonfiguration](./media/policy-migration/74.png)

> [!WARNING]
> När en klassisk princip har inaktiverats kan den inte återaktiveras.

Med informationsvyn för en klassisk princip kan du dokumentera inställningarna, ändra de inkluderade eller uteslutna grupperna och inaktivera principen.

![Policydetaljer - Grupper som ska inkluderas eller uteslutas](./media/policy-migration/75.png)

Genom att ändra de valda grupperna eller genom att utesluta specifika grupper kan du testa effekten av en inaktiverad klassisk princip för några testanvändare innan du inaktiverar principen för alla inkluderade användare och grupper.
 
## <a name="migration-considerations"></a>Överväganden vid migrering

I den här artikeln kallas Azure AD-principer för villkorlig åtkomst även *för nya principer*.
Dina klassiska principer fortsätter att fungera sida vid sida med dina nya principer tills du inaktiverar eller tar bort dem. 

Följande aspekter är viktiga i samband med en politisk konsolidering:

- Även om klassiska principer är kopplade till en specifik molnapp kan du välja så många molnappar som du behöver i en ny princip.
- Kontroller av en klassisk princip och en ny princip för en molnapp kräver att alla kontroller *(OCH)* uppfylls. 
- I en ny princip kan du:
   - Kombinera flera villkor om det krävs av ditt scenario. 
   - Välj flera bidragskrav som åtkomstkontroll och kombinera dem med ett logiskt *ELLER* (kräver en av de markerade kontrollerna) eller med en logisk *OCH* (kräver alla markerade kontroller).

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Om du vill migrera klassiska principer för **Office 365 Exchange online** som innehåller Exchange Active **Sync** som klientappars villkor kanske du inte kan konsolidera dem till en ny princip. 

Detta är till exempel fallet om du vill stödja alla klientapptyper. I en ny princip som har **Villkoret Exchange Active Sync** som klientappar kan du inte välja andra klientappar.

![Villkorlig åtkomst som väljer klientappar](./media/policy-migration/64.png)

En konsolidering till en ny princip är inte heller möjlig om dina klassiska principer innehåller flera villkor. En ny princip som har **Villkoret Exchange Active Sync** som klientappar konfigurerat stöder inte andra villkor:   

![Exchange ActiveSync stöder inte de valda villkoren](./media/policy-migration/08.png)

Om du har en ny princip som har **Villkoret Exchange Active Sync** som klientappar konfigurerat måste du se till att alla andra villkor inte är konfigurerade. 

![Villkor för villkorlig åtkomst](./media/policy-migration/16.png)
 
Appbaserade klassiska principer för Office 365 Exchange Online som innehåller **Exchange Active Sync** som klientappar tillåter enhetsplattformar som **stöds** och **inte stöds.** Du kan inte konfigurera enskilda enhetsplattformar i en relaterad ny princip, men du kan begränsa stödet till [enhetsplattformar som stöds.](concept-conditional-access-conditions.md#device-platforms) 

![Villkorsstyrd åtkomst välj Exchange ActiveSync](./media/policy-migration/65.png)

Du kan konsolidera flera klassiska principer som innehåller **Exchange Active Sync** som klientapparvillkor om de har:

- Endast **Exchange Active Sync** som villkor 
- Flera krav för att bevilja åtkomst konfigurerad

Ett vanligt scenario är konsolideringen av:

- En enhetsbaserad klassisk princip från den klassiska Azure-portalen 
- En appbaserad klassisk princip i Intune-appskyddsportalen 
 
I det här fallet kan du konsolidera dina klassiska principer till en ny princip som har båda kraven valda.

![Bevilja villkorad åtkomstkontroll](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Enhetsplattformar

Klassiska principer med appbaserade kontroller är förkonfigurerade med iOS och Android som enhetsplattformsvillkor. 

I en ny princip måste du välja de [enhetsplattformar](concept-conditional-access-conditions.md#device-platforms) som du vill stödja individuellt.

![Val av plattformar för villkorsstyrda accessenheter](./media/policy-migration/41.png)

## <a name="next-steps"></a>Nästa steg

- [Använd endast rapportläge för villkorlig åtkomst för att fastställa effekten av nya principbeslut.](concept-conditional-access-report-only.md)
- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst läser du [gemensamma principer för villkorlig åtkomst](concept-conditional-access-policy-common.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö läser du artikeln [Så här planerar du distributionen för villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md). 
