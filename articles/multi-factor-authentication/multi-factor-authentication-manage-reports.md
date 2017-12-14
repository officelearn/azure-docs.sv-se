---
title: "Åtkomst- och rapporter för Azure MFA | Microsoft Docs"
description: "Här beskrivs hur du använder funktionen Azure Multi-Factor Authentication - rapporter."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 32697eea410cb9afaa0e4347acd1a280fcf94289
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporter i Azure Multi-Factor Authentication

Azure Multi-Factor Authentication innehåller flera rapporter som kan användas av du och din organisation. De här rapporterna kan nås via Multi-Factor Authentication-hanteringsportalen. I följande tabell visas de tillgängliga rapporterna:

| Rapport | Beskrivning |
|:--- |:--- |
| Användning |Användningen rapporter visar information om övergripande användning, Användarsammanfattning och användarinformation. |
| Serverstatus |Den här rapporten visar statusen för multi-Factor Authentication-servrar som är kopplad till ditt konto. |
| Blockeringshistorik för användare |Rapporterna Visar historik över förfrågningar om att blockera eller avblockera användare. |
| Förbikopplingshistorik för användare |Visar historiken över förfrågningar om att koppla förbi Multi-Factor Authentication för en användares telefonnummer. |
| Bedrägerivarning |Visar en historik över bedrägerivarningar som skickats inom det angivna datumintervallet. |
| I kö |Visar rapporter i kö för bearbetning och deras status. En länk för att hämta eller visa rapporten tillhandahålls när rapporten är färdig. |

## <a name="view-reports"></a>Visa rapporter

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare** > **Multi-Factor Autentisering**.
3. Under **multifaktorautentisering**väljer **tjänstinställningar**. Längst ned under **hantera avancerade inställningar och visa rapporter**väljer **gå till portalen**.
4. Välj typ av rapport som du vill använda från i Azure Multi-Factor Authentication-hanteringsportalen på **visa en rapport** avsnitt i det vänstra navigeringsfönstret.

<center>![Moln](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-rapportering

Identifiera användare som har registrerats för MFA med hjälp av PowerShell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiera användare som inte har registrerats för MFA med hjälp av PowerShell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**Ytterligare resurser**

* [För användare](end-user/multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication på MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
