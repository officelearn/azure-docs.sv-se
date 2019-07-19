---
title: 'Azure Active Directory Domain Services: Aktivera SharePoint-tjänsten för användar profiler | Microsoft Docs'
description: Konfigurera Azure Active Directory Domain Services hanterade domäner för att stödja profilsynkronisering för SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4a9ee05b37a69927d70269dccef2b74a2c251722
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234108"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Konfigurera en hanterad domän som stöder profilsynkronisering för SharePoint Server
SharePoint Server innehåller en användar profil tjänst som används för synkronisering av användar profiler. Om du vill konfigurera användar profil tjänsten måste lämpliga behörigheter beviljas för en Active Directory domän. Mer information finns i [bevilja Active Directory Domain Services behörigheter för profilsynkronisering i SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

Den här artikeln förklarar hur du kan konfigurera Azure AD Domain Services hanterade domäner för att distribuera Sync-tjänsten för användar profiler för SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Gruppen "AAD DC Service accounts"
En säkerhets grupp med namnet "**AAD DC Service accounts**" är tillgänglig i organisations enheten ' användare ' på din hanterade domän. Du kan se den här gruppen i MMC-snapin-modulen **Active Directory användare och datorer** på din hanterade domän.

![AAD DC service-konton säkerhets grupp](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Medlemmar i den här säkerhets gruppen har delegerats följande privilegier:
- Privilegiet "replikera katalog ändringar" på rot-DSE för den hanterade domänen.
- Privilegiet "replikera katalog ändringar" i konfigurations namngivnings kontexten (CN = Configuration container) för den hanterade domänen.

Den här säkerhets gruppen är också medlem i den inbyggda gruppen **för Windows 2000-kompatibel åtkomst**.

![AAD DC service-konton säkerhets grupp](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Aktivera din hanterade domän för att stödja synkronisering av användar profiler för SharePoint Server
Du kan lägga till tjänst kontot som används för SharePoint-synkronisering av användar profiler till **AAD DC Service accounts** Group. Därför får synkroniseringsschemat tillräckliga privilegier för att replikera ändringar i katalogen. Det här konfigurations steget gör att synkroniseringen av användar profiler i SharePoint Server fungerar korrekt.

![AAD DC service-konton – Lägg till medlemmar](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD DC service-konton – Lägg till medlemmar](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Relaterat innehåll
* [Teknisk referens – bevilja Active Directory Domain Services behörigheter för profilsynkronisering i SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
