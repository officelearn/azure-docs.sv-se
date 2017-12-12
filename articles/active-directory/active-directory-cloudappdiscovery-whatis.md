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
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5875cf4cc0938607fa1ff6adf840d513d1fb85bd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Hitta ohanterade molnprogram med Cloud App Discovery
## <a name="summary"></a>Sammanfattning

Cloud App Discovery är en funktion i Azure Active Directory Premium som gör att du kan identifiera ohanterade molnprogram som används av personer i din organisation. Moderna företag, IT-avdelningar ofta inte är medveten om alla molnprogram som medlemmar i organisationen använder för att utföra sitt arbete. Det är enkelt att se varför administratörer skulle oroar obehörig åtkomst till företagsdata, möjliga dataläckage och andra säkerhetsrisker. Den här bristen på medvetenhet kan göra genom att skapa en plan för hantering av dessa säkerhetsrisker verka avskräckande.

> [!TIP] 
> Förbättringar av Cloud App Discovery i Azure Active Directory (Azure AD), som har förbättrats genom att kolla [integrering med Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

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

