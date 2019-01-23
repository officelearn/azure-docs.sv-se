---
title: Programsidan visas inte korrekt för ett program med Application Proxy | Microsoft Docs
description: Vägledning när sidan inte visas korrekt i ett Application Proxy-program som du har integrerat med Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ec0fe8179aee186f147c4898d70393b018341c02
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472559"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Programsidan visas inte korrekt för ett Application Proxy-program

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory Application Proxy-program när du navigerar du till sidan, men något på sidan ser inte ut korrekt.

## <a name="overview"></a>Översikt
När du publicerar en app med Application Proxy kan är bara sidor under rotcertifikatutfärdaren tillgängliga vid åtkomst till programmet. Om sidan inte visas korrekt, kan den interna rot-URL som används för programmet sakna vissa resurser för sidan. Lös, se till att du har publicerat *alla* resurser för sidan som en del av ditt program.

Du kan kontrollera om resurser som saknas är problemet genom att öppna spårningsverktyget för nätverk (till exempel Fiddler eller F12 verktyg i Internet Explorer-/ Microsoft Edge), läser in sidan och söker efter 404-fel. Värde som anger för närvarande inte går att hitta sidorna och att du behöver att publicera dem.

Anta att du har publicerat ett utgifter-program med den interna URL: en som ett exempel på det här fallet http://myapps/expenses, men appen använder formatmallen http://myapps/style.css. I det här fallet publiceras inte formatmallen i ditt program så att läsa in den utgifter app genererar ett 404-fel vid försök att läsa in style.css. I det här exemplet problemet är löst genom att publicera program med en intern Webbadress http://myapp/.

## <a name="problems-with-publishing-as-one-application"></a>Problem med publicering som ett program

Om det inte går att publicera alla resurser i samma program, måste du publicera flera program och aktivera länkar mellan dem.

Om du vill göra det, bör du använda den [anpassade domäner](application-proxy-configure-custom-domain.md) lösning. Den här lösningen kräver dock att du äger certifikatet för din domän och dina program använder fullständigt kvalificerade domännamn (FQDN). Andra alternativ finns i den [felsöka brutna länkar dokumentation](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Nästa steg
[Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md)
