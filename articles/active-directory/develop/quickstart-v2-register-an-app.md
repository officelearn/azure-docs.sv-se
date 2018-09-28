---
title: Registrera en app med Azure AD v2.0-slutpunkten | Microsoft Docs
description: Lär dig hur du registrerar en app hos Microsoft för att möjliggöra inloggning och åtkomst till Microsoft-tjänster med hjälp av Azure AD v2.0-slutpunkten.
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
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: b2dea11b6573be8f574bd18fa69ee76658d6d698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975818"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>Snabbstart: Registrera en app med Azure Active Directory v2.0-slutpunkten

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Om du vill skapa en app som accepterar inloggning med både personligt Microsoft-konto (MSA) och arbets- eller skolkonto (Azure AD) måste du registrera en app med Azure Active Directory (Azure AD) v2.0-slutpunkten. Just nu kan du inte använda några befintliga appar som du kanske har med Azure AD eller MSA – du måste skapa en helt ny.

> [!NOTE]
> Inte alla Azure AD-scenarier och -funktioner stöds av v2.0-slutpunkten. Läs mer om [begränsningarna med v2.0](active-directory-v2-limitations.md) för att avgöra om du ska använda v2.0.

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>Steg 1: Gå till Microsoft-portalen för appregistrering

1. Navigera till Microsoft-portalen för appregistrering på [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).
1. Logga in med ett personligt konto eller ett Microsoft-konto för arbete eller skola. Om du inte har något konto registrerar du dig för ett personligt konto.
1. Klart? Du bör nu titta på din lista över Microsoft-appar, som troligen är tom. Vi ändrar på det.

## <a name="step-2-register-an-app"></a>Steg 2: Registrera en app

1. Välj **Add an app** (Lägg till en app) och ge den ett namn.
    Portalen tilldelar din app ett globalt unikt program-ID som du använder senare i koden. Om appen innehåller en komponent på serversidan som behöver åtkomsttoken för att anropa API:er (tänk: Office, Azure eller ett eget webb-API) skapar du även en **apphemlighet** här.
1. Lägg sedan till de **plattformar** som appen kommer att använda.
    * För webbaserade appar anger du en **omdirigerings-URI** dit inloggningsmeddelanden kan skickas.
    * För mobilappar kopierar du den standardinställda omdirigerings-URI:n som skapas automatiskt åt dig.
    * För webb-API:er skapas automatiskt ett standardomfång för åtkomst till webb-API:et åt dig.
        Du kan lägga till ytterligare omfång med knappen **Lägg till omfång**. Du kan även lägga till appar som har auktoriserats i förväg att använda ditt webb-API med hjälp av formuläret **Förauktoriserade program**.
1. Du kan också anpassa utseendet för inloggningssidan i avsnittet **Profil**. 
1. **Spara** ändringarna innan du fortsätter.

> [!NOTE]
> När du registrerar en app med [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) registreras appen i startklientorganisationen för det konto du använder för att logga in på portalen. Det betyder att du inte kan registrera en app i din Azure AD-klientorganisation med ett personligt Microsoft-konto. Om du uttryckligen vill registrera en app i en viss klientorganisation loggar du in med ett konto som ursprungligen har skapats i den klientorganisationen.

## <a name="next-steps"></a>Nästa steg

Nu när du har en Microsoft-app kan du slutföra en av v2.0-snabbstarterna. Här är några rekommendationer:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]
