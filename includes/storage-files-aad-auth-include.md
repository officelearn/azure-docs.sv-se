---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565101"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) stöder identitetsbaserad autentisering via Server Message Block (SMB) via [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (förhandsversion) och [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). Den här artikeln fokuserar på hur Azure Files kan utnyttja domäntjänster, antingen lokalt eller i Azure, för att stödja identitetsbaserad åtkomst till Azure-filer via SMB. På så sätt kan du enkelt ersätta dina befintliga filservrar med Azure Files och fortsätta att använda din befintliga katalogtjänst, vilket upprätthåller sömlös användaråtkomst till resurser. 

Azure Files tillämpar auktorisering för användarens åtkomst till både resurs- och katalog-/filnivå. Behörighetstilldelning på delningsnivå kan tilldelas Azure AD-användare eller grupper som hanteras via den typiska [RBAC-modellen (Role-Based Access Control).](../articles/role-based-access-control/overview.md) Med RBAC ska autentiseringsuppgifterna som du använder för filåtkomst vara tillgängliga eller synkroniserade till Azure AD. Du kan tilldela inbyggda RBAC-roller som Storage File Data SMB Share Reader till användare eller grupper i Azure AD för att bevilja läsåtkomst till en Azure-filresurs.

På katalog-/filnivå stöder Azure Files bevarande, ärva och tillämpa [Windows-DACLs](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) precis som alla Windows-filservrar. Om du kopierar data via SMB från en filresurs till Azure-filer, eller vice versa, kan du välja att behålla Windows DACLs. Oavsett om du planerar att tvinga fram auktorisering eller inte kan du använda Azure Files för att säkerhetskopiera ACL:er tillsammans med dina data. 
