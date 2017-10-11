---
title: "Registrera ett program med Azure AD v2.0-slutpunkten med hjälp av portalen | Microsoft Docs"
description: "Så här registrerar du en app med Microsoft för att aktivera inloggning och åtkomst till Microsoft-tjänster med v2.0-slutpunkten"
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: e6202aa8665c906382666fe08a561421e50e0a8d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Hur du registrerar en app med v2.0-slutpunkten
Om du vill skapa en app som accepterar både MSA och Azure AD-inloggning, du först behöver registrera en app med Microsoft.  För närvarande kan du inte använda några befintliga appar som du kan ha med Azure AD eller MSA - du behöver skapa en helt ny.

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten.  Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

## <a name="visit-the-microsoft-app-registration-portal"></a>Besök portalen för registrering av Microsoft-app
Första saker först - gå till [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Det här är en ny app registrering portal där du kan hantera dina Microsoft-appar.

Logga in med antingen en personlig eller arbets- eller skolkonto för Microsoft.  Om du inte har antingen registrera dig för ett nytt personligt konto. Gå vidare, det tar inte lång - vi väntar här.

Vill du göra? Du bör nu tittar på listan över Microsoft-appar som är förmodligen tom.  Ska vi ändra.

Klicka på **Lägg till en app**, och ge det ett namn.  Portalen kommer att tilldela din app ett globalt unikt Id för programmet som du vill använda senare i koden.  Om appen innehåller en komponent på serversidan som behöver åtkomst-token för anropa API: er (tror: Office, Azure eller egna webb-API), ska du skapa en **Programhemlighet** även här.

Lägg till de plattformar som ska använda för din app.

* Webbaserade appar, ange en **omdirigerings-URI** där inloggning meddelanden kan skickas.
* För mobila appar, kopiera fördefinierade omdirigerings-uri som skapats automatiskt åt dig.

Du kan också kan du anpassa utseendet och känslan av inloggningssidan under profil.  Se till att klicka på **spara** innan du fortsätter.

> [!NOTE]
> När du skapar ett program som använder [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), programmet kommer att registreras i hem-klient för det konto som du använder för att logga in på portalen.  Det innebär att du inte registrera ett program i Azure AD-klienten med ett personligt microsoftkonto.  Om du uttryckligen vill registrera ett program i en viss klient logga in med ett konto som ursprungligen skapades i den klienten.
> 
> 

## <a name="build-a-quick-start-app"></a>Skapa en app för Snabbstart
Nu när du har en Microsoft-app kan du slutföra en av våra självstudier för v2.0-Snabbstart.  Här är några rekommendationer:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

