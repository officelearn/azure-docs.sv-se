---
title: Aktivera MFA för alla Azure-administratörer
description: Vägledning för att aktivera global administratör
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: d0479b834f814616e44a1888ab8b958990610820
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611707"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Använda multifaktorautentisering (MFA) för prenumerationens administratörer

När du skapar dina administratörer, inklusive ditt globala administratörskonto, är det viktigt att du använder mycket starkt autentiseringsmetoder.

Du kan utföra dagliga administrationen genom att tilldela specifika administratörsroller – till exempel Exchange-administratören eller lösenordsadministratör – till användarkontona för IT-personal efter behov.
Dessutom aktivera [Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) för administratörerna lägger du till ett andra säkerhetslager till användarinloggningar och transaktioner. Azure MFA hjälper även IT minska sannolikheten att en komprometterad autentiseringsuppgift har åtkomst till organisationsdata.

Till exempel: du använda Azure MFA för dina användare och konfigurera den för att använda ett telefonsamtal eller SMS som verifiering. Om användarens autentiseringsuppgifter skulle skadas kan angriparen inte åtkomst till alla resurser eftersom han inte har åtkomst till användarens telefon. Organisationer som inte lägger till extra skyddslager för identitet är svårare för autentiseringsuppgifter identitetsstöld, vilket kan leda till kompromettering av data.

Ett alternativ för organisationer som vill behålla de hela autentiseringen kontroll på plats är att använda [Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server), även kallat ”MFA lokalt”. Med den här metoden kommer du fortfarande att kunna använda multifaktorautentisering, samtidigt som de MFA server lokalt.

Kontrollera vem i din organisation har administratörsbehörighet som du kan kontrollera genom att använda följande Microsoft Azure AD V2 PowerShell-kommando:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Aktivera MFA

Granska hur [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) fungerar innan du fortsätter.

Så länge användarna har licenser som inkluderar Azure Multi-Factor Authentication behöver du inte göra något för att aktivera Azure MFA. Du kan börja med att kräva tvåstegsverifiering för enskilda användare. Följande licenser aktiverar Azure MFA:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Aktivera tvåstegsverifiering för användare

Använd någon av de procedurer som anges i [kräva tvåstegsverifiering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) för en användare eller grupp att börja använda Azure MFA. Du kan välja att tillämpa tvåstegsverifiering för alla inloggningar eller du kan skapa principer för villkorlig åtkomst för att kräva tvåstegsverifiering endast när det är viktigt för dig.

