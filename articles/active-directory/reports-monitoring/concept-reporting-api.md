---
title: 'Komma igång med Azure AD-rapporterings-API: et | Microsoft-dokument'
description: Komma igång med Azure Active Directory-rapporterings-API:et
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f240a5191dd483f89889f3ffe13b1819ca1e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399324"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Komma igång med Azure Active Directory-rapporterings-API:et

Azure Active Directory ger dig en mängd olika [rapporter](overview-reports.md)som innehåller användbar information för program som SIEM-system, granskning och business intelligence-verktyg. 

Genom att använda Microsoft Graph API för Azure AD-rapporter kan du få programmatisk åtkomst till data via en uppsättning REST-baserade API:er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Den här artikeln ger dig en översikt över rapporterings-API:et, inklusive olika sätt att komma åt det.

Om du stöter på problem kan du se [hur du får support för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Krav

För att komma åt rapporterings-API:et, med eller utan användarens ingripande, måste du:

1. Tilldela roller (säkerhetsläsare, säkerhetsadministratör, global administratör)
2. Registrera ett program
3. Bevilja behörigheter
4. Samla in konfigurationsinställningar

Detaljerade instruktioner finns i [förutsättningarna för att komma åt Azure Active Directory-rapporterings-API:et](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>API-slutpunkter 

Slutpunkten för Microsoft Graph API `https://graph.microsoft.com/beta/auditLogs/directoryAudits` för granskningsloggar är och Microsoft `https://graph.microsoft.com/beta/auditLogs/signIns`Graph API-slutpunkten för inloggningar är . Mer information finns i [referensen för gransknings-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) och [inloggnings-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Dessutom kan du använda [API:et för identitetsskyddsriskidentifiering för](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) att få programmatisk åtkomst till säkerhetsidentifieringar med Hjälp av Microsoft Graph. Mer information finns i [Komma igång med Azure Active Directory Identity Protection och Microsoft Graph](../identity-protection/graph-get-started.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>API:er med Utforskaren i Microsoft Graph

Du kan använda [Utforskaren](https://developer.microsoft.com/graph/graph-explorer) i Microsoft Graph för att verifiera dina inloggnings- och gransknings-API-data. Se till att logga in på ditt konto med båda inloggningsknapparna i Graph Explorer-användargränssnittet och ange behörigheter för **AuditLog.Read.All** och **Directory.Read.All** för din klientorganisation enligt bilden.   

![Graph-testaren](./media/concept-reporting-api/graph-explorer.png)

![Användargränssnitt för ändring av behörigheter](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Använda certifikat för att komma åt Azure AD-rapporterings-API:et 

Använd Azure AD Reporting API med certifikat om du planerar att hämta rapportdata utan att användaren behöver göra något.

Detaljerade instruktioner finns i [Hämta data med Azure AD Reporting API med certifikat](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Nästa steg

 * [Förutsättningar för åtkomst till rapporterings-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Hämta data med hjälp av Azure AD Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
 * [Felsöka fel i Azure AD-rapporterings-API](troubleshoot-graph-api.md)


