---
title: Länkarna på sidan fungerar inte för ett program med Application Proxy | Microsoft Docs
description: Så här felsöker du problem med brutna länkar på Application Proxy-program som du har integrerat med Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d860ac77653a97855248c45e43d28ea4c694979e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170915"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Länkarna på sidan fungerar inte för ett Application Proxy-program

Den här artikeln hjälper dig att felsöka varför länkarna på ditt Azure Active Directory Application Proxy-program inte fungerar korrekt.

## <a name="overview"></a>Översikt 
När du har publicerat en app med Application Proxy är de enda länkar som fungerar som standard i programmet länkar till mål som ingår i den publicerade rot-URL. Länkar i programmen fungerar inte, den interna URL: en för programmet inte innehåller förmodligen alla mål för länkar i programmet.

**Varför inträffar det?** När du klickar på en länk i ett program, försöker Application Proxy hitta URL: en som en intern URL inom samma program eller som en URL som är externt tillgänglig. Om länken pekar på en intern Webbadress som inte ligger inom samma program, den inte tillhör någon av dessa behållare och leda till felet hittades inte.

## <a name="ways-you-can-resolve-broken-links"></a>Sätt som du kan lösa brutna länkar

Det finns tre sätt att lösa problemet. Alternativen nedan visas i i allt mer komplexa.

1.  Kontrollera att den interna URL: en är en rotcertifikatutfärdare som innehåller de relevanta länkarna för programmet. Detta gör att alla länkar kan matchas som innehåll som publicerats i samma program.

    Om du ändrar den interna URL: en, men inte vill ändra landningssidan för användare, kan du ändra hemsidans URL till den tidigare publicerade interna URL: en. Detta kan göras genom att gå till ”Azure Active Directory” -&gt; Appregistreringar -&gt; väljer du programmet -&gt; egenskaper. I den här fliken Egenskaper kan se du fältet ”startsida URL”, vilket du kan anpassa sig till önskad landningssidan för att vara.

2.  Om ditt program använder fullständigt kvalificerade domännamn (FQDN), använda [anpassade domäner](application-proxy-configure-custom-domain.md) att publicera dina program. Den här funktionen kan du samma URL som ska användas både internt och externt.

    Det här alternativet säkerställer att länkar i ditt program är externt tillgänglig via programproxy eftersom länkarna i programmet till interna URL: er identifieras också externt. Alla länkar måste tillhöra ett publicerat program. Men med det här alternativet länkarna behöver inte tillhöra samma program och kan höra till flera program.

3.  Om inget av dessa alternativ är möjlig, finns det flera alternativ för att aktivera infogad länköversättning. Dessa alternativ inkluderar med hjälp av Intune Managed Browser, Mina appar-tillägg, eller med hjälp av länken anonymt i programmet. Läs mer om var och en av dessa alternativ och hur du aktiverar dem i [omdirigera hårdkodad länkar till appar som publiceras med Azure AD Application Proxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)

