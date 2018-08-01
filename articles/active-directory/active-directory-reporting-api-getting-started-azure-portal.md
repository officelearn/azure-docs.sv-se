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
ms.openlocfilehash: 89562e6a2bfb977585cec1925a5f306c69c3d1e8
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390678"
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

Du kan använda den [MSGraph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) att verifiera din inloggning och granska API-data. Se till att logga in på ditt konto med båda knapparna logga in i Graph Explorer-Gränssnittet och Ställ in **AuditLog.Read.All** och **Directory.Read.All** behörigheter för din klient som visas.   

![Graph-testaren](./media/active-directory-reporting-api-getting-started-azure-portal/graph-explorer.png)

![Ändra behörigheter för Användargränssnittet](./media/active-directory-reporting-api-getting-started-azure-portal/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Använd certifikat för att få åtkomst till Azure AD reporting-API 

Överväg att använda Azure AD Reporting API: et med certifikat om du planerar att hämta rapportdata utan inblandning av användaren.

Detaljerade anvisningar finns i [hämta data med hjälp av Azure AD Reporting API: et med certifikat](active-directory-reporting-api-with-certificates.md).


## <a name="explore"></a>Utforska

Få ett första intryck reporting API: er:
   
   - [Använda exemplen för granskningen API](active-directory-reporting-api-audit-samples.md) 
   - [Använda exemplen för inloggningsaktivitet rapporten API](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="next-steps"></a>Nästa steg

 * [Granska API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [Inloggningsaktivitet rapport API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Felsöka fel i Azure AD reporting API](active-directory-reporting-troubleshoot-graph-api.md)


