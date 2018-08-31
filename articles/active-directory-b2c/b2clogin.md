---
title: Med hjälp av b2clogin.com | Microsoft Docs
description: Läs mer om med hjälp av b2clogin.com i stället för login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6ad0a5d59b28bf48742c9e1be89b51d2301dd582
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189298"
---
# <a name="using-b2clogincom"></a>Med hjälp av b2clogin.com

>[!IMPORTANT]
>Den här funktionen är allmänt tillgänglig förhandsversion 
>

Nu har du möjlighet att använda Azure AD B2C-tjänsten med `<YourTenantName>.b2clogin.com` istället för att använda `login.microsoftonline.com`.  Detta har många fördelar:
* Du kan inte längre dela samma storleksgränsen för cookie-sidhuvud med andra Microsoft-produkter.
* Du kan ta bort alla referenser till Microsoft i din URL (du kan ersätta `<YourTenantName>.onmicrosoft.com` med ditt klient-ID). Till exempel: `https://<tenantname>.b2clogin.com/tfp/<tenantID>/<policyname>/v2.0/.well-known/openid-configuration`.

 För att kunna dra nytta av b2clogin.com, måste du ange en del av följande:

1. För din **kör nu slutpunkt** se till att du använder `<YourTenantName>.b2clogin.com` istället för att använda `login.microsoftonline.com`.
2. Om du använder MSAL kan du behöva ange `validateauthority=false`.  Detta beror på att tokenutfärdaren blir`<YourTenantName>.b2clogin.com`.
