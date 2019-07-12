---
title: Rekommenderade säkerhetsmetoder för Azure fyr
description: När du använder Azure delegerat resurshantering, är det viktigt att tänka på säkerheten och åtkomstkontroll.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 843b965e6ea74a7c11dc11459ff5d30ddbe5c987
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809871"
---
# <a name="recommended-security-practices"></a>Rekommenderade säkerhetsmetoder

När du använder Azure delegerat resurshantering, är det viktigt att tänka på säkerheten och åtkomstkontroll. Användare i din klient får direkt åtkomst till kundprenumerationer och resursgrupper, så bör du vidta åtgärder för att upprätthålla säkerheten för din klient. Du också vill kontrollera att du bara tillåter åtkomst som behövs för att effektivt hantera resurser för dina kunder. Det här avsnittet innehåller rekommendationer när du gör.

## <a name="require-azure-multi-factor-authentication"></a>Require Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (även kallat tvåstegsverifiering) hjälper till att förhindra angripare från att få åtkomst till ett konto genom att kräva flera steg för autentisering. Du bör kräva Multifaktorautentisering för alla användare i din service provider-klient, inklusive alla användare som har åtkomst till kundresurser.

Vi rekommenderar att du ber dina kunder att implementera Azure Multi-Factor Authentication i sina klienter samt.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Tilldela behörigheter till grupper, med hjälp av principen om lägsta behörighet

För att underlätta hantering, bör du använda Azure AD-användargrupper för varje roll som krävs för att hantera dina kunders resurser. På så sätt kan du lägga till eller ta bort enskilda användare i gruppen efter behov i stället för att tilldela behörigheter direkt till den användaren.

När du skapar din behörighetsstrukturen, måste du följa principen om lägsta behörighet så att användarna bara har de behörigheter som krävs för att slutföra arbetet, vilket minskar risken för oavsiktliga fel.

Du kan till exempel vill använda en struktur som denna:

|Gruppnamn  |type  |principalId  |Rolldefinition  |Rolldefinitions-ID  |
|---------|---------|---------|---------|---------|
|Arkitekter     |Användargrupp         |\<principalId\>         |Deltagare         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Utvärdering     |Användargrupp         |\<principalId\>         |Läsare         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM-specialister     |Användargrupp         |\<principalId\>         |VM-deltagare         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Tjänstens huvudnamn (SPN)         |\<principalId\>         |Deltagare         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

När du har skapat dessa grupper kan tilldela du användare efter behov. Bara lägga till de användare som verkligen behöver ha åtkomst. Glöm inte att granska gruppmedlemskap regelbundet och ta bort alla användare som inte längre lämpliga eller ett nödvändigt att inkludera.

Tänk på att om du [kunderna via en offentlig hanterade tjänsterbjudande](../how-to/publish-managed-services-offers.md), valfri grupp (eller användaren eller tjänstens huvudnamn) som du lägger till har samma behörigheter för alla kunder som köper planen. Om du vill tilldela olika grupper att arbeta med varje kund, måste du publicera en separat privat plan som är exklusivt för varje kund eller kunderna individuellt med hjälp av Azure Resource Manager-mallar. Du kan till exempel publicerar en offentlig plan som har mycket begränsad åtkomst och sedan arbeta med kunden direkt att publicera sina resurser för ytterligare åtkomst med hjälp av en anpassad mall Azure Resource bevilja ytterligare åtkomst efter behov.


## <a name="next-steps"></a>Nästa steg

- [Distribuera Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Lär dig mer om [mellan klientorganisationer hanteringsupplevelser](cross-tenant-management-experience.md).
