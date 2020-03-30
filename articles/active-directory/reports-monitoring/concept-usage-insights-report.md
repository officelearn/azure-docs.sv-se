---
title: Rapport om användning och insikter | Microsoft-dokument
description: Introduktion till rapporten användning och insikter i Azure Active Directory-portalen
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008266"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Rapport om användning och insikter i Azure Active Directory-portalen

Med rapporten användning och insikter kan du få en programcentrerad vy över dina inloggningsdata. Du hittar svar på följande frågor:

*   Vilka är de mest använda programmen i min organisation?
*   Vilka program har de mest misslyckade inloggningarna? 
*   Vilka är de vanligaste inloggningsfelen för varje program?

## <a name="prerequisites"></a>Krav 

För att komma åt data från användnings- och insiktsrapporten behöver du:

* En Azure AD-klient
* En Azure AD-premiumlicens (P1/P2) för att visa inloggningsdata
* En användare i den globala administratören, säkerhetsadministratören, säkerhetsläsaren eller rapportläsarrollerna. Dessutom kan alla användare (icke-administratörer) komma åt sina egna inloggningar. 

## <a name="access-the-usage-and-insights-report"></a>Få tillgång till rapporten användning och insikter

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj rätt katalog och välj sedan **Azure Active Directory** och välj **Enterprise-program**.
3. I avsnittet **Aktivitet** väljer du **Användning & insikter** för att öppna rapporten. 

![Rapport om användning och insikter](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Använda rapporten

I rapporten Användning och insikter visas listan över program med ett eller flera inloggningsförsök och du kan sortera efter antalet lyckade inloggningar, misslyckade inloggningar och framgångsfrekvensen.

Om du klickar på Läs in mer längst ned i listan kan du visa ytterligare program på sidan. Du kan välja datumintervallet för att visa alla program som har använts inom intervallet.

Du kan också ställa in fokus på ett visst program. Välj **Visa inloggningsaktivitet** om du vill visa inloggningsaktiviteten över tid för programmet samt de vanligaste felen.  

När du väljer en dag i programanvändningsdiagrammet får du en detaljerad lista över inloggningsaktiviteterna för programmet.  

![Rapport om användning och insikter](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Nästa steg

* [Rapport över inloggningar](concept-sign-ins.md)