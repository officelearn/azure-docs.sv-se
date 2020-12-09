---
title: Kom igång med Azure AD repor ting API | Microsoft Docs
description: 'Komma igång med API: et för Azure Active Directory rapportering'
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
ms.openlocfilehash: af448d5d28c5add39176500b988824b17f85b9fb
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861977"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Kom igång med API: et för Azure Active Directory rapportering

Azure Active Directory ger dig en rad olika [rapporter](overview-reports.md)som innehåller användbar information för program som SIEM system, granskning och Business Intelligence verktyg. 

Genom att använda Microsoft Graph-API för Azure AD-rapporter kan du få program mässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Den här artikeln ger en översikt över rapporterings-API: t, inklusive sätt att komma åt den.

Om du stöter på problem, se [hur du får support för Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).

## <a name="prerequisites"></a>Krav

Om du vill få åtkomst till rapporterings-API: et, med eller utan åtgärder från användaren, måste du:

1. Tilldela roller (säkerhets läsare, säkerhets administratör, global administratör)
2. Registrera ett program
3. Bevilja behörigheter
4. Samla in konfigurations inställningar

Detaljerade anvisningar finns i [krav för att komma åt Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>API-slutpunkter 

Microsoft Graph API-slutpunkten för gransknings loggar är `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` och Microsoft Graph-API-slutpunkten för inloggningar är `https://graph.microsoft.com/v1.0/auditLogs/signIns` . Mer information finns i API- [referens för granskning](/graph/api/resources/directoryaudit) och [INLOGGNINGs-API-referens](/graph/api/resources/signIn).

Dessutom kan du använda [identitets skyddets identifierings-API](/graph/api/resources/identityriskevent?view=graph-rest-beta) för att få program mässig åtkomst till säkerhets identifieringar med hjälp av Microsoft Graph. Mer information finns i [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>API: er med Microsoft Graph Explorer

Du kan använda [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) för att verifiera dina inloggnings-och gransknings-API-data. Var noga med att logga in på ditt konto med hjälp av båda inloggnings knapparna i graphs användar gränssnitt och ange **AuditLog. Read. all** och **Directory. Read. alla** behörigheter för din klient organisation som visas.   

![Graph-testaren](./media/concept-reporting-api/graph-explorer.png)

![Ändra behörighets gränssnitt](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Använda certifikat för att få åtkomst till Azure AD repor ting API 

Använd Azure AD repor ting-API: et med certifikat om du planerar att hämta rapporterings data utan åtgärder från användaren.

Detaljerade anvisningar finns i [Hämta data med hjälp av Azure AD repor ting API med certifikat](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Nästa steg

 * [Krav för att få åtkomst till rapporterings-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Hämta data med hjälp av Azure AD Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
 * [Felsöka fel i Azure AD repor ting API](troubleshoot-graph-api.md)
