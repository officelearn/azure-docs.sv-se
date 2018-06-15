---
title: Aktivera MFA för alla Azure-administratörer
description: Vägledning för att aktivera global administratör
ms.service: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: a7a08c54fe0c59e1e100e1c46e7a640da0692077
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181022"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Använda multifaktorautentisering (MFA) för prenumerationsadministratörer

Det är viktigt att du använder mycket starkt autentiseringsmetoder när du skapar dina administratörer, inklusive ditt globala administratörskonto.

Du kan utföra dagliga administrationen genom att tilldela specifika administratörsroller – till exempel Exchange-administratören eller lösenordsadministratör – till användarkontona för IT-personal efter behov.
Aktivera dessutom [Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) för dina administratörer lägger du till ett andra säkerhetslager till användarinloggningar och transaktioner. Azure MFA hjälper även IT minskar risken att avslöjade autentiseringsuppgifter kommer att ha åtkomst till organisationens data.

Exempel: du införa Azure MFA för användarna och konfigurera den att använda ett telefonsamtal eller SMS som verifiering. Om användarens autentiseringsuppgifter komprometteras kan angripare inte åtkomst till alla resurser eftersom han inte har åtkomst till användarens telefon. Organisationer som inte lägga till extra skyddslager identitet är mer känslig för autentiseringsuppgifter attack med lösenordsstöld, vilket kan leda till röjande av data.

Ett alternativ för organisationer som vill ha hela autentiseringen kontrollen lokal är att använda [Azure Multi-Factor Authentication-servern](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), även kallat ”MFA lokalt”. Med den här metoden kan du fortfarande att kunna använda multifaktorautentisering, samtidigt som den MFA-server lokalt.

Kontrollera vem i din organisation har administrativa behörigheter som du kan verifiera med hjälp av följande V2 för Microsoft Azure AD PowerShell-kommando:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Att aktivera MFA

Granska hur [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) fungerar innan du fortsätter.

Så länge användarna har licenser som inkluderar Azure Multi-Factor Authentication behöver du inte göra något för att aktivera Azure MFA. Du kan börja med att kräva tvåstegsverifiering för enskilda användare. Följande licenser aktiverar Azure MFA:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Aktivera tvåstegsverifiering för användare

Använd någon av de förfaranden som anges i [kräva tvåstegsverifiering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) för en användare eller grupp som du vill börja använda Azure MFA. Du kan välja att tillämpa tvåstegsverifiering för alla inloggningar eller du kan skapa principer för villkorlig åtkomst för att kräva tvåstegsverifiering endast när det är viktigt för dig.

