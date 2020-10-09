---
title: Användnings-och insikts rapport | Microsoft Docs
description: Introduktion till användnings-och insikts rapport i Azure Active Directory portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "74008266"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Användnings-och insikts rapport i Azure Active Directorys portalen

Med hjälp av rapporten användning och insikter kan du få en programinriktad vy över dina inloggnings data. Du kan hitta svar på följande frågor:

*   Vilka är de mest använda programmen i min organisation?
*   Vilka program har de mest misslyckade inloggningarna? 
*   Vilka är de vanligaste inloggnings felen för varje program?

## <a name="prerequisites"></a>Krav 

Om du vill komma åt data från rapporten användning och insikter behöver du:

* En Azure AD-klient
* En Azure AD Premium-licens (P1/P2) som visar inloggnings data
* En användare i rollerna global administratör, säkerhets administratör, säkerhets läsare eller rapport läsare. Dessutom kan alla användare (icke-administratörer) komma åt sina egna inloggningar. 

## <a name="access-the-usage-and-insights-report"></a>Öppna rapporten användning och insikter

1. Navigera till [Azure Portal](https://portal.azure.com).
2. Välj rätt katalog, Välj **Azure Active Directory** och välj **företags program**.
3. I avsnittet **aktivitet** väljer du **användning & insikter** för att öppna rapporten. 

![Rapport om användning och insikter](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Använda rapporten

I rapporten användning och insikter visas en lista med program med ett eller flera inloggnings försök, och du kan sortera efter antalet lyckade inloggningar, misslyckade inloggningar och antalet lyckade inloggningar.

Om du klickar på Läs in mer längst ned i listan kan du Visa fler program på sidan. Du kan välja datum intervall för att visa alla program som har använts inom intervallet.

Du kan också ange fokus för ett angivet program. Välj **Visa inloggnings aktivitet** för att se inloggnings aktiviteten över tid för programmet och de vanligaste felen.  

När du väljer en dag i program användnings diagrammet visas en detaljerad lista över inloggnings aktiviteterna för programmet.  

![Rapport om användning och insikter](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Nästa steg

* [Rapport över inloggningar](concept-sign-ins.md)