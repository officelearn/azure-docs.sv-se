---
title: Kom igång med Azure AD reporting API | Microsoft Docs
description: Hur du kommer igång med Azure Active Directory reporting API
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 842e4d8413544a303b656a8e4a05bdf58a7c8164
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223849"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Kom igång med Azure Active Directory reporting API

Azure Active Directory ger dig en mängd olika [rapporter](active-directory-reporting-azure-portal.md). Data för de här rapporterna kan vara användbara för dina program, till exempel SIEM-system, gransknings- och business intelligence-verktyg. 

Du kan få programmatisk åtkomst till data via en uppsättning REST-baserad API: er med hjälp av Azure AD reporting API. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Den här artikeln innehåller en översikt över för att komma åt rapporteringsdata med relaterade API.

Om du stöter på problem, se [få support för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Förutsättningar

Om du vill komma åt reporting API, även om du planerar att få åtkomst till API: et med ett skript, måste du:

1. Tilldela roller (Security Reader säkerhet Admin Global administratör)
2. Registrera ett program
3. Bevilja behörigheter
4. Samla in konfigurationsinställningar


 
Detaljerade instruktioner finns i [krav för att få åtkomst till Azure Active Directory reporting API](active-directory-reporting-api-prerequisites-azure-portal.md).


## <a name="recommendation"></a>Rekommendation 

Om du planerar att hämta reporting data utan åtgärder från användaren, bör du använda Azure AD Reporting API med certifikat.

Detaljerade instruktioner finns [hämta data med hjälp av Azure AD Reporting API med certifikat](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Utforska

Hämta en första intryck reporting API:
   
   - [Använda exemplen för granskning API](active-directory-reporting-api-audit-samples.md) 
 
   - [Använda exemplen för rapporten inloggningsaktivitet API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Anpassa  

Skapa din egen lösning: 
   
   - [Med hjälp av audit API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Med hjälp av rapporten API-referens inloggningsaktivitet](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



