---
title: Användningen och insikter för rapporten i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till användningen och insikter för rapporten i Azure Active Directory-portalen
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
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806363"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Rapport om användningen och insikter i Azure Active Directory-portalen

Med hjälp av rapporten användningen och insikter får du en programcentrerad vy över dina data för inloggning. Du kan hitta svar på följande frågor:

*   Vad finns på överst används program i min organisation?
*   Vilka program har flest misslyckade inloggningar? 
*   Vilka är de viktigaste inloggning felen för varje program?

## <a name="prerequisites"></a>Nödvändiga komponenter 

Om du vill komma åt data i rapporten användningen och insikter, behöver du:

* En Azure AD-klient
* En Azure AD premium (P1/P2)-licens att visa data för inloggning
* En användare i global administratör, säkerhetsadministratör, säkerhetsläsare eller rapporten läsare roller. Dessutom kan alla användare (icke-administratörer) kan komma åt sina egna inloggningar. 

## <a name="access-the-usage-and-insights-report"></a>Komma åt rapporten användningen och insikter

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj rätt katalog och sedan **Azure Active Directory** och välj **företagsprogram**.
3. Från den **aktivitet** väljer **användning och insikter** att öppna rapporten. 

![Användningen och insikter rapport](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Använda rapporten

Användningen och insikter rapporten visar en lista över program med en eller flera tecken i försök och gör att du kan sortera efter antal genomförda inloggningar, misslyckade inloggningar och frekvensen.

Klicka på Läs in fler längst ned i listan kan du visa ytterligare program på sidan. Du kan välja datumintervall att visa alla program som har använts i intervallet.

Du kan också ange fokus på ett visst program. Välj **visa inloggningsaktivitet** Se tecknet i aktiviteten med tiden för programmet, samt de viktigaste felen.  

När du väljer en dag i programmet Se Användningsdiagram, få en detaljerad lista över inloggningsaktiviteterna för programmet.  

![Användningen och insikter rapport](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Nästa steg

* [Rapporten inloggningar](concept-sign-ins.md)