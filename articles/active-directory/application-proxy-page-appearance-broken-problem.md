---
title: "Sidan program visas inte korrekt för ett program med Application Proxy | Microsoft Docs"
description: "Vägledning när sidan inte visas korrekt i ett Application Proxy-program som du har integrerat med Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cac4c333e59ef9a0f28a2f93a7afee22eeafd54e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Sidan program visas inte korrekt för ett program med Application Proxy

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory Application Proxy-program när du navigerar till sidan, men något på sidan ser inte rätt.

## <a name="overview"></a>Översikt
När du publicerar en Application Proxy-app är bara sidor under din roten tillgängliga vid åtkomst till programmet. Om sidan inte visas korrekt, kan den interna rot-URL som används för programmet saknas några resurser för sidan. För att lösa, se till att du har publicerat *alla* resurser för sidan som en del av ditt program.

Du kan kontrollera detta är problemet genom att öppna nätverk-spårning (till exempel Fiddler eller F12 verktyg i Internet Explorer/kant), sidan lästes in och söker efter 404-fel. Värde som anger de sidor som för närvarande går inte att hitta och du kan fortfarande publiceras.

Som ett exempel på det här fallet förutsätter att du har publicerat ett utgifter program med hjälp av en intern URL för <http://myapps/expenses>, men att appen använder formatmallen <http://myapps/style.css>. I det här fallet publiceras inte formatmallen i ditt program så inläsning appen utgifter utlösa ett 404-fel vid försök att läsa in style.css. I det här exemplet skulle att lösa problemet genom att publicera program med en intern URL <http://myapp/> i stället.

## <a name="problems-with-publishing-as-one-application"></a>Problem med publicering som ett program

Om det inte går att publicera alla resurser inom samma program, måste du publicera flera program och aktivera länkar mellan dem.

Om du vill göra det, bör du använda den [anpassade domäner](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) lösning. Denna lösning kräver dock att du äger certifikatet för din domän och ditt program använder fullständigt kvalificerade domännamn (FQDN). Andra alternativ finns på [felsöka brutna länkar dokumentationen](https://microsoft-my.sharepoint.com/personal/harshja_microsoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=IxuG3mFVbnPWI3Yn4Qi7wCNi8VIfHS5mwPt5quh8DMw%3d&docid=2_14558cd6ddea34c1c9887dc640feb5831&rev=1).

## <a name="next-steps"></a>Nästa steg
[Publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md)
