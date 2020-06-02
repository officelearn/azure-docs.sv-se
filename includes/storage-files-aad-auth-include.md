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
ms.openlocfilehash: d470160a9b54af8751371aa4ca58202646c8fab8
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263382"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) stöder identitets-baserad autentisering över Server Message Block (SMB) via [lokala Active Directory Domain Services (AD ds)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) och [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Den här artikeln fokuserar på hur Azure-filresurser kan använda domän tjänster, antingen lokalt eller i Azure, för att ge stöd för identitets-baserad åtkomst till Azure-filresurser över SMB. Genom att aktivera identitets åtkomst för dina Azure-filresurser kan du ersätta befintliga fil servrar med Azure-filresurser utan att ersätta den befintliga katalog tjänsten och upprätthålla sömlös användar åtkomst till resurser. 

Azure Files tvingar användaren att få åtkomst till både delnings-och katalog-/fil nivåerna. Behörighets tilldelning på resurs nivå kan utföras på Azure Active Directory (Azure AD)-användare eller grupper som hanteras via [RBAC-modellen (rollbaserad åtkomst kontroll](../articles/role-based-access-control/overview.md) ). Med RBAC bör autentiseringsuppgifterna som du använder för fil åtkomst vara tillgängliga eller synkroniserade till Azure AD. Du kan tilldela inbyggda RBAC-roller som lagrings fil data SMB Share Reader till användare eller grupper i Azure AD för att bevilja Läs åtkomst till en Azure-filresurs.

På katalog-/filnivå Azure Files har stöd för att bevara, ärva och verkställa Windows- [DACL](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) -filer på samma sätt som alla Windows-filservrar. Du kan välja att behålla Windows-DACL: er när du kopierar data över SMB mellan din befintliga fil resurs och dina Azure-filresurser. Oavsett om du planerar att framtvinga auktorisering eller inte, kan du använda Azure-filresurser för att säkerhetskopiera ACL: er tillsammans med dina data. 
