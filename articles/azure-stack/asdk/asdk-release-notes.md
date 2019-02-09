---
title: Microsoft Azure Stack Development Kit viktig information | Microsoft Docs
description: Förbättringar, korrigeringar och kända problem med Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 35a3850cdda7f6844258439b74b29dcc20852a89
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959603"
---
# <a name="asdk-release-notes"></a>Viktig information om ASDK

Den här artikeln innehåller information om ändringar, korrigeringar och kända problem i Azure Stack Development Kit (ASDK). Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](../azure-stack-updates.md#determine-the-current-version).

Håll dig uppdaterad med Nyheter i ASDK genom att prenumerera på den [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11901095"></a>Skapa 1.1901.0.95

### <a name="changes"></a>Ändringar

Den här versionen innehåller följande förbättringar för Azure Stack:

- BGP och NAT-komponenter har nu distribuerats på den fysiska värden. Detta eliminerar behovet av att ha två offentliga eller företagets IP-adresser för att distribuera ASDK och också enklare att distribuera.
- Integrerade Azure Stack-system säkerhetskopieringar kan nu [verifieras](asdk-validate-backup.md) med hjälp av den **asdk installer.ps1** PowerShell-skript.

### <a name="new-features"></a>Nya funktioner

- En lista över nya funktioner i den här versionen finns i [i det här avsnittet](../azure-stack-update-1901.md#new-features) viktig information för Azure Stack.

### <a name="fixed-and-known-issues"></a>Fast och kända problem

- En lista över problem som åtgärdas i den här versionen finns i [i det här avsnittet](../azure-stack-update-1901.md#fixed-issues) viktig information för Azure Stack. En lista över kända problem finns i [i det här avsnittet](../azure-stack-update-1901.md#known-issues-post-installation).
- Observera att [tillgängliga Azure Stack-snabbkorrigeringar](../azure-stack-update-1901.md#azure-stack-hotfixes) kan inte användas för Azure Stack ASDK.

## <a name="build-118110101"></a>Skapa 1.1811.0.101

### <a name="changes"></a>Ändringar

Den här versionen innehåller följande förbättringar för Azure Stack:  

- Det finns en uppsättning nya minsta och rekommenderade maskin- och programvarukrav för ASDK. Dessa nya rekommenderade specifikationer finns dokumenterade i [Azure Stack-distributioner saker att tänka på](asdk-deploy-considerations.md). Azure Stack-plattformen har utvecklats fler tjänster är nu tillgängliga och fler resurser som kan krävas. Specifikationer för ökad återspeglar rekommendationerna reviderade.

### <a name="new-features"></a>Nya funktioner

En lista över nya funktioner i den här versionen finns i [i det här avsnittet](../azure-stack-update-1811.md#new-features) viktig information för Azure Stack.

### <a name="fixed-and-known-issues"></a>Fast och kända problem

En lista över problem som åtgärdas i den här versionen finns i [i det här avsnittet](../azure-stack-update-1811.md#fixed-issues) viktig information för Azure Stack. En lista över kända problem finns i [i det här avsnittet](../azure-stack-update-1811.md#known-issues-post-installation).

## <a name="build-11809090"></a>Skapa 1.1809.0.90

### <a name="new-features"></a>Nya funktioner

En lista över nya funktioner i den här versionen finns i [i det här avsnittet](../azure-stack-update-1809.md#new-features) viktig information för Azure Stack.

### <a name="fixed-issues"></a>Åtgärdade problem

En lista över problem som åtgärdas i den här versionen finns i [i det här avsnittet](../azure-stack-update-1809.md#fixed-issues).

### <a name="known-issues"></a>Kända problem

En lista över kända problem i den här versionen finns i [i det här avsnittet](../azure-stack-update-1809.md#known-issues-post-installation).
