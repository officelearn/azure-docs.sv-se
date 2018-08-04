---
title: Registrera ett program med Azure AD v2.0-slutpunkten med hjälp av portalen | Microsoft Docs
description: Hur du registrerar en app med Microsoft för att aktivera logga in och komma åt Microsoft services med v2.0-slutpunkten
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506551"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Hur du registrerar en app med v2.0-slutpunkten
Om du vill skapa en app som accepterar både personliga Microsoft-konto (MSA) och arbete eller skola konto (Azure AD)-inloggningen, behöver du först registrera en app med Microsoft. Just nu, du kan använda alla befintliga appar som du kan ha med Azure AD eller MSA - måste du skapa ett nytt program.

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, den [v2.0 begränsningar](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Gå till portalen för registrering av Microsoft-app
Navigera först till registreringsportalen på Microsoft app [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Logga in med antingen en personlig eller arbets- eller skolkonto för Microsoft. Om du inte har någon av dessa kan registrera dig för ett nytt personligt konto.

Klar? Du bör tittar på din lista över Microsoft-appar som är förmodligen tom. Nu ska vi ändra som.

Klicka på **lägga till en app**, och ge den ett namn. Portalen tilldelar en app i ett globalt unikt ID som du ska använda senare i koden. Om din app innehåller en komponent på serversidan som behöver åtkomst-token för anropa API: er (tror: Office, Azure eller dina egna webb-API), kommer du att skapa en **Programhemlighet** även här.

Lägg sedan till den **plattformar** som din app ska använda.

* För webbaserade program, anger du en **omdirigerings-URI** där inloggning meddelanden kan skickas.
* Kopiera omdirigerings-URI: N skapas automatiskt åt dig den standard för mobila appar.
* För webb-API: er skapas en standardomfattning för att få åtkomst till webb-API automatiskt åt dig. Du kan välja att lägga till ytterligare scope med hjälp av den **lägga till Scope** knappen. Du kan också lägga till alla program som redan har auktoriserats att använda din webb-API med hjälp av den **före auktoriserade program** formuläret. 

Du kan också du kan anpassa utseendet på sidan logga in i den **profil** avsnittet. Se till att klicka på **spara** innan du fortsätter.

> [!NOTE]
> När du skapar ett program med hjälp av [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), programmet registreras i startklientorganisation för det konto som användes för att logga in på portalen. Det innebär att du inte kan registrera ett program i Azure AD-klienten med hjälp av ett personligt microsoftkonto. Om du uttryckligen vill registrera ett program i en viss klient kan du logga in med ett konto som ursprungligen skapades i den klienten.


## <a name="build-a-quickstart-app"></a>Skapa en Snabbstart-app
Nu när du har en Microsoft-app kan slutföra du en av snabbstartsguiderna v2.0. Här är några rekommendationer:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

