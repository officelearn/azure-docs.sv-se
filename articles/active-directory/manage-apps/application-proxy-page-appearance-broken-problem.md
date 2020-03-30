---
title: Appsidan visas inte korrekt för programproxyapp | Microsoft-dokument
description: Vägledning när sidan inte visas korrekt i ett programproxyprogram som du har integrerat med Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381426"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Programsidan visas inte korrekt för ett programproxyprogram

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory Application Proxy-program när du navigerar till sidan, men något på sidan ser inte korrekt ut.

## <a name="overview"></a>Översikt
När du publicerar en programproxyapp är endast sidor under roten tillgängliga när du öppnar programmet. Om sidan inte visas korrekt kan den interna url:en för roten som används för programmet saknas vissa sidresurser. Lös problemet genom att kontrollera att du har publicerat *alla* resurser för sidan som en del av programmet.

Du kan kontrollera om resurser saknas genom att öppna nätverksspåraren (till exempel Fiddler eller F12-verktygen i Internet Explorer/Microsoft Edge), läsa in sidan och leta efter 404 fel. Det anger att sidorna för närvarande inte kan hittas och att du måste publicera dem.

Som ett exempel på det här fallet antar vi att `http://myapps/expenses`du har publicerat ett `http://myapps/style.css`utgiftsprogram med den interna URL:en , men appen använder formatmallen . I det här fallet publiceras inte formatmallen i ditt program, så läser in utgiftsappen ett 404-fel när du försöker läsa in style.css. I det här exemplet löses problemet genom att `http://myapp/`publicera programmet med en intern URL .

## <a name="problems-with-publishing-as-one-application"></a>Problem med publicering som ett program

Om det inte är möjligt att publicera alla resurser i samma program måste du publicera flera program och aktivera länkar mellan dem.

För att göra det rekommenderar vi att du använder den [anpassade domänlösningen.](application-proxy-configure-custom-domain.md) Den här lösningen kräver dock att du äger certifikatet för din domän och dina program använder fullständigt kvalificerade domännamn (FQDN). Fler alternativ finns i [dokumentationen för felsöka brutna länkar](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Nästa steg
[Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md)
