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
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854549"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) stöder identitets-baserad autentisering över Server Message Block (SMB) via [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Dina domänanslutna virtuella Windows-datorer (VM) kan komma åt Azure-filresurser med hjälp av [Azure Active Directory (autentiseringsuppgifter för Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

Du kan hantera Azure Files resurs nivå åtkomst till en identitet, till exempel en användare eller grupp i Azure AD med hjälp av [rollbaserad åtkomst kontroll (RBAC)](../articles/role-based-access-control/overview.md). Du kan definiera anpassade RBAC-roller som innehåller vanliga uppsättningar behörigheter som används för att komma åt Azure Files. När du tilldelar en anpassad RBAC-roll till en Azure AD-identitet beviljas den identiteten åtkomst till en Azure-filresurs enligt dessa behörigheter.

Azure Files också stöd för att bevara, ärva och verkställa NTFS- [DACL: er](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) på alla filer och kataloger i en fil resurs. Om du kopierar data från en fil resurs till Azure Files eller vice versa kan du ange att NTFS-DACL: er ska bevaras. På det här sättet kan du implementera säkerhets kopierings scenarier med hjälp av Azure Files och bevara dina NTFS-DACL: er mellan din lokala fil resurs och din moln fil resurs. 

> [!NOTE]
> - Azure AD DS-autentisering för SMB-åtkomst (Server Message Block) stöds inte för virtuella Linux-datorer. Endast virtuella Windows-datorer stöds.
> - Azure AD DS-autentisering för SMB-åtkomst stöds inte för Active Directory domänanslutna datorer. Under tiden kan du överväga att använda [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) för att börja migrera dina data till Azure Files och fortsätta att framtvinga åtkomst kontroll genom att använda Active Directory autentiseringsuppgifter från dina lokala Active Directory domänanslutna datorer. 
> - Azure AD DS-autentisering för SMB-åtkomst är endast tillgängligt för lagrings konton som skapats efter den 24 september 2018.
> - Azure AD DS-autentisering för SMB-åtkomst och NTFS-persistence stöds inte på Azure-filresurser som hanteras av Azure File Sync.
> - Azure AD DS-autentisering stöder inte autentisering mot dator konton som skapats i Azure AD DS.
