---
title: "Åtkomst- och rapporter för Azure MFA | Microsoft Docs"
description: "Här beskrivs hur du använder funktionen Azure Multi-Factor Authentication - rapporter."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
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
ms.openlocfilehash: a0ac1711b6bfb8f461cd775ed1f3409925643615
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
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

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj Active Directory till vänster.
3. Gör något av följande två alternativ, beroende på om du använder Autentiseringsleverantörer:
   * **Alternativ 1**: Klicka på fliken Flerfunktionsautentiseringsleverantörer. Välj din MFA-leverantör och klicka på den **hantera** längst ned.
   * **Alternativ 2**: Välj din katalog och gå till den **konfigurera** fliken. Välj **Hantera tjänstinställningar** i avsnittet för multifaktorautentisering. Klicka på Gå till portal-länken längst ned på sidan för MFA inställningar.
4. Välj typ av rapport som du vill använda från i Azure Multi-Factor Authentication-hanteringsportalen på **visa en rapport** avsnitt i det vänstra navigeringsfönstret.

<center>![Moln](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-rapportering

Identifiera användare som har registrerats för MFA med hjälp av Powershell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiera användare som inte har registrerats för MFA med hjälp av Powershell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**Ytterligare resurser**

* [För användare](end-user/multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication på MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
