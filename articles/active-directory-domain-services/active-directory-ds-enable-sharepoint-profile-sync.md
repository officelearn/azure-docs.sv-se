---
title: 'Azure Active Directory Domain Services: Aktivera stöd för SharePoint användarprofiltjänsten | Microsoft Docs'
description: Konfigurera Azure Active Directory Domain Services hanterade domäner för profilsynkronisering för SharePoint-Server
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: bb831c2b3cb9aee5146a667841a97771ff0ff85a
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843539"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Konfigurera en hanterad domän för profilsynkronisering för SharePoint-Server
SharePoint Server innehåller en användarprofiltjänsten som används för synkronisering av användarprofiler. Om du vill konfigurera användarprofiltjänsten måste rätt behörigheter beviljas på en Active Directory-domän. Mer information finns i [ge Active Directory Domain Services-behörigheter för profilsynkronisering i SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

Den här artikeln förklarar hur du kan konfigurera Azure AD Domain Services hanterade domäner om du vill distribuera tjänsten SharePoint Server synkronisering av användarprofiler.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Gruppen ”AAD DC-tjänstkonton'
En säkerhetsgrupp som heter ”**AAD DC-tjänstkonton**” finns i ”användare” organisationsenhet på den hanterade domänen. Du kan se den här gruppen i den **Active Directory-användare och datorer** MMC snapin-modulen på din hanterade domän.

![AAD DC-tjänstkonton säkerhetsgrupp](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Medlemmar i den här säkerhetsgruppen har delegerade följande behörigheter:
- -Replikera katalogändringar ”-behörighet på rot-DSE för den hanterade domänen.
- -Replikera katalogändringar ”-behörighet på konfigurationsnamngivningen (cn = konfigurationsbehållare) för den hanterade domänen.

Den här säkerhetsgruppen är också medlem i den inbyggda gruppen **kompatibel åtkomst innan Windows 2000**.

![AAD DC-tjänstkonton säkerhetsgrupp](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Aktivera din hanterade domän för synkronisering av SharePoint Server användarprofiler
Du kan lägga till tjänstkontot som används för profilsynkronisering för SharePoint-användare till den **AAD DC-tjänstkonton** grupp. Därför hämtar synkroniseringskontot tillräcklig behörighet för att replikera ändringar till katalogen. Det här konfigurationssteget gör det möjligt för synkronisering av SharePoint Server användarprofiler ska fungera korrekt.

![AAD DC-tjänstkonton – lägga till medlemmar](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD DC-tjänstkonton – lägga till medlemmar](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Relaterat innehåll
* [Teknisk referens - bevilja Active Directory Domain Services-behörigheter för profilsynkronisering i SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
