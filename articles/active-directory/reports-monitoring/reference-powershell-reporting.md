---
title: Azure AD PowerShell-cmdlets för rapportering | Microsoft-dokument
description: Referens för Azure AD PowerShell-cmdlets för rapportering.
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
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495304"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Azure AD PowerShell-cmdletar för rapportering

> [!NOTE] 
> Dessa Powershell-cmdlets fungerar för närvarande endast med [Azure AD Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Module. Observera att förhandsgranskningsmodulen inte föreslås för produktionsanvändning. 

Om du vill installera den offentliga förhandsversionen använder du följande. 

```powershell
Install-module AzureADPreview
```
Mer infromation om hur du ansluter till Azure AD med powershell finns i artikeln [Azure AD Powershell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Med Azure Active Directory(Azure AD) rapporter kan du få information om aktiviteter kring alla skrivåtgärder i din riktning (granskningsloggar) och autentiseringsdata (inloggningsloggar). Även om informationen är tillgänglig med hjälp av MS Graph API, nu kan du hämta samma data med hjälp av Azure AD PowerShell cmdlets för rapportering.

I den här artikeln får du en översikt över de PowerShell-cmdletar som ska användas för granskningsloggar och inloggningsloggar.

## <a name="audit-logs"></a>Granskningsloggar

[Granskningsloggar](concept-audit-logs.md) ger spårbarhet via loggar för alla ändringar som görs av olika funktioner i Azure AD. Exempel på granskningsloggar är ändringar som gjorts i alla resurser i Azure AD, till exempel lägga till eller ta bort användare, appar, grupper, roller och principer.

Du får tillgång till granskningsloggarna med cmdleten Get-AzureAUDitDirectoryLogs.


| Scenario                      | PowerShell-kommando |
| :--                           | :--                |
| Namn på programvisning      | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync'" |
| Kategori                      | Get-AzureADAuditDirectoryLogs -Filter "kategori eq "Application Management"" |
| Tid för aktivitetsdatum            | Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2019-04-18" |
| Samtliga av ovanstående              | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq "Azure AD Cloud Sync" och kategori eq 'Application Management' och activityDateTime gt 2019-04-18"|


Följande bild visar ett exempel på det här kommandot. 

![Knappen "Datasammanfattning"](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Inloggningsloggar

Loggarna [för inloggningar](concept-sign-ins.md) innehåller information om användningen av hanterade program och användarloggningsaktiviteter.

Du får tillgång till inloggningsloggarna med cmdleten Get-AzureADAuditSignInLogs.


| Scenario                      | PowerShell-kommando |
| :--                           | :--                |
| Namn på användarvisning             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins'" |
| Skapa datumtid              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0Z" (Allt sedan 17:30 den 4/18) |
| Status                        | Get-AzureADAuditSignInLogs -Filter "status/errorCode eq 50105" |
| Namn på programvisning      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed aktiverat]'" |
| Samtliga av ovanstående              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' och status/errorCode ne 0 och appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


Följande bild visar ett exempel på det här kommandot. 

![Knappen "Datasammanfattning"](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure AD-rapporter](overview-reports.md).
- [Rapport över granskningsloggar](concept-audit-logs.md). 
- [Programmatisk åtkomst till Azure AD-rapporter](concept-reporting-api.md)
