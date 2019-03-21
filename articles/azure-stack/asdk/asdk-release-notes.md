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
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 33f1ccf3f1c7bc657cc66efe7c5025356c954ad6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58187769"
---
# <a name="asdk-release-notes"></a>Viktig information om ASDK

Den här artikeln innehåller information om ändringar, korrigeringar och kända problem i Azure Stack Development Kit (ASDK). Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](../azure-stack-updates.md#determine-the-current-version).

Håll dig uppdaterad med Nyheter i ASDK genom att prenumerera på den [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11902069"></a>Skapa 1.1902.0.69

### <a name="new-features"></a>Nya funktioner

- 1902-build introducerar ett nytt användargränssnitt på Azure Stack-administratörsportalen för att skapa planer, erbjudanden, kvoter och tilläggsplaner. Mer information, inklusive skärmdumpar, se [skapa planer, erbjudanden och kvoter](../azure-stack-create-plan.md).

- En lista över andra ändringar och förbättringar i den här versionen finns i [i det här avsnittet](../azure-stack-update-1902.md#improvements) viktig information i Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Kända problem

- Ett problem har identifierats som ignoreras paket över 1450 byte till en intern belastningsutjämnaren (ILB). Problemet beror på inställning för MTU på värden är för lågt för VXLAN inkapslad paket som passerar rollen som från och med 1901 har flyttats till värden. Det finns minst två scenarier som kan uppstå som vi har sett det här problemet manifest själva:

  - SQL-frågor till SQL Always-On som är bakom en interna belastningsutjämnaren (ILB) och är över 660 byte.
  - Kubernetes-distributioner misslyckas om du försöker aktivera flera huvudservrar.  

  Problemet uppstår när du har kommunikation mellan en virtuell dator och en ILB i samma virtuella nätverk, men i olika undernät. Du kan lösa det här problemet genom att köra följande kommandon i en upphöjd kommandotolk på ASDK värden:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>Skapa 1.1901.0.95

Se den [viktiga build information i Azure Stack viktig](../azure-stack-update-1901.md#build-reference).

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
