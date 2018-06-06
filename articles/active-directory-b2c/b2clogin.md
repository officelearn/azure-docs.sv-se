---
title: Med hjälp av b2clogin.com | Microsoft Docs
description: Lär dig mer om hur du använder b2clogin.com i stället för login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c41c02acaeffa170d55f3c59f34a4b1ecae1c523
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712461"
---
# <a name="using-b2clogincom"></a>Med hjälp av b2clogin.com

>[!IMPORTANT]
>Den här funktionen är förhandsversion 
>

Nu har du möjlighet att använda Azure AD B2C-tjänsten med `<YourTenantName>.b2clogin.com` istället för att använda `login.microsoftonline.com`.  Detta har många fördelar:
* Du kommer inte längre dela samma storleksgränsen för cookie-sidhuvud med andra Microsoft-produkter
* Du kan ta bort alla referenser till Microsoft i URL: en (du kan ersätta `<YourTenantName>.onmicrosoft.com` med klient-ID)

 För att kunna dra nytta av b2clogin.com, måste du ange något av följande:

1. För din **kör nu endpoint** kontrollerar du att du använder `<YourTenantName>.b2clogin.com` istället för att använda `login.microsoftonline.com`.
2. Om du använder MSAL, måste du ange `validateauthority=false`.  Detta beror på att token utfärdaren blir`<YourTenantName>.b2clogin.com`.