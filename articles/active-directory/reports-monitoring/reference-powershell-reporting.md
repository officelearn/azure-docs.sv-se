---
title: Azure AD PowerShell-cmdletar för rapportering | Microsoft Docs
description: Referens för Azure AD PowerShell-cmdletar för rapportering.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27fa3d7be5238527f86e9dfde3be70ae09259d69
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302744"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Azure AD PowerShell-cmdletar för rapportering

Med Azure Active Directory (Azure AD)-rapporter kan du få den information du behöver för att avgöra hur din miljö fungerar. Du kan hämta rapport data med hjälp av Azure AD PowerShell-cmdlets för rapportering.

Den här artikeln ger en översikt över cmdleten.




## <a name="audit-logs"></a>Granskningsloggar

[Gransknings loggar](concept-audit-logs.md) ger spårning via loggar för alla ändringar som gjorts av olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.

Du får åtkomst till gransknings loggarna med hjälp av cmdleten Get-AzureADAuditDirectoryLogs.


| Scenario                      | PowerShell-kommando |
| :--                           | :--                |
| Programmets visnings namn      | Get-AzureADAuditDirectoryLogs-filter "initiatedBy/app/displayName EQ" Azure AD-molnbaserad synkronisering "" |
| Category                      | Get-AzureADAuditDirectoryLogs – filter "Category EQ" program hantering " |
| Datum/tid för aktivitet            | Get-AzureADAuditDirectoryLogs-filter "activityDateTime gt 2019-04-18" |
| Alla ovanstående              | Get-AzureADAuditDirectoryLogs-filter "initiatedBy/app/displayName EQ" Azure AD Cloud Sync "och Category EQ" Application Management "och activityDateTime gt 2019-04-18"|


Följande bild visar ett exempel på det här kommandot. 

![Knappen data Sammanfattning](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Inloggningsloggar

[Inloggnings](concept-sign-ins.md) loggarna ger information om användningen av hanterade program och användar inloggnings aktiviteter.

Du får åtkomst till inloggnings loggarna med hjälp av cmdleten Get-AzureADAuditSignInLogs.


| Scenario                      | PowerShell-kommando |
| :--                           | :--                |
| Användarens visnings namn             | Get-AzureADAuditSignInLogs-filter "userDisplayName EQ" Timothy Perkins "" |
| Skapa datum och tid              | Get-AzureADAuditSignInLogs-filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (allting sedan 5:30 PM på 4/18) |
| Status                        | Get-AzureADAuditSignInLogs-filter "status/errorCode EQ 50105" |
| Programmets visnings namn      | Get-AzureADAuditSignInLogs-filter "appDisplayName EQ" StoreFrontStudio [wsfed Enabled] " |
| Alla ovanstående              | Get-AzureADAuditSignInLogs-filter "userDisplayName EQ" Timothy Perkins "och status/errorCode Ne 0 och appDisplayName EQ StoreFrontStudio [wsfed Enabled]" |


Följande bild visar ett exempel på det här kommandot. 

![Knappen data Sammanfattning](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure AD-rapporter](overview-reports.md).
- [Rapporten gransknings loggar](concept-audit-logs.md). 
- [Programmerings åtkomst till Azure AD-rapporter](concept-reporting-api.md)
