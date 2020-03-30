---
title: Rekommenderade säkerhetsmetoder
description: När du använder Azure-delegerad resurshantering är det viktigt att tänka på säkerhets- och åtkomstkontroll.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246916"
---
# <a name="recommended-security-practices"></a>Rekommenderade säkerhetsmetoder

När du använder [Azure-delegerad resurshantering](azure-delegated-resource-management.md)är det viktigt att tänka på säkerhets- och åtkomstkontroll. Användare i din klient har direkt åtkomst till kundprenumerationer och resursgrupper, så du bör vidta åtgärder för att upprätthålla din klients säkerhet. Du vill också se till att du bara tillåter den åtkomst som behövs för att effektivt hantera kundernas resurser. Det här avsnittet innehåller rekommendationer som hjälper dig att göra det.

## <a name="require-azure-multi-factor-authentication"></a>Kräv Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (kallas även tvåstegsverifiering) hjälper till att förhindra att angripare får åtkomst till ett konto genom att kräva flera autentiseringssteg. Du bör kräva multifaktorautentisering för alla användare i din tjänsteleverantörsklient, inklusive alla användare som har åtkomst till kundresurser.

Vi föreslår att du ber dina kunder att implementera Azure Multi-Factor Autentisering i sina klienter också.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Tilldela behörigheter till grupper med hjälp av principen om lägsta behörighet

För att underlätta hanteringen rekommenderar vi att du använder Azure AD-användargrupper för varje roll som krävs för att hantera dina kunders resurser. På så sätt kan du lägga till eller ta bort enskilda användare i gruppen efter behov, i stället för att tilldela behörigheter direkt till den användaren.

> [!IMPORTANT]
> För att kunna lägga till behörigheter för en Azure AD-grupp måste **grupptypen** vara **Säkerhet** och inte **Office 365**. Det här alternativet väljs när gruppen skapas. Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

När du skapar din behörighetsstruktur måste du följa principen om lägsta behörighet så att användarna bara har de behörigheter som krävs för att slutföra sitt jobb, vilket bidrar till att minska risken för oavsiktliga fel.

Du kanske till exempel vill använda en struktur som denna:

|Gruppnamn  |Typ  |principalId (huvudsakligaId  |Rolldefinition  |ID för rolldefinition  |
|---------|---------|---------|---------|---------|
|Arkitekter     |Användargrupp         |\<principalId (huvudsakligaId\>         |Deltagare         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Utvärdering     |Användargrupp         |\<principalId (huvudsakligaId\>         |Läsare         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM-specialister     |Användargrupp         |\<principalId (huvudsakligaId\>         |Vm-deltagare         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Tjänstens huvudnamn (SPN)         |\<principalId (huvudsakligaId\>         |Deltagare         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

När du har skapat dessa grupper kan du tilldela användare efter behov. Lägg bara till de användare som verkligen behöver ha åtkomst. Var noga med att granska gruppmedlemskap regelbundet och ta bort alla användare som inte längre är lämpliga eller nödvändiga för att inkludera.

Tänk på att när du [är ombord på kunder via ett offentligt hanterat tjänsterbjudande](../how-to/publish-managed-services-offers.md)har alla grupper (eller användar- eller tjänsthuvudnamn) som du inkluderar samma behörigheter för varje kund som köper planen. Om du vill tilldela olika grupper att arbeta med varje kund måste du publicera en separat privat plan som är exklusiv för varje kund eller ombord på kunder individuellt med hjälp av Azure Resource Manager-mallar. Du kan till exempel publicera en offentlig plan som har mycket begränsad åtkomst och sedan arbeta med kunden direkt för att gå ombord på sina resurser för ytterligare åtkomst med hjälp av en anpassad Azure Resource Template som ger ytterligare åtkomst efter behov.


## <a name="next-steps"></a>Nästa steg

- [Distribuera Azure Multi Factor-autentisering](../../active-directory/authentication/howto-mfa-getstarted.md).
- Lär dig mer om [hanteringsupplevelser mellan klienter.](cross-tenant-management-experience.md)
