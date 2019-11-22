---
title: Länkar på sidan fungerar inte för ett Application Proxy-program
description: Så här felsöker du problem med brutna länkar i Application Proxy-program som du har integrerat med Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 570699fe83197a1b5442909d8b89e285a1dfa73b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275437"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Länkar på sidan fungerar inte för ett Application Proxy-program

Den här artikeln hjälper dig att felsöka varför länkar i Azure Active Directory-programproxy programmet inte fungerar som de ska.

## <a name="overview"></a>Översikt 
När du har publicerat en app för programproxy är de enda länkarna som fungerar som standard i programmet länkar till destinationer som finns i den publicerade rot-URL: en. Länkarna i programmen fungerar inte, den interna URL: en för programmet innehåller antagligen inte alla mål för länkar i programmet.

**Varför sker detta?** När du klickar på en länk i ett program försöker programproxyn matcha webb adressen antingen som en intern URL i samma program, eller som en extern URL. Om länken pekar på en intern URL som inte finns i samma program, tillhör den inte någon av dessa buckets och resulterar i ett fel som inte hittas.

## <a name="ways-you-can-resolve-broken-links"></a>Sätt att lösa brutna länkar

Det finns tre sätt att lösa det här problemet. Alternativen nedan visas i ökande komplexitet.

1.  Kontrol lera att den interna URL: en är en rot som innehåller alla relevanta länkar för programmet. Detta gör att alla länkar kan matchas när innehåll publiceras i samma program.

    Om du ändrar den interna URL: en men inte vill ändra landnings sidan för användare ändrar du Start sidans URL till den tidigare publicerade interna URL: en. Det kan du göra genom att gå till "Azure Active Directory"-&gt; app-registreringar –&gt; Välj program-&gt;-egenskaperna. På fliken Egenskaper ser du fältet "hem sidans URL", som du kan anpassa till den önskade landnings sidan.

2.  Om dina program använder fullständigt kvalificerade domän namn (FQDN) kan du använda [anpassade domäner](application-proxy-configure-custom-domain.md) för att publicera dina program. Den här funktionen tillåter att samma URL används både internt och externt.

    Med det här alternativet ser du till att länkarna i programmet kan nås externt via programproxyn, eftersom länkarna i programmet till interna URL: er också känns igen externt. Alla länkar måste redan tillhöra ett publicerat program. Men med det här alternativet behöver länkarna inte tillhöra samma program och kan tillhöra flera program.

3.  Om inget av dessa alternativ är möjligt finns det flera alternativ för att aktivera översättning av infogade länkar. De här alternativen omfattar att använda Intune Managed Browser, mina apps-tillägg eller använda länk översättnings inställningen i ditt program. Läs mer om var och en av dessa alternativ och hur du aktiverar dem i [omdirigera hårdkodad-Länkar för appar som publicerats med Azure AD-programproxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)

