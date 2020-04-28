---
title: Vad är privilegierad identitetshantering? - Azure AD | Microsoft-dokument
description: Ger en översikt över Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb0cc61b61328df86c27498a1007f2372fb9548
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867451"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Vad är Azure AD Privileged Identity Management?

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) är en tjänst som du kan använda för att hantera, kontrollera och övervaka åtkomst till viktiga resurser i din organisation. Dessa resurser omfattar resurser i Azure AD, Azure och andra Microsoft Online-tjänster som Office 365 eller Microsoft Intune.

## <a name="reasons-to-use"></a>Skäl att använda

Organisationer behöver minimera antalet personer som har åtkomst till skyddad information eller skyddade resurser för att därigenom minska risken för att användare som vill vålla skada får åtkomst till informationen eller att behöriga användare oavsiktligt påverkar känsliga resurser. Användare måste emellertid fortfarande kunna utföra privilegierade åtgärder i Azure AD, Azure, Office 365 och SaaS-appar. Organisationer kan ge användare privilegierad just-in-time-åtkomst (JIT) till Azure-resurser och Azure AD. Det finns ofta behov av att övervaka vad användarna gör med sina administratörsprivilegier.

## <a name="what-does-it-do"></a>Vad gör den?

Privilegierad identitetshantering ger tidsbaserad och godkännandebaserad rollaktivering för att minska riskerna för överdriven, onödig eller missbrukad åtkomstbehörigheter för resurser som du bryr dig om. Här är några av de viktigaste funktionerna i Privilegierad identitetshantering:

- Ge privilegierad **just-in-time**-åtkomst till Azure AD och Azure-resurser
- Tilldela **tidsbunden** åtkomst till resurser med hjälp av start- och slutdatum
- Kräv **godkännande** för att aktivera privilegierade roller
- Framtvinga **multifaktorautentisering** för aktivering av roller
- Använd **motivering** för att förstå varför användare aktiverar
- Få **meddelanden** när privilegierade roller aktiveras
- Utför **åtkomstgranskningar** för att se till att användare fortfarande behöver ha vissa roller
- Ladda ned **granskningshistorik** för intern eller extern granskning

## <a name="what-can-i-do-with-it"></a>Vad kan jag göra med den?

När du har konfigurerat Privilegierad identitetshantering visas alternativ **för Uppgifter,** **Hantera**och **Aktivitet** på den vänstra navigeringsmenyn. Som administratör kan du välja mellan att hantera **Azure AD-roller** och **Azure-resurs**-roller. När du väljer vilken typ av roll du vill hantera visas en liknande uppsättning med alternativ för den rolltypen.

![Skärmbild av privilegierad identitetshantering i Azure-portalen](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Vem kan göra vad?

För Azure AD-roller i Privilegierad identitetshantering kan endast en användare som är i rollen Privilegierad roll eller Global administratör hantera tilldelningar för andra administratörer. Du kan [bevilja åtkomst till andra administratörer för att hantera privilegierad identitetshantering](pim-how-to-give-access-to-pim.md). Globala administratörer, säkerhetsadministratörer, globala läsare och säkerhetsläsare kan också visa tilldelningar till Azure AD-roller i privilegierad identitetshantering.

För Azure-resursroller i Privilegierad identitetshantering kan endast en prenumerationsadministratör, en resursägare eller en administratör för användaråtkomst för resurser hantera tilldelningar för andra administratörer. Användare som är privilegierade rolladministratörer, säkerhetsadministratörer eller säkerhetsläsare har som standard inte åtkomst till att visa tilldelningar till Azure-resursroller i Privilegierad identitetshantering.

## <a name="scenarios"></a>Scenarier

Privileged Identity Management stöder följande scenarier:

### <a name="privileged-role-administrator-permissions"></a>Behörigheter för privilegierad rolladministratör

- aktivera godkännande för specifika roller
- Ange godkännare användare eller grupper för att godkänna begäranden
- visa historik för begäranden och godkännanden för alla privilegierade roller.

### <a name="approver-permissions"></a>Behörigheter för godkännare

- visa väntande godkännanden (begäranden)
- Godkänna eller avvisa begäranden om rollhöjning (enkel och mass)
- Motivera mitt godkännande eller avslag

### <a name="eligible-role-user-permissions"></a>Kvalificerade rollanvändarbehörigheter

- begära aktivering av roller som kräver godkännande
- visa status för begäran att aktivera
- slutföra uppgiften i Azure AD om aktiveringen godkändes.

## <a name="terminology"></a>Terminologi

Om du vill bättre förstå Privilegierad identitetshantering och dess dokumentation bör du granska följande villkor.

| Term eller begrepp | Kategori för rolltilldelning | Beskrivning |
| --- | --- | --- |
| berättigad | Typ | En rolltilldelning som kräver att en användare utför en eller flera åtgärder för att använda rollen. Om en användare har gjorts berättigad för en roll innebär det att användaren kan aktivera rollen när det finns behov av att utföra privilegierade åtgärder. Det finns ingen skillnad i den åtkomst som ges till någon med en permanent kontra berättigad rolltilldelning. Den enda skillnaden är att vissa användare inte behöver den åtkomsten hela tiden. |
| aktiv | Typ | En rolltilldelning som inte kräver att en användare utför några åtgärder för att använda rollen. Användare som är tilldelade som aktiva har de behörigheter som rollen har tilldelats. |
| aktivera |  | Processen med att utföra en eller flera åtgärder för att använda en roll som en användare är berättigad för. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare. |
| tilldelad | Status | En användare som har en aktiv rolltilldelning. |
| aktiverad | Status | En användare som har en berättigad rolltilldelning, utförde åtgärderna för att aktivera rollen och nu är aktiv.  När användaren är aktiverad kan han/hon använda rollen under en förinställd tidsperiod innan aktivering krävs igen. |
| permanent berättigad | Varaktighet | En rolltilldelning där en användare alltid är berättigad att aktivera rollen. |
| permanent aktiv | Varaktighet | En rolltilldelning där en användare alltid kan använda rollen utan att utföra några åtgärder. |
| berättigad med upphörande | Varaktighet | En rolltilldelning där en användare är berättigad att aktivera rollen inom angivna startdatum och slutdatum. |
| aktiv med upphörande | Varaktighet | En rolltilldelning där en användare kan använda rollen utan att utföra några åtgärder inom angivna startdatum och slutdatum. |
| just-in-time-åtkomst (JIT) |  | En modell i vilken användarna får tillfällig behörighet att utföra privilegierade uppgifter, vilket hindrar skadliga eller obehöriga användare från att få åtkomst när behörigheterna har gått ut. Åtkomst beviljas endast när användare behöver den. |
| princip om minsta behörighetsåtkomst |  | En rekommenderad säkerhetsrutin där varje användare endast har fått de minsta privilegier som krävs för att utföra uppgifterna som de har behörighet att utföra. Den här metoden minimerar antalet globala administratörer och använder i stället specifika administratörsroller för vissa scenarier. |

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Information om licenser för användare finns i [Licenskrav för att använda Privilegierad identitetshantering](subscription-requirements.md).

## <a name="next-steps"></a>Nästa steg

- [Licenskrav för att använda privilegierad identitetshantering](subscription-requirements.md)
- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Distribuera Privileged Identity Management](pim-deployment-plan.md)
