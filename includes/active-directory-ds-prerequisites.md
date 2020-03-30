---
title: ta med fil
description: inkludera fil med förutsättningar
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426963"
---
> [!IMPORTANT]
> **Aktivera synkronisering av lösenordsh hash-synkronisering till Azure AD Domain Services innan du slutför uppgifterna i den här artikeln.**
>
> Följ instruktionerna nedan, beroende på vilken typ av användare i din Azure AD-katalog. Slutför båda uppsättningarna instruktioner om du har en blandning av molnbaserade och synkroniserade användarkonton i din Azure AD-katalog. Du kanske inte kan utföra följande åtgärder om du försöker använda ett B2B-gästkonto (exempel , din Gmail eller MSA från en annan identitetsleverantör som vi tillåter) eftersom vi inte har lösenordet för dessa användare synkroniserade till hanterad domän eftersom dessa är gästkonton i katalogen. Den fullständiga informationen om dessa konton, inklusive deras lösenord, skulle ligga utanför Azure AD och eftersom den här informationen inte finns i Azure AD synkroniseras den inte ens till den hanterade domänen. 
> - [Instruktioner för molnbaserade användarkonton](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instruktioner för användarkonton synkroniserade från en lokal katalog](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
