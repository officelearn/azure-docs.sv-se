---
title: Säkerhetsaviseringar för Azure AD-roller i PIM - Azure AD | Microsoft-dokument
description: Konfigurera säkerhetsaviseringar för Azure AD-roller Privilegierad identitetshantering i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86dbcdc24c90ba8b161b041af96cbdd0665ad827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253317"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Konfigurera säkerhetsaviseringar för Azure AD-roller i privilegierad identitetshantering

Pim (Privileged Identity Management) genererar aviseringar när det finns misstänkt eller osäker aktivitet i din Azure Active Directory -organisation (Azure Ad). När en avisering utlöses visas den på instrumentpanelen privilegierad identitetshantering. Markera aviseringen om du vill visa en rapport som visar de användare eller roller som utlöste aviseringen.

## <a name="determine-your-version-of-pim"></a>Bestäm din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azure-resursroller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API:et](azure-ad-roles-features.md#api-changes). Medan den nya versionen distribueras, vilka procedurer som du följer i den här artikeln beror på vilken version av privilegierad identitetshantering du för närvarande har. Följ stegen i det här avsnittet för att avgöra vilken version av privilegierad identitetshantering du har. När du känner till din version av Privilegierad identitetshantering kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är i [rollen Privilegierad rolladministratör.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Öppna **Azure AD-privilegierad identitetshantering**. Om du har en banderoll högst upp på översiktssidan följer du instruktionerna på fliken **Ny version** i den här artikeln. Annars följer du instruktionerna på fliken **Föregående version.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Följ stegen i den här artikeln för att undersöka säkerhetsaviseringar för Azure AD-roller.

# <a name="new-version"></a>[Ny version](#tab/new)

