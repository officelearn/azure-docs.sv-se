---
title: 'Kom igång med Azure AD reporting API: et | Microsoft Docs'
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
ms.openlocfilehash: 93532f4b0b2d527a4d5c79e2ee1b2810394b2f11
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442091"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Kom igång med Azure Active Directory reporting API

Azure Active Directory ger dig en mängd olika [rapporter](active-directory-reporting-azure-portal.md). Data för de här rapporterna kan vara användbara för dina program, till exempel SIEM-system, gransknings- och business intelligence-verktyg. 

Med hjälp av Azure AD reporting API kan få du programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Den här artikeln innehåller en översikt över för att få åtkomst till rapporteringsdata med relaterade API: et.

Om du stöter på problem, se [få support för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).


## <a name="prerequisites"></a>Förutsättningar

Om du vill komma åt rapporterings-API, även om du planerar att komma åt API med hjälp av ett skript, måste du:

1. Tilldela roller (Security-läsare, säkerhetsadministratör, Global administratör)
2. Registrera ett program
3. Bevilja behörigheter
4. Samla in konfigurationsinställningar


 
Detaljerade anvisningar finns i den [krav för att få åtkomst till Azure Active Directory reporting API](active-directory-reporting-api-prerequisites-azure-portal.md).

## <a name="apis-with-graph-explorer"></a>API: er med Graph-testaren

Du kan använda den [MSGraph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) att verifiera din inloggning och granska API-data. Se till att logga in på ditt konto med båda knapparna logga in i Graph Explorer-Gränssnittet och Ställ in **Tasks.ReadWrite** och **Directory.ReadAll** behörigheter för din klient som visas.   

![Graph-testaren](./media/active-directory-reporting-api-getting-started-azure-portal/graph-explorer.png)

![Ändra behörigheter för Användargränssnittet](./media/active-directory-reporting-api-getting-started-azure-portal/modify-permissions.png)

## <a name="recommendation"></a>Rekommendation 

Om du planerar om hur du hämtar rapporteringsdata utan inblandning av användaren, bör du använda Azure AD Reporting API: et med certifikat.

Detaljerade anvisningar finns i [hämta data med hjälp av Azure AD Reporting API: et med certifikat](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Utforska

Få ett första intryck reporting API: er:
   
   - [Använda exemplen för granskningen API](active-directory-reporting-api-audit-samples.md) 
 
   - [Använda exemplen för inloggningsaktivitet rapporten API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>Anpassa  

Skapa en egen lösning: 
   
   - [Med hjälp av audit API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [Med inloggningsaktivitet rapport API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



