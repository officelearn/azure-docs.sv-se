---
title: Felsöker inloggningsfelrapporter | Microsoft-dokument
description: Lär dig hur du felsöker inloggningsfel med Hjälp av Azure Active Directory-rapporter i Azure-portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008061"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Så här felsöker du inloggningsfel med Hjälp av Azure Active Directory-rapporter

[Med inloggningsrapporten](concept-sign-ins.md) i Azure Active Directory (Azure AD) kan du hitta svar på frågor kring hur du hanterar åtkomst till programmen i organisationen, inklusive:

- Vilket inloggningsmönster har en användare?
- Hur många användare har en användare loggat in under en vecka?
- Vad är status för dessa inloggningar?


Dessutom kan inloggningsrapporten också hjälpa dig att felsöka inloggningsfel för användare i organisationen. I den här guiden får du lära dig hur du isolerar ett inloggningsfel i inloggningsrapporten och använder den för att förstå orsaken till felet.

## <a name="prerequisites"></a>Krav

Du behöver:

* En Azure AD-klient med en premiumlicens (P1/P2). Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version.
* En användare som är i den **globala administratören,** **säkerhetsadministratör,** **säkerhetsläsare**eller **rapportläsare** roll för klienten. Dessutom kan alla användare komma åt sina egna inloggningar. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Felsöka inloggningsfel med hjälp av inloggningsrapporten

1. Gå till [Azure-portalen](https://portal.azure.com) och välj din katalog.
2. Välj **Azure Active Directory** och välj **Inloggningar** i avsnittet **Övervakning**. 
3. Använd de medföljande filtren för att begränsa felet, antingen med användarnamnet eller objektidentifieraren, programnamnet eller datumet. Dessutom väljer du **Fel** i listrutan **Status** om du bara vill visa de misslyckade inloggningarna. 

    ![Filtrera resultat](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifiera den misslyckade inloggning som du vill undersöka. Välj det för att öppna ytterligare informationsfönstret med mer information om den misslyckade inloggningen. Anteckna **felkoden för inloggning** och **felorsaken**. 

    ![Välj post](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Du hittar även den här informationen på fliken **Felsökning och support** i informationsfönstret.

    ![Felsökning och support](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Felorsaken beskriver felet. I ovanstående scenario är felorsaken **ogiltigt användarnamn eller lösenord eller Ogiltigt lokalt användarnamn eller lösenord**. Fix är att helt enkelt logga in igen med rätt användarnamn och lösenord.

7. Du kan få ytterligare information, inklusive idéer för reparation, genom att söka efter felkoden, **50126** i det här exemplet, [i inloggningsfelkoder referens](reference-sign-ins-error-codes.md). 

8. Om allt annat misslyckas, eller om problemet kvarstår trots att du vidtar det rekommenderade tillvägagångssättet, [öppnar du en supportbiljett](../fundamentals/active-directory-troubleshooting-support-howto.md) enligt stegen på fliken **Felsökning och support.** 

## <a name="next-steps"></a>Nästa steg

* [Referens för inloggningar av felkoder](reference-sign-ins-error-codes.md)
* [Översikt över rapportöversikt för inloggningar](concept-sign-ins.md)
