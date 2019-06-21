---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269348"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) stöder identity-baserad autentisering via SMB (Server Message Block) (förhandsversion) via [domäntjänster för Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/overview.md). Dina domänanslutna Windows-datorer (VM) kan komma åt Azure-filresurser med [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) autentiseringsuppgifter. 

Du kan hantera Azure Files dela åtkomst till en identitet, till exempel en användare, grupp i Azure AD med [rollbaserad åtkomstkontroll (RBAC)](../articles/role-based-access-control/overview.md). Du kan definiera anpassade RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för att komma åt Azure Files. När du tilldelar din anpassade RBAC-roll till en Azure AD-identitet identitet beviljas åtkomst till en Azure-filresurs enligt dessa behörigheter.

Som en del av förhandsversionen, även Azure Files stöder bevarar, ärver och tillämpa [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) för alla filer och kataloger i en filresurs. Om du kopierar data från en filresurs till Azure Files eller tvärtom, du kan ange att bibehålls NTFS DACL: er. På så sätt kan du implementera säkerhetskopieringsscenarier med hjälp av Azure Files, bevara din NTFS DACLS mellan din lokala filresurs och din molnbaserade filresurs. 

> [!NOTE]
> - Azure AD Domain Service-autentisering för SMB-åtkomst stöds inte för virtuella Linux-datorer. Endast virtuella Windows-datorer stöds.
> - Azure AD Domain Service-autentisering för SMB-åtkomst stöds inte för Active Directory domänansluten dator. Under tiden kan du överväga för att använda [Azure Files Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) att börja migrera dina data till Azure Files och fortsätta att tillämpa åtkomstkontroll med hjälp av AD credentails från din lokala AD domänanslutna datorer. 
> - Azure AD Domain Service-autentisering för SMB-åtkomst är endast tillgänglig för lagringskonton som skapats efter den 24 September 2018.
> - Azure AD Domain-tjänst-autentisering för SMB-åtkomst och NTFS DACL beständiga stöds inte på Azure-filresurser som hanteras av Azure File Sync Service. 
