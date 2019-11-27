---
title: Migrera principer för villkorlig åtkomst – Azure Active Directory
description: Lär dig vad du behöver veta för att migrera klassiska principer i Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380544"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Vad är en princip migrering i Azure Active Directory villkorlig åtkomst? 

[Villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) är en funktion i Azure Active Directory (Azure AD) som gör att du kan styra hur auktoriserade användare får åtkomst till dina molnappar. Även om syftet fortfarande är detsamma, har lanseringen av den nya Azure Portal infört betydande förbättringar av hur villkorlig åtkomst fungerar.

Överväg att migrera principerna som du inte har skapat i Azure Portal eftersom:

- Nu kan du adressera scenarier som du inte kan hantera tidigare.
- Du kan minska antalet principer som du måste hantera genom att konsolidera dem.   
- Du kan hantera alla principer för villkorlig åtkomst på en central plats.
- Den klassiska Azure-portalen kommer att dras tillbaka.   

Den här artikeln förklarar vad du behöver veta för att migrera dina befintliga principer för villkorlig åtkomst till det nya ramverket.
 
## <a name="classic-policies"></a>Klassiska principer

På sidan [Azure Portal](https://portal.azure.com)är sidan [villkorlig åtkomst-principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) din start punkt för dina principer för villkorlig åtkomst. I din miljö kan du dock även ha principer för villkorlig åtkomst som du inte har skapat med hjälp av den här sidan. Dessa principer kallas *klassiska principer*. Klassiska principer är principer för villkorlig åtkomst, du har skapat i:

- Den klassiska Azure-portalen
- Den klassiska Intune-portalen
- Intune-appskydd Portal

På sidan **villkorlig åtkomst** kan du komma åt dina klassiska principer genom att klicka på [**klassiska principer (för hands version)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) i avsnittet **Hantera** . 

![Azure Active Directory](./media/policy-migration/71.png)

Vyn **klassiska principer** ger dig ett alternativ för att:

- Filtrera dina klassiska principer.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Inaktivera klassiska principer.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Granska inställningarna för en klassisk princip (och inaktivera den).

   ![Azure Active Directory](./media/policy-migration/74.png)

Om du har inaktiverat en klassisk princip kan du inte återställa det här steget längre. Det är därför du kan ändra grupp medlemskapet i en klassisk princip med hjälp av vyn **information** . 

![Azure Active Directory](./media/policy-migration/75.png)

Genom att antingen ändra de valda grupperna eller genom att utesluta vissa grupper kan du testa resultatet av en inaktive rad klassisk princip för några test användare innan du inaktiverar principen för alla inkluderade användare och grupper. 

## <a name="azure-ad-conditional-access-policies"></a>Principer för villkorlig åtkomst för Azure AD

Med villkorlig åtkomst i Azure Portal kan du hantera alla principer på en central plats. Eftersom implementeringen av hur villkorlig åtkomst har ändrats bör du bekanta dig med de grundläggande begreppen innan du migrerar dina klassiska principer.

Se:

- [Vad är villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md) för att lära dig om de grundläggande begreppen och terminologin.
- [Metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md) för att få vägledning om hur du distribuerar villkorlig åtkomst i din organisation.
- [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md) för att bekanta dig med användar gränssnittet i Azure Portal.
 
## <a name="migration-considerations"></a>Överväganden vid migrering

I den här artikeln kallas principer för villkorlig åtkomst för Azure AD också *nya principer*.
De klassiska principerna fortsätter att fungera sida vid sida med dina nya principer tills du inaktiverar eller tar bort dem. 

Följande aspekter är viktiga i samband med en princip konsolidering:

- Även om de klassiska principerna är knutna till en speciell molnbaserad app, kan du välja så många molnappar som du behöver i en ny princip.
- Kontroller av en klassisk princip och en ny princip för en molnbaserad app kräver att alla kontroller (*och*) är uppfyllda. 
- I en ny princip kan du:
   - Kombinera flera villkor om ditt scenario kräver det. 
   - Välj flera bidrags krav som åtkomst kontroll och kombinera dem med ett logiskt *eller* (Kräv en av de valda kontrollerna) eller med ett logiskt *och* (Kräv alla valda kontroller).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Om du vill migrera klassiska principer för **Office 365 Exchange Online** som innehåller **Exchange Active Sync** som klient program villkor, kanske du inte kan konsolidera dem till en ny princip. 

Detta är till exempel fallet om du vill stödja alla typer av klient program. I en ny princip som har **Exchange Active Sync** som klient program villkor kan du inte välja andra klient program.

![Azure Active Directory](./media/policy-migration/64.png)

Det går inte heller att utföra en konsolidering i en ny princip om de klassiska principerna innehåller flera villkor. En ny princip som har kon figurer ATS för **Exchange Active Sync** som klient program inte stöder andra villkor:   

![Azure Active Directory](./media/policy-migration/08.png)

Om du har en ny princip som har konfigurerat villkor för **Exchange Active Sync** som klient program, måste du se till att alla andra villkor inte är konfigurerade. 

![Azure Active Directory](./media/policy-migration/16.png)
 
[App-baserade](technical-reference.md#approved-client-app-requirement) klassiska principer för Office 365 Exchange Online som omfattar **Exchange Active Sync** som klient program villkor tillåter **stöd** för [plattformar](technical-reference.md#device-platform-condition)som stöds och **inte stöds** . Även om du inte kan konfigurera enskilda enhets plattformar i en relaterad ny princip kan du begränsa stödet till endast [plattformar som stöds](technical-reference.md#device-platform-condition) . 

![Azure Active Directory](./media/policy-migration/65.png)

Du kan konsolidera flera klassiska principer som omfattar **Exchange Active Sync** som klient program villkor om de har:

- Endast **Exchange Active Sync** som villkor 
- Flera krav för att bevilja åtkomst konfigurerat

Ett vanligt scenario är konsolideringen av:

- En enhets beroende klassisk princip från den klassiska Azure-portalen 
- En app-baserad klassisk princip i Intunes app Protection-Portal 
 
I det här fallet kan du konsolidera dina klassiska principer till en ny princip som har båda de krav som valts.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Enhets plattformar

Klassiska principer med [app-baserade kontroller](technical-reference.md#approved-client-app-requirement) är förkonfigurerade med iOS och Android som [enhets plattforms villkor](technical-reference.md#device-platform-condition). 

I en ny princip måste du välja de [enhets plattformar](technical-reference.md#device-platform-condition) som du vill stödja individuellt.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst, se [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö, se [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md). 
