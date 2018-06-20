---
title: 'Azure Active Directory Domain Services: Aktivera stöd för användarprofil för SharePoint service | Microsoft Docs'
description: Konfigurera Azure Active Directory Domain Services hanterade domäner för att stödja profilsynkronisering för SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: e1162a3f87c9242e7366109302b3bd807b1fdcc8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214383"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Konfigurera en hanterad domän för att stödja profilsynkronisering för SharePoint Server
SharePoint Server innehåller en tjänsten användarprofil som används för synkronisering av användarprofiler. Om du vill konfigurera tjänsten användarprofil behöver rätt behörigheter beviljas på en Active Directory-domän. Mer information finns i [bevilja behörigheter för Active Directory Domain Services för profilsynkronisering i SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

Den här artikeln förklarar hur du kan konfigurera Azure AD Domain Services hanterade domäner om du vill distribuera tjänsten SharePoint Server synkronisering av användarprofiler.

## <a name="the-aad-dc-service-accounts-group"></a>Gruppen 'AAD DC Service Accounts'
En säkerhetsgrupp som heter '**AAD DC-tjänstkonton**' är tillgänglig i organisationsenheten 'Användare' på din hanterade domän. Du kan se den här gruppen i den **Active Directory-användare och datorer** MMC snapin-modulen på din hanterade domän.

![AAD DC-tjänstkonton säkerhetsgrupp](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Medlemmar i den här säkerhetsgruppen delegeras följande behörigheter:
- Privilegiet ”Replikera katalogändringar' på root DSE för den hanterade domänen.
- Privilegiet ”Replikera katalogändringar' på konfigurationsnamngivningskontexten (cn = konfigurationsbehållare) för den hanterade domänen.

Den här säkerhetsgruppen även är medlem i den inbyggda gruppen **kompatibel åtkomst innan Windows 2000**.

![AAD DC-tjänstkonton säkerhetsgrupp](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Aktivera din hanterade domän som stöd för synkronisering av SharePoint Server användarprofiler
Du kan lägga till tjänstkontot som används för synkronisering av användarprofil SharePoint till den **AAD DC-tjänstkonton** grupp. Konto för synkronisering hämtar därför tillräcklig behörighet för att replikera ändringar i katalogen. Det här konfigurationssteget gör det möjligt för synkronisering av SharePoint Server användarprofiler ska fungera korrekt.

![AAD-DC-tjänstkonton - lägga till medlemmar](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD-DC-tjänstkonton - lägga till medlemmar](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Relaterat innehåll
* [Teknisk referens - bevilja Active Directory Domain Services-behörigheter för profilsynkronisering i SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
