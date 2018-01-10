---
title: Hitta ohanterade molnprogram med Cloud App Discovery i Azure Active Directory | Microsoft Docs
description: "Innehåller information om att söka efter och hantera program med Cloud App Discovery, vilka är fördelarna och hur det fungerar."
services: active-directory
keywords: cloud app discovery, hantera program
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Hitta ohanterade molnprogram med Cloud App Discovery
## <a name="summary"></a>Sammanfattning

Cloud App Discovery i Azure Active Directory ger nu en förbättrad utan Agent discovery-upplevelse som drivs av Microsoft Cloud App Security. Om du vill använda Cloud App Discovery bara logga in med dina Azure AD Premium P1. Den här uppdateringen är tillgänglig utan extra kostnad för alla Azure AD Premium P1-kunder. Kom igång med artikeln [konfigurera Cloud App Discovery i Azure AD](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started), sedan testa [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/).

> [!IMPORTANT] 
> Aktuellt Azure AD Cloud App Discovery erfarenhet av agent-baserad identifiering är att stängas av på 5 mars 2018, efter vilken agenterna är inaktiverad och data tas bort. Utför åtgärd före mars 5 för att få igång på den nya upplevelsen för att undvika avbrott i tjänsten.  
 
**Med Cloud App Discovery kan du:**

* Hitta molnprogram som används och mäta denna användning av antalet användare, datavolym trafik eller antalet webbegäranden till programmet.
* Identifiera de användare som använder ett program.
* Exportera data för offlineanalys.
* Se dessa program under IT-kontroll och aktivera enkel inloggning för användarhantering.

## <a name="how-it-works"></a>Hur det fungerar
1. Programmet användning agenter är installerade på användarens dator.
2. Programmet användningsinformation som avbildas av agenterna skickas via en säker och krypterad kanal till cloud app discovery-tjänsten.
3. Cloud App Discovery-tjänsten utvärderar data och genererar rapporter.

![Cloud App Discovery-diagram](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>Nästa steg
* [Cloud App Discovery säkerhets- och överväganden för sekretess](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Cloud App Discovery-registerinställningar för proxyservrar med anpassade portar](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery-agenten Changelog](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)

