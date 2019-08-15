---
title: Aktivera MFA för alla Azure-administratörer
description: Vägledning för att aktivera global administratör
ms.service: security
ms.subservice: security-fundamentals
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: e30ce71a66dd5cb6c810111d359660d875ae97a8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927907"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Framtvinga Multi-Factor Authentication (MFA) för prenumerations administratörer

När du skapar dina administratörer, inklusive ditt globala administratörs konto, är det viktigt att du använder mycket starka autentiseringsmetoder.

Du kan utföra daglig administration genom att tilldela vissa administratörs roller, till exempel Exchange-administratör eller lösen ords administratör, till användar konton för IT-personalen vid behov.
Genom att aktivera [Azure Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) för dina administratörer lägger du dessutom till ett andra säkerhets lager för användar inloggningar och transaktioner. Azure MFA hjälper också till att minska sannolikheten för att en komprometterad autentiseringsuppgift kommer att ha åtkomst till organisationens data.

Exempel: Du tillämpar Azure MFA för dina användare och konfigurerar den för att använda ett telefonsamtal eller textmeddelande som verifiering. Om användarens autentiseringsuppgifter komprometteras kan angriparen inte komma åt någon resurs eftersom de inte kommer att ha åtkomst till användarens telefon. Organisationer som inte lägger till extra lager med identitets skydd är mer känsliga för stöld av autentiseringsuppgifter, vilket kan leda till att data komprometteras.

Ett alternativ för organisationer som vill behålla hela autentiseringen lokalt är att använda [Azure Multi-Factor Authentication Server](../../active-directory/authentication/howto-mfaserver-deploy.md), som också kallas "MFA lokalt". Med den här metoden kommer du fortfarande att kunna tillämpa Multi-Factor Authentication, samtidigt som MFA-servern hålls lokal.

Du kan kontrol lera vem i din organisation som har administratörs behörighet genom att använda följande Microsoft Azure AD v2 PowerShell-kommando:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Aktivera MFA

Granska hur [MFA](../../active-directory/authentication/howto-mfa-mfasettings.md) fungerar innan du fortsätter.

Så länge användarna har licenser som inkluderar Azure Multi-Factor Authentication behöver du inte göra något för att aktivera Azure MFA. Du kan börja med att kräva tvåstegsverifiering för enskilda användare. Följande licenser aktiverar Azure MFA:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Aktivera tvåstegsverifiering för användare

Använd en av de procedurer som anges i [så här kräver du](../../active-directory/authentication/howto-mfa-userstates.md) tvåstegsverifiering för en användare eller grupp för att börja använda Azure MFA. Du kan välja att genomdriva tvåstegsverifiering för alla inloggningar eller så kan du skapa principer för villkorlig åtkomst för att kräva tvåstegsverifiering endast när det är viktigt för dig.

