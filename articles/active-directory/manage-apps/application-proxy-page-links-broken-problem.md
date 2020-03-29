---
title: Länkar på sidan fungerar inte för ett program proxyprogram
description: Felsöka problem med brutna länkar i programproxyprogram som du har integrerat med Azure AD
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430235"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Länkar på sidan fungerar inte för ett program proxyprogram

Den här artikeln hjälper dig att felsöka varför länkar på azure Active Directory Application Proxy-programmet inte fungerar korrekt.

## <a name="overview"></a>Översikt 
När du har publicerat en programproxy-app är de enda länkarna som fungerar som standard i programmet länkar till mål som finns i den publicerade rot-URL:en. Länkarna i programmen fungerar inte, den interna URL:en för programmet innehåller förmodligen inte alla länkar i programmet.

**Varför sker det här?** När du klickar på en länk i ett program försöker Programproxy matcha URL:en som antingen en intern URL i samma program eller som en externt tillgänglig URL. Om länken pekar på en intern URL som inte finns i samma program, tillhör den inte någon av dessa buckets och resulterar i ett fel som inte hittades.

## <a name="ways-you-can-resolve-broken-links"></a>Sätt du kan lösa brutna länkar

Det finns tre sätt att lösa problemet. Alternativen nedan är listade i ökande komplexitet.

1.  Kontrollera att den interna URL:en är en rot som innehåller alla relevanta länkar för programmet. Detta gör att alla länkar kan lösas som innehåll som publiceras i samma program.

    Om du ändrar den interna webbadressen men inte vill ändra målsidan för användare ändrar du startsidesadressen till den tidigare publicerade interna webbadressen. Detta kan göras genom att gå till&gt; "Azure&gt; Active Directory"&gt; - Appregistreringar - välj programmet - Branding. I avsnittet branding visas fältet "Webbadressen", som du kan justera för att vara önskad målsida. Om du fortfarande använder den äldre appregistreringarna visas information om egenskaper på fliken "Webbadress". 
    
    > [!IMPORTANT]
    > För att göra ovanstående ändringar behöver du rättigheter att ändra programobjekt i Azure AD. Användaren måste tilldelas [rollen Programadministratör](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) som ger programmodifieraionsrättigheter i Azure AD till användaren.
    >

2.  Om dina program använder fullständigt kvalificerade domännamn (FQDN) använder du [anpassade domäner](application-proxy-configure-custom-domain.md) för att publicera dina program. Med den här funktionen kan samma URL användas både internt och externt.

    Det här alternativet säkerställer att länkarna i ditt program är externt tillgängliga via Programproxy eftersom länkarna i programmet till interna webbadresser också känns igen externt. Alla länkar måste fortfarande tillhöra en publicerad ansökan. Med det här alternativet behöver dock länkarna inte tillhöra samma program och kan tillhöra flera program.

3.  Om inget av dessa alternativ är genomförbart finns det flera alternativ för att aktivera infogad länköversättning. De här alternativen inkluderar att använda intune-hanterade webbläsare, tillägget Mina appar eller använda länköversättningsinställningen i ditt program. Mer information om vart och ett av dessa alternativ och hur du aktiverar dem finns i [Omdirigera hårdkodade länkar för appar som publiceras med Azure AD Application Proxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)

