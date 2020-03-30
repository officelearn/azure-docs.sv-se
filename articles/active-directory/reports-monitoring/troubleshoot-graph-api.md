---
title: Felsöka fel i Azure Active Directory-rapporterings-API | Microsoft-dokument
description: Ger dig en lösning på fel när du anropar Azure Active Directory Reporting API:er.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399288"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Felsöka fel i Azure Active Directory-rapporterings-API

I den här artikeln visas de vanliga felmeddelanden som du kan stöta på när du öppnar aktivitetsrapporter med Microsoft Graph API och steg för deras lösning.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 internt serverfel för HTTP när du öppnar Microsoft Graph V2-slutpunkten

Vi stöder för närvarande inte slutpunkten Microsoft Graph v2 – se till att komma åt aktivitetsloggarna med hjälp av slutpunkten Microsoft Graph v1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: Ingen av klienterna är B2C eller klient har inte premiumlicens

Åtkomst till inloggningsrapporter kräver en Azure Active Directory premium 1 (P1) licens. Om det här felmeddelandet visas när du öppnar inloggningar kontrollerar du att din klient är licensierad med en Azure AD P1-licens.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fel: Användaren finns inte i de tillåtna rollerna 

Om det här felmeddelandet visas när du försöker komma åt granskningsloggar eller inloggningar med API:et kontrollerar du att ditt konto är en del av rollen **Säkerhetsläsare** eller **rapportläsare** i din Azure Active Directory-klientorganisation. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: Program som saknar behörigheten AAD "Läs katalogdata" 

Följ stegen i [förutsättningarna för att komma åt Azure Active Directory-rapporterings-API:et](howto-configure-prerequisites-for-reporting-api.md) för att säkerställa att ditt program körs med rätt uppsättning behörigheter. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fel: Microsoft Graph API saknas med behörigheten Läs alla granskningsloggdata

Följ stegen i [förutsättningarna för att komma åt Azure Active Directory-rapporterings-API:et](howto-configure-prerequisites-for-reporting-api.md) för att säkerställa att ditt program körs med rätt uppsättning behörigheter. 

## <a name="next-steps"></a>Efterföljande moment

[Använda api-referensen](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
för granskning[Använd API-referensen för inloggningsaktivitetsrapporten](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
