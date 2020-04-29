---
title: ta med fil
description: inkludera fil med krav
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "68426963"
---
> [!IMPORTANT]
> **Aktivera synkronisering av lösen ords-hash till Azure AD Domain Services innan du slutför uppgifterna i den här artikeln.**
>
> Följ anvisningarna nedan, beroende på typ av användare i Azure AD-katalogen. Slutför båda uppsättningarna med instruktioner om du har en blandning av enbart molnbaserade och synkroniserade användar konton i Azure AD-katalogen. Du kanske inte kan utföra följande åtgärder om du försöker använda ett B2B-gäst konto (till exempel ditt Gmail eller MSA från en annan identitetsprovider som vi tillåter) eftersom vi inte har lösen ordet för de här användarna synkroniserade med den hanterade domänen eftersom det är gäst konton i katalogen. All fullständig information om dessa konton, inklusive deras lösen ord, skulle ligga utanför Azure AD och eftersom den här informationen inte finns i Azure AD, så den kommer inte ens att synkroniseras med den hanterade domänen. 
> - [Instruktioner för endast molnbaserade användar konton](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instruktioner för användar konton som synkroniseras från en lokal katalog](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
