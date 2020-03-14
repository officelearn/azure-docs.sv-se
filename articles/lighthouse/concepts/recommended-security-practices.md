---
title: Rekommenderade säkerhetsmetoder
description: När du använder Azure-delegerad resurs hantering är det viktigt att tänka på säkerhets-och åtkomst kontroll.
ms.date: 07/11/2019
ms.topic: conceptual
ms.openlocfilehash: 8972d6548eccb1006d90bfcbb4dba8c01b05a981
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270711"
---
# <a name="recommended-security-practices"></a>Rekommenderade säkerhetsmetoder

När du använder Azure-delegerad resurs hantering är det viktigt att tänka på säkerhets-och åtkomst kontroll. Användare i din klient organisation har direkt åtkomst till kund prenumerationer och resurs grupper, så du bör vidta åtgärder för att underhålla din klients säkerhet. Du bör också se till att du bara tillåter den åtkomst som behövs för att hantera kundernas resurser på ett effektivt sätt. Det här avsnittet innehåller rekommendationer som hjälper dig att göra det.

## <a name="require-azure-multi-factor-authentication"></a>Kräv Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (även kallat tvåstegsverifiering) hjälper till att förhindra angripare från att få åtkomst till ett konto genom att kräva flera steg för autentisering. Du bör kräva Multi-Factor Authentication för alla användare i din tjänst leverantörs klient, inklusive användare som har åtkomst till kund resurser.

Vi rekommenderar att du ber dina kunder att implementera Azure-Multi-Factor Authentication även i sina klienter.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Tilldela behörigheter till grupper med principen om minsta behörighet

För att förenkla hanteringen rekommenderar vi att du använder Azure AD-användargrupper för varje roll som krävs för att hantera dina kunders resurser. På så sätt kan du lägga till eller ta bort enskilda användare i gruppen efter behov, i stället för att tilldela behörigheter direkt till användaren.

När du skapar din behörighets struktur måste du kontrol lera att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra sitt jobb, vilket bidrar till att minska risken för oavsiktliga fel.

Du kanske exempelvis vill använda en struktur som detta:

|Grupp namn  |Typ  |principalId  |Rolldefinition  |Roll Definitions-ID  |
|---------|---------|---------|---------|---------|
|Architects     |Användargrupp         |\<principalId\>         |Deltagare         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Utvärdering     |Användargrupp         |\<principalId\>         |Läsare         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM-specialister     |Användargrupp         |\<principalId\>         |VM-deltagare         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Tjänstens huvudnamn (SPN)         |\<principalId\>         |Deltagare         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

När du har skapat dessa grupper kan du tilldela användare efter behov. Lägg endast till de användare som verkligen behöver ha åtkomst. Se till att granska grupp medlemskapet regelbundet och ta bort alla användare som inte längre behövs eller som krävs för att inkludera.

Tänk på att när du registrerar [kunder via ett offentligt hanterat tjänst erbjudande](../how-to/publish-managed-services-offers.md), kommer alla grupper (eller användare eller tjänstens huvud namn) som du inkluderar att ha samma behörigheter för alla kunder som köper planen. Om du vill tilldela olika grupper för att arbeta med varje kund måste du publicera en separat privat plan som är exklusiv för varje kund eller publicera kunder individuellt genom att använda Azure Resource Manager mallar. Du kan till exempel publicera ett offentligt plan som har mycket begränsad åtkomst och sedan arbeta med kunden direkt för att publicera sina resurser för ytterligare åtkomst med hjälp av en anpassad Azure-resurs mal len som beviljar ytterligare åtkomst vid behov.


## <a name="next-steps"></a>Nästa steg

- [Distribuera Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Lär dig mer om [hanterings upplevelser mellan flera innehavare](cross-tenant-management-experience.md).