![Azure AD-roller – varningsfönster med aviseringar och allvarlighetsgrad](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Säkerhetsaviseringar

I det här avsnittet visas alla säkerhetsaviseringar för Azure AD-roller, tillsammans med hur du åtgärdar och förhindrar. Allvarlighetsgrad har följande innebörd:

- **Hög**: Kräver omedelbar åtgärd på grund av en policyöverträdelse.
- **Medium**: Kräver inte omedelbara åtgärder men signalerar en potentiell policyöverträdelse.
- **Låg**: Kräver inte omedelbara åtgärder men föreslår en bättre politisk förändring.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratörer använder inte sina privilegierade roller

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Användare som har tilldelats privilegierade roller som de inte behöver ökar risken för en attack. Det är också lättare för angripare att förbli obemärkt i konton som inte aktivt används. |
| **Hur fixar man?** | Granska användarna i listan och ta bort dem från privilegierade roller som de inte behöver. |
| **Prevention (Skydd)** | Tilldela privilegierade roller endast till användare som har en affärsmotivering. </br>Schemalägg regelbundna [åtkomstgranskningar](pim-how-to-start-security-review.md) för att kontrollera att användarna fortfarande behöver åtkomst. |
| **Åtgärder för begränsning av portaler** | Tar bort kontot från sin privilegierade roll. |
| **Utlösare** | Utlöses om en användare går över ett visst antal dagar utan att aktivera en roll. |
| **Antal dagar** | Den här inställningen anger det maximala antalet dagar, från 0 till 100, som en användare kan gå utan att aktivera en roll.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Roller kräver inte multifaktorautentisering för aktivering

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Utan multifaktorautentisering kan komprometterade användare aktivera privilegierade roller. |
| **Hur fixar man?** | Granska listan över roller och [kräver multifaktorautentisering](pim-how-to-change-default-settings.md) för varje roll. |
| **Prevention (Skydd)** | [Kräv MFA](pim-how-to-change-default-settings.md) för varje roll.  |
| **Åtgärder för begränsning av portaler** | Gör multifaktorautentisering krävs för aktivering av den privilegierade rollen. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organisationen har inte Azure AD Premium P2

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Den aktuella Azure AD-organisationen har inte Azure AD Premium P2. |
| **Hur fixar man?** | Granska information om [Azure AD-versioner](../fundamentals/active-directory-whatis.md). Uppgradera till Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potentiella inaktuella konton i en privilegierad roll

| | |
| --- | --- |
| **Allvarlighetsgrad** | Medel |
| **Varför får jag den här varningen?** | Konton i en privilegierad roll har inte ändrat sitt lösenord under de senaste 90 dagarna. Dessa konton kan vara tjänstkonton eller delade konton som inte underhålls och som är sårbara för angripare. |
| **Hur fixar man?** | Granska kontona i listan. Om de inte längre behöver åtkomst tar du bort dem från deras privilegierade roller. |
| **Prevention (Skydd)** | Kontrollera att konton som delas roterar starka lösenord när det sker en ändring av de användare som känner till lösenordet. </br>Granska regelbundet konton med privilegierade roller med hjälp av [åtkomstgranskningar](pim-how-to-start-security-review.md) och ta bort rolltilldelningar som inte längre behövs. |
| **Åtgärder för begränsning av portaler** | Tar bort kontot från sin privilegierade roll. |
| **Metodtips** | Delade konton, tjänst- och nödåtkomstkonton som autentiserar med ett lösenord och tilldelas administrativa roller med hög behörighet, till exempel Global administratör eller Säkerhetsadministratör bör ha sina lösenord roterade i följande fall:<ul><li>Efter en säkerhetsincident som innebär missbruk eller kompromettering av rättigheter för administrativ åtkomst</li><li>När en användares behörighet har ändrats så att de inte längre är administratör (till exempel efter att en anställd som var administratör lämnar IT eller lämnar organisationen)</li><li>Med jämna mellanrum (t.ex. kvartalsvis eller år) även om det inte förekom någon känd överträdelse eller ändring av IT-bemanning</li></ul>Eftersom flera personer har åtkomst till dessa kontons autentiseringsuppgifter bör autentiseringsuppgifterna roteras för att säkerställa att personer som har lämnat sina roller inte längre kan komma åt kontona. [Läs mer om att skydda konton](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Roller tilldelas utanför Privilegierad identitetshantering

| | |
| --- | --- |
| **Allvarlighetsgrad** | Hög |
| **Varför får jag den här varningen?** | Privilegierade rolltilldelningar som görs utanför Privilegierad identitetshantering övervakas inte korrekt och kan tyda på en aktiv attack. |
| **Hur fixar man?** | Granska användarna i listan och ta bort dem från privilegierade roller som tilldelats utanför Privilegierad identitetshantering. |
| **Prevention (Skydd)** | Undersök var användare tilldelas privilegierade roller utanför Privilegierad identitetshantering och förhindra framtida tilldelningar därifrån. |
| **Åtgärder för begränsning av portaler** | Tar bort användaren från deras privilegierade roll. |

### <a name="there-are-too-many-global-administrators"></a>Det finns för många globala administratörer

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Global administratör är den högsta privilegierade rollen. Om en global administratör äventyras får angriparen åtkomst till alla sina behörigheter, vilket äventyrar hela systemet. |
| **Hur fixar man?** | Granska användarna i listan och ta bort alla som inte absolut behöver rollen Global administratör. </br>Tilldela dessa användare lägre privilegierade roller i stället. |
| **Prevention (Skydd)** | Tilldela användarna den minst privilegierade roll de behöver. |
| **Åtgärder för begränsning av portaler** | Tar bort kontot från sin privilegierade roll. |
| **Utlösare** | Utlöses om två olika villkor uppfylls och du kan konfigurera båda. Först måste du nå ett visst tröskelvärde för globala administratörer. För det andra måste en viss procentandel av dina totala rolltilldelningar vara globala administratörer. Om du bara uppfyller någon av dessa mätningar visas inte aviseringen. |
| **Minsta antal globala administratörer** | Den här inställningen anger antalet globala administratörer, från 2 till 100, som du anser vara för få för din Azure AD-organisation. |
| **Procentandel globala administratörer** | Den här inställningen anger den minsta procentandelen administratörer som är globala administratörer, från 0 % till 100 %, under vilket du inte vill att din Azure AD-organisation ska doppas. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollerna aktiveras för ofta

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Flera aktiveringar till samma privilegierade roll av samma användare är ett tecken på en attack. |
| **Hur fixar man?** | Granska användarna i listan och se till att [aktiveringstiden](pim-how-to-change-default-settings.md) för deras privilegierade roll är tillräckligt lång för att de ska kunna utföra sina uppgifter. |
| **Prevention (Skydd)** | Kontrollera att [aktiveringstiden](pim-how-to-change-default-settings.md) för privilegierade roller är tillräckligt lång för att användarna ska kunna utföra sina uppgifter.</br>[Kräv multifaktorautentisering](pim-how-to-change-default-settings.md) för privilegierade roller som har konton som delas av flera administratörer. |
| **Åtgärder för begränsning av portaler** | Ej tillämpligt |
| **Utlösare** | Utlöses om en användare aktiverar samma privilegierade roll flera gånger inom en angiven period. Du kan konfigurera både tidsperioden och antalet aktiveringar. |
| **Tidsram för förnyelse av aktivering** | Den här inställningen anger i dagar, timmar, minuter och sekund den tidsperiod som du vill använda för att spåra misstänkta förnyelser. |
| **Antal förnyelser av aktiveringar** | Den här inställningen anger antalet aktiveringar, från 2 till 100, då du vill bli meddelad, inom den tidsram du valde. Du kan ändra den här inställningen genom att flytta skjutreglaget eller skriva ett nummer i textrutan. |

## <a name="configure-security-alert-settings"></a>Konfigurera inställningar för säkerhetsvarningar

Gå till **Inställningar**på sidan Aviseringar .

![Sidan Aviseringar med inställningar markerade](media/pim-how-to-configure-security-alerts/alert-settings.png)

Anpassa inställningarna på de olika aviseringarna så att de fungerar med din miljö och dina säkerhetsmål.

![Ange sida för en avisering för att aktivera och konfigurera inställningar](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

![Azure AD-roller – varningsfönster med aviseringar och allvarlighetsgrad](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Säkerhetsaviseringar

I det här avsnittet visas alla säkerhetsaviseringar för Azure AD-roller, tillsammans med hur du åtgärdar och förhindrar. Allvarlighetsgrad har följande innebörd:

- **Hög**: Kräver omedelbar åtgärd på grund av en policyöverträdelse.
- **Medium**: Kräver inte omedelbara åtgärder men signalerar en potentiell policyöverträdelse.
- **Låg**: Kräver inte omedelbara åtgärder men föreslår en bättre politisk förändring.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratörer använder inte sina privilegierade roller

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Användare som har tilldelats privilegierade roller som de inte behöver ökar risken för en attack. Det är också lättare för angripare att förbli obemärkt i konton som inte aktivt används. |
| **Hur fixar man?** | Granska användarna i listan och ta bort dem från privilegierade roller som de inte behöver. |
| **Prevention (Skydd)** | Tilldela privilegierade roller endast till användare som har en affärsmotivering. </br>Schemalägg regelbundna [åtkomstgranskningar](pim-how-to-start-security-review.md) för att kontrollera att användarna fortfarande behöver åtkomst. |
| **Åtgärder för begränsning av portaler** | Tar bort kontot från sin privilegierade roll. |
| **Utlösare** | Utlöses om en användare går över ett visst antal dagar utan att aktivera en roll. |
| **Antal dagar** | Den här inställningen anger det maximala antalet dagar, från 0 till 100, som en användare kan gå utan att aktivera en roll.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Roller kräver inte multifaktorautentisering för aktivering

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Utan multifaktorautentisering kan komprometterade användare aktivera privilegierade roller. |
| **Hur fixar man?** | Granska listan över roller och [kräver multifaktorautentisering](pim-how-to-change-default-settings.md) för varje roll. |
| **Prevention (Skydd)** | [Kräv MFA](pim-how-to-change-default-settings.md) för varje roll.  |
| **Åtgärder för begränsning av portaler** | Gör multifaktorautentisering krävs för aktivering av den privilegierade rollen. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organisationen har inte Azure AD Premium P2

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Den aktuella Azure AD-organisationen har inte Azure AD Premium P2. |
| **Hur fixar man?** | Granska information om [Azure AD-versioner](../fundamentals/active-directory-whatis.md). Uppgradera till Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potentiella inaktuella konton i en privilegierad roll

| | |
| --- | --- |
| **Allvarlighetsgrad** | Medel |
| **Varför får jag den här varningen?** | Konton i en privilegierad roll har inte ändrat sitt lösenord under de senaste 90 dagarna. Dessa konton kan vara tjänstkonton eller delade konton som inte underhålls och som är sårbara för angripare. |
| **Hur fixar man?** | Granska kontona i listan. Om de inte längre behöver åtkomst tar du bort dem från deras privilegierade roller. |
| **Prevention (Skydd)** | Kontrollera att konton som delas roterar starka lösenord när det sker en ändring av de användare som känner till lösenordet. </br>Granska regelbundet konton med privilegierade roller med hjälp av [åtkomstgranskningar](pim-how-to-start-security-review.md) och ta bort rolltilldelningar som inte längre behövs. |
| **Åtgärder för begränsning av portaler** | Tar bort kontot från sin privilegierade roll. |
| **Metodtips** | Delade konton, tjänst- och nödåtkomstkonton som autentiserar med ett lösenord och tilldelas administrativa roller med hög behörighet, till exempel Global administratör eller Säkerhetsadministratör bör ha sina lösenord roterade i följande fall:<ul><li>Efter en säkerhetsincident som innebär missbruk eller kompromettering av rättigheter för administrativ åtkomst</li><li>När en användares behörighet har ändrats så att de inte längre är administratör (till exempel efter att en anställd som var administratör lämnar IT eller lämnar organisationen)</li><li>Med jämna mellanrum (t.ex. kvartalsvis eller år) även om det inte förekom någon känd överträdelse eller ändring av IT-bemanning</li></ul>Eftersom flera personer har åtkomst till dessa kontons autentiseringsuppgifter bör autentiseringsuppgifterna roteras för att säkerställa att personer som har lämnat sina roller inte längre kan komma åt kontona. [Läs mer](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Roller tilldelas utanför Privilegierad identitetshantering

| | |
| --- | --- |
| **Allvarlighetsgrad** | Hög |
| **Varför får jag den här varningen?** | Privilegierade rolltilldelningar som görs utanför Privilegierad identitetshantering övervakas inte korrekt och kan tyda på en aktiv attack. |
| **Hur fixar man?** | Granska användarna i listan och ta bort dem från privilegierade roller som tilldelats utanför Privilegierad identitetshantering. |
| **Prevention (Skydd)** | Undersök var användare tilldelas privilegierade roller utanför Privilegierad identitetshantering och förhindra framtida tilldelningar därifrån. |
| **Åtgärder för begränsning av portaler** | Tar bort användaren från deras privilegierade roll. |

### <a name="there-are-too-many-global-administrators"></a>Det finns för många globala administratörer

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Global administratör är den högsta privilegierade rollen. Om en global administratör äventyras får angriparen åtkomst till alla sina behörigheter, vilket äventyrar hela systemet. |
| **Hur fixar man?** | Granska användarna i listan och ta bort alla som inte absolut behöver rollen Global administratör. </br>Tilldela dessa användare lägre privilegierade roller i stället. |
| **Prevention (Skydd)** | Tilldela användarna den minst privilegierade roll de behöver. |
| **Åtgärder för begränsning av portaler** | Tar bort kontot från sin privilegierade roll. |
| **Utlösare** | Utlöses om två olika villkor uppfylls och du kan konfigurera båda. Först måste du nå ett visst tröskelvärde för globala administratörer. För det andra måste en viss procentandel av dina totala rolltilldelningar vara globala administratörer. Om du bara uppfyller någon av dessa mätningar visas inte aviseringen. |
| **Minsta antal globala administratörer** | Den här inställningen anger antalet globala administratörer, från 2 till 100, som du anser vara för få för din Azure AD-organisation. |
| **Procentandel globala administratörer** | Den här inställningen anger den minsta procentandelen administratörer som är globala administratörer, från 0 % till 100 %, under vilket du inte vill att din Azure AD-organisation ska doppas. |

### <a name="roles-are-being-activated-too-frequently"></a>Rollerna aktiveras för ofta

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här varningen?** | Flera aktiveringar till samma privilegierade roll av samma användare är ett tecken på en attack. |
| **Hur fixar man?** | Granska användarna i listan och se till att [aktiveringstiden](pim-how-to-change-default-settings.md) för deras privilegierade roll är tillräckligt lång för att de ska kunna utföra sina uppgifter. |
| **Prevention (Skydd)** | Kontrollera att [aktiveringstiden](pim-how-to-change-default-settings.md) för privilegierade roller är tillräckligt lång för att användarna ska kunna utföra sina uppgifter.</br>[Kräv multifaktorautentisering](pim-how-to-change-default-settings.md) för privilegierade roller som har konton som delas av flera administratörer. |
| **Åtgärder för begränsning av portaler** | Ej tillämpligt |
| **Utlösare** | Utlöses om en användare aktiverar samma privilegierade roll flera gånger inom en angiven period. Du kan konfigurera både tidsperioden och antalet aktiveringar. |
| **Tidsram för förnyelse av aktivering** | Den här inställningen anger i dagar, timmar, minuter och sekund den tidsperiod som du vill använda för att spåra misstänkta förnyelser. |
| **Antal förnyelser av aktiveringar** | Den här inställningen anger antalet aktiveringar, från 2 till 100, då du vill bli meddelad, inom den tidsram du valde. Du kan ändra den här inställningen genom att flytta skjutreglaget eller skriva ett nummer i textrutan. |

## <a name="configure-security-alert-settings"></a>Konfigurera inställningar för säkerhetsvarningar

Du kan anpassa några av säkerhetsaviseringarna i Privilegierad identitetshantering så att de fungerar med organisationens behov och säkerhetsmål. Så här öppnar du säkerhetsvarningsinställningarna:

1. Öppna **privilegierad identitetshantering** i Azure AD.

1. Välj **Azure AD-roller**.

1. Välj **Inställningar** och sedan **Aviseringar**.

    ![Azure AD-roller – Inställningar med valda aviseringar](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Välj ett aviseringsnamn för att konfigurera inställningen för den aviseringen.

    ![För den valda aviseringen visas fönstret säkerhetsvarningsinställningar](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure AD-rollinställningar i privilegierad identitetshantering](pim-how-to-change-default-settings.md)
