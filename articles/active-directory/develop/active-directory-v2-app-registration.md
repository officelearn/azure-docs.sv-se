---
title: Registrera ett program med Azure AD v2.0-slutpunkten med hjälp av portalen | Microsoft Docs
description: Så här registrerar du en app med Microsoft för att aktivera inloggning och åtkomst till Microsoft-tjänster med v2.0-slutpunkten
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 7c24271553f131e67711a3e8d914049e704e919e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Hur du registrerar en app med v2.0-slutpunkten
Om du vill skapa en app som accepterar både personliga Microsoft-konto (MSA) och arbete eller skola inloggning konto (Azure AD), behöver du först registrera en app med Microsoft. För närvarande kan du inte använda några befintliga appar som du kan ha med Azure AD eller MSA - du behöver skapa en helt ny.

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten på [v2.0 begränsningar](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Besök portalen för registrering av Microsoft-app
Först gå till registreringsportalen på Microsoft app [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Logga in med antingen en personlig eller arbets- eller skolkonto för Microsoft. Om du inte har antingen registrera dig för ett nytt personligt konto.

Vill du göra? Du bör nu tittar på listan över Microsoft-appar som är förmodligen tom. Ska vi ändra.

Klicka på **Lägg till en app**, och ge det ett namn.  Portalen kommer att tilldela din app ett globalt unikt ID för programmet som du vill använda senare i koden.  Om appen innehåller en komponent på serversidan som behöver åtkomst-token för anropa API: er (tror: Office, Azure eller egna webb-API), ska du skapa en **Programhemlighet** även här.

Lägg sedan till den **plattformar** som din app ska använda.

* För webbaserade program anger du en **omdirigerings-URI** där inloggning meddelanden kan skickas.
* Kopiera ned standard omdirigeringen URI automatiskt skapas för mobila appar.
* För webb-API: er skapas automatiskt ett standardscope för åtkomst till webb-API för dig. Du kan välja att lägga till ytterligare scope med hjälp av den **lägga till Scope** knappen. Du kan också lägga till alla program som redan har auktoriserats att använda webb-API med hjälp av den **före auktoriserade program** formuläret. 

Alternativt kan du kan anpassa utseendet och känslan av inloggningssidan i den **profil** avsnitt. Se till att klicka på **spara** innan du fortsätter.

> [!NOTE]
> När du skapar ett program som använder [ https://apps.dev.microsoft.com/ ](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), programmet kommer att registreras i hem-klient för det konto som du använder för att logga in på portalen. Det innebär att du inte registrera ett program i Azure AD-klienten med ett personligt microsoftkonto. Om du uttryckligen vill registrera ett program i en viss klient logga in med ett konto som ursprungligen skapades i den klienten.


## <a name="build-a-quickstart-app"></a>Skapa en app för Snabbstart
Nu när du har en Microsoft-app kan du slutföra någon av följande självstudiekurser för v2.0-Snabbstart.  Här är några rekommendationer:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

