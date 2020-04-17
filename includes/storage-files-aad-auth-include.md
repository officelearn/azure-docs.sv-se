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
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536597"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) stöder identitetsbaserad autentisering via Server Message Block (SMB) via [lokala AD DS (Active Directory Domain Services)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (förhandsversion) och Azure Active Directory Domain Services [(Azure AD DS).](../articles/active-directory-domain-services/overview.md) Den här artikeln fokuserar på hur Azure-filresurser kan använda domäntjänster, antingen lokalt eller i Azure, för att stödja identitetsbaserad åtkomst till Azure-filresurser via SMB. Genom att aktivera identitetsbaserad åtkomst för dina Azure-filresurser kan du ersätta befintliga filservrar med Azure-filresurser utan att ersätta din befintliga katalogtjänst, vilket upprätthåller sömlös användaråtkomst till resurser. 

Azure Files tillämpar auktorisering för användaråtkomst till både resurs- och katalog-/filnivåer. Behörighetstilldelning på delningsnivå kan utföras på Azure Active Directory (Azure AD) användare eller grupper som hanteras via [rbac-modellen (Role-based Access Control).](../articles/role-based-access-control/overview.md) Med RBAC ska autentiseringsuppgifterna som du använder för filåtkomst vara tillgängliga eller synkroniserade till Azure AD. Du kan tilldela inbyggda RBAC-roller som Storage File Data SMB Share Reader till användare eller grupper i Azure AD för att bevilja läsåtkomst till en Azure-filresurs.

På katalog-/filnivå stöder Azure Files bevarande, ärva och tillämpa [Windows-DACLs](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) precis som alla Windows-filservrar. Du kan välja att behålla Windows DACLs när du kopierar data via SMB mellan din befintliga filresurs och dina Azure-filresurser. Oavsett om du planerar att tvinga auktorisering eller inte kan du använda Azure-filresurser för att säkerhetskopiera ACL:er tillsammans med dina data. 
