---
title: Licensiera användare i Azure Active Directory | Microsoft Docs
description: Lär dig mer om att licensiera dig och dina användare i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: jeffgilb
manager: mtillman
editor: ''
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: 7dc71b33f542cf49d8a0abdb16e6e2f21e5dc2a0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713753"
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Snabbstart: Licensiera användare i Azure Active Directory
Licens-baserad Azure AD-tjänster fungerar genom att aktivera en Azure Active Directory (Azure AD)-prenumeration i Azure-klient. Efter att prenumerationen är aktiv funktioner hanteras av Azure AD-administratörer och används av licensierade användare. När du köper Enterprise Mobility + Security, Azure AD Premium eller Azure AD Basic har din klient uppdaterats med prenumeration, inklusive dess giltighetstid och förbetalda licenser. Din prenumerationsinformation antalet tilldelade eller tillgängliga licenser är tillgängliga via Azure portal under **Azure Active Directory** genom att öppna den **licenser** panelen. Den **licenser** bladet är också den bästa platsen för att hantera din licens.

Även om hur du skaffar en prenumeration är allt du behöver konfigurera betalda funktioner, måste du fortfarande tilldela användarlicenser för betald Azure AD betalda funktioner. Alla användare som ska ha åtkomst till eller som hanteras via en Azure AD betald funktionen måste tilldelas en licens. Licenstilldelning finns en mappning mellan en användare och en köpta tjänst, till exempel Azure AD Premium, Basic eller Enterprise Mobility + Security.

Du kan använda [gruppbaserade licenstilldelning](active-directory-licensing-whatis-azure-portal.md) att ställa in regler som t.ex följande:
* Alla användare i din katalog få automatiskt en licens
* Alla med lämpliga befattning hämtar en licens
* Du kan delegera beslutet till andra chefer i organisationen (med hjälp av [självbetjäning grupper](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> En detaljerad beskrivning av licenstilldelning till grupper, inklusive avancerade scenarier och Office 365-licensiering scenarier, finns i [tilldela licenser till användare av medlemskap i Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Tilldela licenser till användare och grupper
Med en aktiv prenumeration kan du först tilldela en licens till dig själv och uppdatera webbläsaren om du vill kontrollera att du se alla förväntade funktioner som ingår i din prenumeration. Nästa steg är att tilldela licenser till användare som behöver åtkomst till betald Azure AD-funktioner. Det är ett enkelt sätt att tilldela licenser att tilldela licenser till grupper av användare i stället för enskilda användare. När du tilldelar licenser till en grupp har alla medlemmar tilldelats en licens. Om användare läggs till eller tas bort från gruppen, tilldelas rätt licens automatiskt eller tas bort. 

> [!NOTE]
> Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, administratören måste ange den **användningsplats** -egenskapen för användaren. Du kan ange egenskapen under **användare** &gt; **profil** &gt; **inställningar** i Azure-portalen. När du använder gruppen licenstilldelning ärver alla användare vars användningsplats inte anges platsen för katalogen.

Tilldela en licens under **Azure Active Directory** &gt; **licenser** &gt; **alla produkter**, Välj en eller flera produkter och välj sedan **tilldela** i kommandofältet.

![Välj en licens för att tilldela](media/license-users-groups/select-license-to-assign.png)

Du kan använda den **användare och grupper** bladet att välja flera användare eller grupper eller inaktivera serviceplaner i produkten. Använda sökrutan överst för att söka efter användarnamn, gruppnamn.

![Välj en användare eller grupp för licenstilldelning](media/license-users-groups/select-user-for-license-assignment.png)

När du tilldelar licenser till en grupp kan det ta lite tid innan alla användare ärver licensen beroende på storleken på gruppen. Du kan kontrollera Bearbetningsstatus av **grupp** bladet under den **licenser** panelen.

![Licensstatus för tilldelning](media/license-users-groups/license-assignment-status.png)

Tilldelningsfel kan uppstå under Azure AD-licenstilldelning men är relativt ovanliga vid hantering av Azure AD och Enterprise Mobility + Security-produkter. Potentiella Tilldelningsfel är begränsade till:
- Tilldelningen konflikt: när en användare tidigare tilldelats en licens som inte är kompatibel med den aktuella licensen. I det här fallet kräver tilldela nya licensen tar bort det aktuella.
- Överskred tillgängliga licenser: när antalet användare i tilldelade grupper överskrider de tillgängliga licenserna, status för en användares klienttilldelning återspeglar inte går att koppla på grund av saknade licenser.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Azure AD B2B-samarbete och licensiering

B2B-samarbete kan du bjuda in gästanvändare i Azure AD-klient för att ge åtkomst till Azure AD-tjänster och alla Azure-resurser som du gör tillgängliga.  

Det är gratis för bjuda in B2B-användare och tilldela dem till ett program i Azure AD. Upp till 10 appar per gästanvändare och 3 grundläggande rapporter också är gratis för B2B-samarbete användare. Om din gästanvändare har lämpliga licenser tilldelade i partnerns Azure AD-klient, ska de licensieras med din egen samt.

Det krävs inte, men om du vill ge åtkomst till betald Azure AD-funktioner, dessa B2B gästanvändare måste ha licens med lämpliga licenser för Azure AD. En bjuda in klient med en Azure AD betald licens kan tilldela B2B-samarbete användarrättigheter till en ytterligare fem gästanvändare bjudits in till klienten. Scenarier och information finns i [B2B-samarbete licensiering vägledning](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Visa tilldelade licenser

En sammanfattning av tilldelade och tillgängliga licenser visas under **Azure Active Directory** &gt; **licenser** &gt; **alla produkter**.

![Visa licens sammanfattning](media/license-users-groups/view-license-summary.png)

En detaljerad lista över tilldelade användare och grupper är tillgänglig när du väljer en viss produkt. Den **licensierade användare** listan visas alla användare som för närvarande använder en licens och om licensen har tilldelats direkt till användaren eller om den har ärvts från en grupp.

![Visa information om licens](media/license-users-groups/view-license-detail.png)

På liknande sätt den **licensierad grupper** i listan visas alla grupper som har tilldelats licenser. Välj en användare eller grupp att öppna den **licenser** som visar alla licenser som är kopplade till det aktuella objektet.

## <a name="remove-a-license"></a>Ta bort en licens

Om du vill ta bort en licens, gå till användaren eller gruppen och öppna den **licenser** panelen. Välj licensvillkoren och klicka på **ta bort**.

![Ta bort en licens](media/license-users-groups/remove-license.png)

Licenser som ärvs av användaren från en grupp kan inte tas bort direkt. Ta bort användaren från gruppen från vilken de ärver licensen i stället.


## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du tilldelar licenser till användare och grupper i Azure AD-katalog i denna Snabbstart. 

Du kan använda följande länk för att konfigurera prenumerationen licenstilldelning i Azure AD från Azure-portalen.

> [!div class="nextstepaction"]
> [Tilldela licenser för Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 