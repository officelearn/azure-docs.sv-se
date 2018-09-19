---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7f086c44abf6c9002c47904dc722294e046528f7
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293773"
---
## <a name="test-your-app"></a>Testa din app

1. Köra din kod till din enhetsemulatorn.

2. Försök att logga in med ett Azure Active Directory-konto (arbets- eller skolkonto konto) eller Microsoft-konto (live.com, outlook.com). 

    ![Testa ditt program](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Ange användarnamn och lösenord](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>Godkänna din app
Första gången en användare loggar in på ditt program, uppmanas de att godkänna behörigheterna som appen behöver, som visas här: 

![Ge ditt medgivande för programåtkomst](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="success"></a>Lyckades!
När du loggar in och godkänna visar appen svar från Microsoft Graph API. Det här specifika anropet är att den **/me** slutpunkten och returnerar den [användarprofil](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get). En lista över andra Microsoft Graph-slutpunkter, se [Microsoft Graph API-utvecklardokumentation](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>Scope och delegerade behörigheter

Microsoft Graph API kräver den *User.Read* omfattning att läsa en användares profil. Det här området är automatiskt i varje app som är registrerade i portalen för registrering av programmet. API: er kräver ytterligare scope. Till exempel Microsoft Graph API kräver den *Calendars.Read* omfattning att lista användarens kalendrar. 

För att komma åt användarens kalendrar, lägger du till den *Calendars.Read* delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den *Calendars.Read* begränsa omfånget till den `acquireTokenSilent` anropa. 

>[!NOTE]
>Dina användare kan uppmanas om ytterligare samtycke om du ändrar din appregistrering.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
