---
title: 'Felsöka fel i Azure Active Directory reporting API: N | Microsoft Docs'
description: 'Ger dig en lösning till fel vid anrop av Azure Active Directory Reporting API: er.'
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
ms.openlocfilehash: 8b517204fb650020bdebf8172186f30fff58f722
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285029"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Felsöka fel i Azure Active Directory reporting API

Den här artikeln innehåller vanliga felmeddelanden som kan uppstå vid åtkomst till rapporter med MS Graph API och stegen för sin lösning.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 http-internt serverfel vid åtkomst till Microsoft Graph V2-slutpunkten

Vi stöder för närvarande inte v2-slutpunkten för Microsoft Graph – se till att få åtkomst till aktivitetsloggar som använder Microsoft Graph v1-slutpunkten.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Fel: Det gick inte att hämta användarroller från AD Graph

Du kan få detta felmeddelande när du försöker komma åt inloggningar med Graph Explorer. Kontrollera att du har loggat in till ditt konto med båda knapparna logga in i Gränssnittet Graph Explorer enligt följande bild. 

![Graph-testaren](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Fel: Det gick inte att göra premium licenskontrollen från AD Graph 

Om du får detta felmeddelande när du försöker komma åt inloggningar med Graph Explorer, Välj **behörighet att ändra** under ditt konto i det vänstra navigeringsfältet och väljer **Tasks.ReadWrite** och **Directory.Read.All**. 

![Ändra behörigheter för Användargränssnittet](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: Varken klienten är B2C eller klienten har inte premium-licens

Åtkomst till inloggning rapporter kräver en Azure Active Directory premium 1 (P1) licens. Om du ser det här felmeddelandet vid åtkomst till inloggningar, se till att din klient är licensierad med Azure Active Directory P1-licens.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fel: Användaren är inte tillåtna roller 

Om du ser det här felmeddelandet när du försöker komma åt granskningsloggar eller inloggningar med API: et, se till att ditt konto är en del av den **Säkerhetsläsare** eller **rapportläsare** roll i Azure Active Directory innehavaren. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: Program som saknar behörighet för AAD-läsa kataloginformation 

Följ stegen i den [förutsättningar för att få åtkomst till Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md) så att programmet körs med rätt uppsättning behörigheter. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Fel: Programmet MSGraph API läsa alla gransknings-och loggdata behörighet som saknas

Följ stegen i den [förutsättningar för att få åtkomst till Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md) så att programmet körs med rätt uppsättning behörigheter. 

## <a name="next-steps"></a>Nästa steg

[Använda granskningsloggarna API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[använda inloggningsaktivitet rapport API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
