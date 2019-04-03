---
title: Så här felsöker du inloggningsfel med hjälp av Azure Active Directory-rapporter | Microsoft Docs
description: Lär dig att felsöka inloggning med Azure Active Directory-rapporter i Azure-portalen
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
ms.openlocfilehash: db68ad2a29dcaa53d219b679b9e0f24a50a6f576
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877065"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Anvisningar: Felsöka inloggning med Azure Active Directory-rapporter

Den [rapporten inloggningar](concept-sign-ins.md) i Azure Active Directory (Azure AD) kan du få svar på frågor kring att hantera åtkomst till program i din organisation, inklusive:

- Vilket inloggningsmönster har en användare?
- Hur många användare har en användare loggat in under en vecka?
- Vad är status för dessa inloggningar?


Dessutom rapporten inloggningar kan också hjälpa dig att felsöka inloggningar för användare i din organisation. Du lär dig hur du isolera ett fel i rapporten inloggningar och använda den för att förstå de grundläggande orsakerna till felet i den här guiden.

## <a name="prerequisites"></a>Förutsättningar

Du behöver:

* En Azure AD-klient med en premiumlicens (P1/P2). Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version.
* En användare som tillhör den **global administratör**, **säkerhetsadministratör**, **säkerhetsläsare**, eller **rapportera läsare** rollen för klienten. Dessutom kan alla användare komma åt sina egna inloggningar. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Felsöka inloggning med hjälp av rapporten inloggningar

1. Gå till [Azure-portalen](https://portal.azure.com) och välj din katalog.
2. Välj **Azure Active Directory** och välj **Inloggningar** i avsnittet **Övervakning**. 
3. Använd de angivna filtren för att begränsa fel, antingen efter användarnamn eller objekt-ID, namn eller datum. Dessutom väljer **fel** från den **Status** listrutan för att visa endast de misslyckade inloggningarna. 

    ![Filtrera resultat](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifiera misslyckade inloggningen du vill undersöka. Välj det för att öppna fönstret för ytterligare information öppnas med mer information om misslyckade inloggningen. Anteckna den **inloggning felkoden** och **felorsak**. 

    ![Välj posten](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Du kan också hitta den här informationen i den **felsökning och support** flik i informationsfönstret.

    ![Felsökning och support](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Orsaken till felet beskriver felet. I scenariot ovan orsaken till felet är exempelvis **ogiltigt användarnamn eller lösenord eller ogiltigt lokalt användarnamn eller lösenord**. Korrigeringen är att bara logga in igen med rätt användarnamn och lösenord.

7. Du kan få ytterligare information, inklusive idéer om justeringen genom att söka efter felkoden **50126** i det här exemplet i den [inloggningar referens för felkoder](reference-sign-ins-error-codes.md). 

8. Om allt annat misslyckas, eller om problemet kvarstår trots att utföra den rekommenderade erhåller [öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) följa stegen i den **felsökning och support** fliken. 

## <a name="next-steps"></a>Nästa steg

* [Referera till inloggningar felkoder](reference-sign-ins-error-codes.md)
* [Översikt över inloggningar](concept-sign-ins.md)
