---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 64751e0fcbf9a2255964d0de673e2cc2020ceb9a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254726"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) stöder identity-baserad autentisering via SMB (Server Message Block) (förhandsversion) via [domäntjänster för Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/active-directory-ds-overview.md). Dina domänanslutna Windows-datorer (VM) kan komma åt Azure-filresurser med [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) autentiseringsuppgifter. 

Azure AD autentiserar en identitet som en användare, grupp eller tjänstens huvudnamn med [rollbaserad åtkomstkontroll (RBAC)](../articles/role-based-access-control/overview.md). Du kan definiera anpassade RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för att komma åt Azure Files. När du tilldelar din anpassade RBAC-roll till en Azure AD-identitet identitet beviljas åtkomst till en Azure-filresurs enligt dessa behörigheter.

Som en del av förhandsversionen, även Azure Files stöder bevarar, ärver och tillämpa [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) för alla filer och kataloger i en filresurs. Om du kopierar data från en filresurs till Azure Files eller tvärtom, du kan ange att bibehålls NTFS DACL: er. På så sätt kan du implementera säkerhetskopieringsscenarier med hjälp av Azure Files, bevara din NTFS DACLS mellan din lokala filresurs och din molnbaserade filresurs. 

> [!NOTE]
> - Azure AD-autentisering över SMB stöds inte för virtuella Linux-datorer i förhandsversionen. Endast Windows Server-datorer stöds.
> - Azure AD-autentisering över SMB stöds inte för lokala datorer som har åtkomst till Azure Files.
> - Azure AD-autentisering är endast tillgänglig för lagringskonton som skapats efter den 24 September 2018.
> - Azure AD-autentisering över SMB- och NTFS-ACL beständiga stöds inte på Azure-filresurser som hanteras av Azure File Sync Service. 
