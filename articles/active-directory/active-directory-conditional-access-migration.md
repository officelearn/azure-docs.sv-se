---
title: Migrera klassiska principer i Azure portal | Microsoft Docs
description: Lär dig vad du behöver veta för att migrera klassiska principer i Azure-portalen.
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b6285381833526cdbdd7c1392894458a47c1cf34
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723736"
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migrera klassiska principer i Azure-portalen 


[Villkorlig åtkomst](active-directory-conditional-access-azure-portal.md) är en funktion i Azure Active directory (AD Azure) som gör det möjligt att styra hur behöriga användare har åtkomst till dina molnappar. Syftet är fortfarande samma, har lanseringen av den nya Azure-portalen införts betydande förbättringar i hur villkorlig åtkomst fungerar.

Du bör migrera principer som du inte har skapat i Azure-portalen eftersom:

- Du kan nu hantera scenarier som du inte kan hantera innan.

- Du kan minska antalet principer som du måste hantera genom att konsolidera dem.   

- Du kan hantera alla dina principer för villkorlig åtkomst på en central plats.

- Den klassiska Azure-portalen ska tas bort.   

Den här artikeln beskrivs vad du behöver veta för att migrera dina befintliga principer för villkorlig åtkomst till det nya ramverket.
 
## <a name="classic-policies"></a>Klassiska principer

I den [Azure-portalen](https://portal.azure.com), [villkorlig åtkomst - principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) är ingången till din villkorlig åtkomst principer. Men i din miljö, kan du också ha principer för villkorlig åtkomst som du inte har skapat med hjälp av den här sidan. Dessa principer kallas *klassiska principer*. Klassiska policys är villkorliga åtkomstprinciper, du har skapat i:

- den klassiska Azure-portalen
- Den klassiska portalen för Intune
- Intune App Protection-portal


På den **villkorlig åtkomst** sida, du kan komma åt dina klassiska principer genom att klicka på [ **klassisk principer (förhandsgranskning)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) i den **hantera** avsnittet. 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


Den **klassisk principer** visa ger ett alternativ för att:

- Filtrera dina klassiska principer.
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Inaktivera klassiska principer.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Granska inställningarna för klassiska principer (och inaktivera det).

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Om du har inaktiverat en klassiska princip, kan du återställa det här steget längre. Det är därför du kan ändra gruppmedlemskap i en princip för klassiska med hjälp av den **information** vyn. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Genom att ändra de valda grupperna eller genom att utesluta specifika grupper, kan du testa effekten av en inaktiverad klassiska princip för några testanvändare innan du inaktiverar principen för alla inkluderade användare och grupper. 



## <a name="azure-ad-conditional-access-policies"></a>Principer för villkorlig åtkomst till Azure AD

Du kan hantera alla dina principer på en central plats med villkorlig åtkomst i Azure-portalen. Eftersom implementeringen av hur villkorlig åtkomst har ändrats betydligt, bör du bekanta dig med grundläggande begrepp innan du migrerar dina klassiska principer.

Se:

- [Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md) mer information om grundläggande begrepp, terminologi.

- [Bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md) att få vägledning om hur du distribuerar villkorlig åtkomst i din organisation.

- [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) att bekanta dig med användargränssnittet i Azure-portalen.


 
## <a name="migration-considerations"></a>Överväganden vid migrering

I den här artikeln principer för villkorlig åtkomst i Azure AD också kallas *nya principer*.
Principer för klassiska fortsätta arbeta sida vid sida med din nya principer tills du inaktivera eller ta bort dem. 

Följande aspekter är viktiga i samband med en princip konsolidering:

- Medan klassiska principer är knutna till en specifik molnappen, kan du välja så många molnappar som du behöver i en ny princip.

- Kontroller av en klassiska princip och en ny princip för en molnapp behöver alla kontroller (*och*) uppfylls. 


- I en ny princip kan du:
 
    - Kombinera flera villkor om det krävs av ditt scenario. 

    - Välj flera bevilja krav som åtkomst styra och kombinera dem med ett logiskt *eller* (kräver en av de markerade kontrollerna) eller med ett logiskt *och* (kräver att alla de markerade kontrollerna).

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Om du vill migrera klassiska principer för **Office 365 Exchange online** som inkluderar **Exchange Active Sync** som klienten appar villkor du kanske inte konsolidera dem till en ny princip. 

Detta gäller, till exempel om du vill stödja alla apptyper som klienten. I en ny princip som har **Exchange Active Sync** som klienten appar villkor du kan inte välja andra klientprogram.

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

En konsolidering till en ny princip är inte möjligt om klassiska principerna innehåller flera villkor. En ny princip som har **Exchange Active Sync** klientprogram villkor som konfigurerats stöder inte andra villkor:   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Om du har en ny princip som har **Exchange Active Sync** som klientprogram villkoret konfigurerad, måste du se till att alla andra villkor som inte har konfigurerats. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

[App-baserade](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) klassiska principer för Office 365 Exchange Online som innehåller **Exchange Active Sync** som klienten appar villkor Tillåt **stöds** och **stöds inte** [enhetsplattformar](active-directory-conditional-access-technical-reference.md#device-platform-condition). Medan du inte kan konfigurera enskilda enhetsplattformar i en ny princip för relaterade, kan du begränsa stöd till [enhetsplattformar som stöds](active-directory-conditional-access-technical-reference.md#device-platform-condition) endast. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

Du kan konsolidera flera klassiska principer som innehåller **Exchange Active Sync** som klienten appar villkor om de har:

- Endast **Exchange Active Sync** som villkor 

- Flera krav för att bevilja åtkomst är konfigurerat

Ett vanligt scenario är konsolidering av:

- En enhetsbaserad klassiska princip från den klassiska Azure-portalen 
- En app klassiska principbaserad Intune app skydd Portal 
 
I det här fallet kan du konsolidera dina klassiska principer till en ny princip som har båda krav som valts.

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Enhetsplattformar

Klassiska principer med [app kontroller](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) är förkonfigurerade med iOS och Android som den [enhet plattform villkoret](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

I en ny princip måste du välja den [enhetsplattformar](active-directory-conditional-access-technical-reference.md#device-platform-condition) du vill stödja individuellt.

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
