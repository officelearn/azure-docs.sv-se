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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 860d602ecba257ed9015d1e080e5dcb1aa5ab872
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624632"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Kom igång med Azure Active Directory reporting API

Azure Active Directory ger dig en mängd olika [rapporter](overview-reports.md), som innehåller användbar information för program, till exempel SIEM-system, granskning och business intelligence-verktyg. 

Med Microsoft Graph API för Azure AD-rapporter kan få du programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Den här artikeln ger en översikt över rapporterings-API, inklusive sätt att komma åt den.

Om du stöter på problem, se [få support för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Förutsättningar

Om du vill få åtkomst till rapporterings-API, med eller utan inblandning av användaren, måste du:

1. Tilldela roller (Security-läsare, säkerhetsadministratör, Global administratör)
2. Registrera ett program
3. Bevilja behörigheter
4. Samla in konfigurationsinställningar

Detaljerade anvisningar finns i den [krav för att få åtkomst till Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>API-slutpunkter 

Microsoft Graph API-slutpunkten för granskningsloggar är `https://graph.microsoft.com/beta/auditLogs/directoryAudits` och Microsoft Graph API-slutpunkten för inloggningar är `https://graph.microsoft.com/beta/auditLogs/signIns`. Mer information finns i den [gransknings-och API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) och [logga in API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

## <a name="apis-with-graph-explorer"></a>API: er med Graph-testaren

Du kan använda den [MSGraph explorer](https://developer.microsoft.com/graph/graph-explorer) att verifiera din inloggning och granska API-data. Se till att logga in på ditt konto med båda knapparna logga in i Graph Explorer-Gränssnittet och Ställ in **AuditLog.Read.All** och **Directory.Read.All** behörigheter för din klient som visas.   

![Graph-testaren](./media/concept-reporting-api/graph-explorer.png)

![Ändra behörigheter för Användargränssnittet](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Använd certifikat för att få åtkomst till Azure AD reporting-API 

Använda Azure AD Reporting API: et med certifikat om du planerar att hämta rapportdata utan inblandning av användaren.

Detaljerade anvisningar finns i [hämta data med hjälp av Azure AD Reporting API: et med certifikat](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Nästa steg

 * [Förutsättningar för att få åtkomst till rapporterings-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Hämta data med hjälp av Azure AD Reporting API: et med certifikat](tutorial-access-api-with-certificates.md)
 * [Felsöka fel i Azure AD reporting API](troubleshoot-graph-api.md)


