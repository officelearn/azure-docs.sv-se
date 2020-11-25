---
title: Öka återhämtningen av autentiserings-och auktoriserings program som du utvecklar
titleSuffix: Microsoft identity platform
description: Översikt över vår återhämtnings vägledning för program utveckling med Azure Active Directory och Microsoft Identity Platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: f6078ed96106d6a53b55195ee950e2165334221d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920080"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Öka återhämtningen av autentiserings-och auktoriserings program som du utvecklar

Microsoft Identity använder modern token-baserad autentisering och auktorisering. Det innebär att ett program hämtar token från en identitets leverantör för att autentisera användaren och för att ge programmet möjlighet att anropa skyddade API: er.

![Bild av programmet som anropar Microsoft Identity](media/resilience-app-development-overview/app-overview.png)

En token är giltig under en viss tids period innan appen måste hämta en ny. Ett anrop för att hämta en token kan sällan Miss lyckas på grund av ett problem som nätverks-eller infrastruktur fel eller strömavbrott i tjänsten. I det här dokumentet beskriver vi steg för steg hur en utvecklare kan ta för att öka återhämtningen i sina program om ett fel uppstår vid hämtning av token.

De här artiklarna ger vägledning om ökad återhämtning i appar med hjälp av Microsoft Identity Platform och Azure Active Directory. Det finns vägledning för både klient program som arbetar på uppdrag av en inloggad användare och program för daemon som arbetar på egen ställe. De innehåller metod tips för att använda tokens och anropa resurser.

- [Bygg återhämtning till program som loggar in användare](resilience-client-app.md)
- [Bygg återhämtning till program utan användare](resilience-daemon-app.md)
- [Bygg återhämtning i din infrastruktur för identitets-och åtkomst hantering](resilience-in-infrastructure.md)
- [Bygg återhämtning i din kund identitets-och åtkomst hantering med Azure Active Directory B2C](resilience-b2c.md)