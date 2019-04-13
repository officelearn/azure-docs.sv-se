---
title: ta med fil
description: Inkludera fil med krav
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
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551508"
---
> [!IMPORTANT]
> **Aktivera lösenordshashsynkronisering till Azure AD Domain Services innan du utför uppgifterna i den här artikeln.**
>
> Följ anvisningarna nedan, beroende på vilken typ av användare i Azure AD-katalogen. Slutför båda uppsättningarna instruktioner om du har en blandning av endast molnbaserade och synkroniserade användarkonton i Azure AD-katalogen. Du kanske inte kan utföra följande åtgärder om du vill använda ett B2B gästkonto (exempel ditt gmail eller MSA från en annan identitetsprovider som tillåter vi) eftersom vi inte har lösenordet för dessa användare som synkroniseras till hanterad domän som dessa är gästkonton i katalogen. Fullständig information om dessa konton, inklusive deras lösenord skulle vara utanför Azure AD och eftersom den här informationen inte är i Azure AD kan därför det även synkroniseras inte med den hanterade domänen. 
> - [Instruktioner för endast molnbaserade användarkonton](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Anvisningar för användarkonton som synkroniseras från en lokal katalog](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
