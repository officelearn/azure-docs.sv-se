---
title: "Länkarna på sidan fungerar inte för ett program med Application Proxy | Microsoft Docs"
description: "Felsökning av problem med brutna länkar på Application Proxy-program som du har integrerat med Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 17f2afb0aaf3b899784a504b77f33a1284f0a232
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Länkarna på sidan fungerar inte för ett program med Application Proxy

Den här artikeln hjälper dig att felsöka anledningen länkarna på ditt Azure Active Directory Application Proxy-program inte fungerar korrekt.

## <a name="overview"></a>Översikt 
När du har publicerat en app i Application Proxy är bara länkarna som fungerar som standard i programmet länkar till mål i publicerade rot-URL. Länkar i programmen fungerar inte, Intern URL för programmet inte innehåller förmodligen alla mål för länkar i programmet.

**Varför händer det här?** När du klickar på en länk i ett program försöker Application Proxy matcha URL: en som en intern URL inom samma program eller som ett externt tillgänglig URL. Om länken pekar på en intern URL inte ligger inom samma program, den inte tillhör någon av dessa behållare och resultera i ett hittades inte.

## <a name="ways-you-can-resolve-broken-links"></a>Sätt som du kan lösa brutna länkar

Det finns tre sätt att lösa problemet. Alternativen nedan visas i i stigande komplexitet.

1.  Kontrollera att intern URL är en rotcertifikatutfärdare som innehåller de relevanta länkarna för programmet. Detta gör att alla länkar till att matcha som innehåll som publiceras inom samma program.

    Om du ändrar Intern URL, men inte vill ändra Landningssida för användare, kan du ändra den URL-Adressen till tidigare publicerade Intern URL. Detta kan göras genom att gå till ”Azure Active Directory -”&gt; App registreringar -&gt; Välj program -&gt; egenskaper. I den här fliken kan du se fältet ”startsidan URL” som du kan justera ska önskade denna sida.

2.  Om dina program använder fullständigt kvalificerade domännamn (FQDN), använda [anpassade domäner](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) att publicera dina program. Den här funktionen gör att samma URL som ska användas både internt och externt.

    Det här alternativet ser till att länkar i ditt program är externt tillgänglig via Application Proxy eftersom länkarna i programmet till Intern URL-adresser identifieras också externt. Observera att alla länkar fortfarande måste tillhöra ett publicerat program. Men med det här alternativet länkarna behöver du inte tillhör samma program och kan höra till flera program.

3.  Om inget av dessa alternativ är möjlig, ansluta förhandsgranskning för en ny funktion som gör översättning/skriva om URL: en. Med det här alternativet vara intern URL: er eller länkar som finns i HTML-innehållet i dina program översättas eller ”mappas”, publicerade externa App Proxy URL: er. Den här metoden fungerar för länkar i HTML- eller CSS och det hjälper inte om länken genereras via JS. 

Därför rekommenderar vi använder den [anpassade domäner](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) lösning om möjligt. Om du vill ansluta till förhandsversionen av e- <aadapfeedback@microsoft.com> med applicationId(s).

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-working-with-proxy-servers.md)

