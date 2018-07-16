---
title: Rapporter för åtkomst och användning för Azure MFA | Microsoft Docs
description: Här beskrivs hur du använder funktionen för Azure Multi-Factor Authentication - rapporter.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 146a86058adc73626e532f33e9fdbc83d9cf27e8
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049002"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporter i Azure Multi-Factor Authentication

Azure Multi-Factor Authentication innehåller flera rapporter som kan användas av dig och din organisation som är tillgängliga via Azure-portalen. I följande tabell visas de tillgängliga rapporterna:

| Rapportera | Plats | Beskrivning |
|:--- |:--- |:--- |
| Blockeringshistorik för användare | Azure AD > MFA Server > blockera/avblockera användare | Visar historiken över förfrågningar om att blockera eller avblockera användare. |
| Användning och bedrägeri aviseringar | Azure AD > inloggningar | Innehåller information om totala användning, Användarsammanfattning och information om användare; samt en historik över bedrägerivarningar som skickats under det angivna datumintervallet. |
| Användningen av lokala komponenter | Azure AD > MFA-servern > Aktivitetsrapport | Innehåller information om totala användning för MFA via NPS-tillägget, ADFS, och MFA-servern. |
| Förbikopplingshistorik för användare | Azure AD > MFA Server > engångsförbikoppling | Innehåller en historik över förfrågningar om förbikoppling Multi-Factor Authentication för en användare. |
| Serverstatus | Azure AD > MFA Server > serverstatus | Visar status för multi-Factor Authentication-servrar som är associerade med ditt konto. |

## <a name="view-reports"></a>Visa rapporter 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **MFA Server**.
3. Välj den rapport som du vill visa.

   <center>![Moln](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-rapportering

Identifiera användare som har registrerats för MFA med hjälp av PowerShell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiera användare som inte har registrerats för MFA med hjälp av PowerShell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Nästa steg

* [För användare](../user-help/multi-factor-authentication-end-user.md)
* [Var du vill distribuera](concept-mfa-whichversion.md)
