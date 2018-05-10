---
title: Med hjälp av b2clogin.com| Microsoft Docs
description: Lär dig mer om hur du använder b2clogin.com i stället för login.microsoftonline.com.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
#<a name="using-b2clogincom"></a>Med hjälp av b2clogin.com

>[!IMPORTANT]
>Den här funktionen är förhandsversion 
>

Nu har du möjlighet att använda Azure AD B2C-tjänsten med `<YourTenantName>.b2clogin.com` istället för att använda `login.microsoftonline.com`.  Detta har många fördelar:
* Du kommer inte längre dela samma storleksgränsen för cookie-sidhuvud med andra Microsoft-produkter
* Du kan ta bort alla referenser till Microsoft i URL: en (du kan ersätta `<YourTenantName>.onmicrosoft.com` med klient-ID)

 För att kunna dra nytta av b2clogin.com, måste du ange något av följande:

1. För din **kör nu endpoint** kontrollerar du att du använder `<YourTenantName>.b2clogin.com` istället för att använda `login.microsoftonline.com`.
2. Om du använder MSAL, måste du ange `validateauthority=false`.  Detta beror på att token utfärdaren blir`<YourTenantName>.b2clogin.com`.