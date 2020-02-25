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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565101"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) stöder identitets-baserad autentisering över Server Message Block (SMB) via [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (för hands version) och [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (ga). Den här artikeln fokuserar på hur Azure Files kan utnyttja domän tjänster, antingen lokalt eller i Azure, för att ge stöd åt identitetsbaserade åtkomst till Azure Files över SMB. På så sätt kan du enkelt ersätta dina befintliga fil servrar med Azure Files och fortsätta att använda din befintliga katalog tjänst för att upprätthålla sömlös användar åtkomst till resurser. 

Azure Files tvingar användaren att få åtkomst till både delnings-och katalog-/fil nivå. Behörighets tilldelning på resurs nivå kan tilldelas till Azure AD-användare eller grupper som hanteras via den vanliga modellen för [rollbaserad åtkomst kontroll (RBAC)](../articles/role-based-access-control/overview.md) . Med RBAC bör autentiseringsuppgifterna som du använder för fil åtkomst vara tillgängliga eller synkroniserade till Azure AD. Du kan tilldela inbyggda RBAC-roller som lagrings fil data SMB Share Reader till användare eller grupper i Azure AD för att bevilja Läs åtkomst till en Azure-filresurs.

På katalog-/filnivå Azure Files har stöd för att bevara, ärva och verkställa Windows- [DACL](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) -filer på samma sätt som alla Windows-filservrar. Om du kopierar data över SMB från en fil resurs till Azure Files eller vice versa, kan du välja att behålla Windows-DACL: er. Oavsett om du planerar att framtvinga auktorisering eller inte, kan du utnyttja Azure Files för att säkerhetskopiera ACL: er tillsammans med dina data. 
