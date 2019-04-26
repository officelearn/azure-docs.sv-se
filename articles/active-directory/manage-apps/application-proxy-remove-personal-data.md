---
title: Ta bort personliga data – Azure Active Directory Application Proxy | Microsoft Docs
description: Ta bort personliga data från kopplingarna är installerad på enheter för Azure Active Directory Application Proxy.
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5f3c1883f156562cfab59cb102fb0cf18b03803
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292883"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Ta bort personliga data för Azure Active Directory Application Proxy  

Azure Active Directory Application Proxy kräver att du installerar kopplingar på dina enheter, vilket innebär att det kan finnas personliga data på dina enheter. Den här artikeln innehåller anvisningar att ta bort dessa personliga data för att förbättra sekretess. 


## <a name="where-is-the-personal-data"></a>Var finns personliga data?
Det är möjligt för Application Proxy att skriva personliga data till följande loggtyper av:

- Händelseloggar för anslutningen
- Windows-händelseloggar

## <a name="remove-personal-data-from-windows-event-logs"></a>Ta bort personliga data från Windows-händelseloggar

Information om hur du konfigurerar datakvarhållning för Windows-händelseloggar finns i [inställningar för händelseloggar](https://technet.microsoft.com/library/cc952132.aspx). Mer information om Windows-händelseloggar, se [med hjälp av Windows-händelseloggen](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Ta bort personliga data från anslutningen händelseloggar

För att säkerställa Application Proxy-loggar inte har personliga data, kan du antingen:

- Ta bort eller visa data vid behov, eller
- Inaktivera loggning

Använd följande avsnitt för att ta bort personliga data från händelseloggarna för anslutningen. Du måste slutföra borttagningen för alla enheter där anslutningen är installerad.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Visa eller exportera specifika data

Om du vill visa eller exportera specifika data, Sök efter relaterade poster i var och en av händelseloggar för anslutningen. Loggarna finns på `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Eftersom loggarna är textfiler, du kan använda [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) att söka efter textposter som relaterar till en användare.  

Sök loggfiler för användar-ID för att hitta personliga data. 

Sök efter de här komponenterna i anger du användarnamnet för att hitta personliga data som loggats av ett program som använder Kerberos-begränsad delegering:

- Lokala användarens huvudnamn
- Användaren är del av användarens huvudnamn
- Användarnamnet är del av den lokala användarens huvudnamn
- Lokala konton kontosäkerhet (SAM) kontonamnet 


### <a name="delete-specific-data"></a>Ta bort specifika data

Ta bort specifika data:

1. Starta om tjänsten Microsoft Azure AD Application Proxy Connector för att generera en ny loggfil skapas. Ny loggfil kan du ta bort eller ändra de gamla loggfilerna. 
2. Följ den [vy eller export specifika data](#view-or-export-specific-data) processen som beskrivs ovan för att hitta information som måste tas bort. Sök alla connector-loggar.
3. Ta bort relevanta loggfiler eller selektivt ta bort de fält som innehåller personliga data. Du kan också ta bort alla gamla loggfiler om du inte längre behövs.

### <a name="turn-off-connector-logs"></a>Inaktivera connector-loggar

Ett alternativ till att kontrollera connector-loggar inte innehåller personliga data är att inaktivera kontot log. Stoppa genererar connector-loggarna genom att ta bort följande markerade rad från `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Konfiguration](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Nästa steg

En översikt över Application Proxy, se [att tillhandahålla säker fjärråtkomst till lokala program](application-proxy.md).

