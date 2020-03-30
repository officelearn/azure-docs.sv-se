---
title: Ta bort personuppgifter - Azure Active Directory Application Proxy
description: Ta bort personliga data från kopplingar som är installerade på enheter för Azure Active Directory Application Proxy.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe97956d99bd5c677e499b532ef85a1bb4d324ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275415"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Ta bort personliga data för Azure Active Directory Application Proxy

Azure Active Directory Application Proxy kräver att du installerar kopplingar på dina enheter, vilket innebär att det kan finnas personuppgifter på dina enheter. Den här artikeln innehåller steg för hur du tar bort dessa personuppgifter för att förbättra sekretessen.

## <a name="where-is-the-personal-data"></a>Var finns personuppgifterna?

Det är möjligt för Application Proxy att skriva personuppgifter till följande loggtyper:

- Kopplingshändelseloggar
- Windows-händelseloggar

## <a name="remove-personal-data-from-windows-event-logs"></a>Ta bort personliga data från Windows-händelseloggar

Information om hur du konfigurerar datakvarhållning för Windows-händelseloggar finns i [Inställningar för händelseloggar](https://technet.microsoft.com/library/cc952132.aspx). Mer information om Windows-händelseloggar finns i [Använda Windows-händelselogg](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Ta bort personliga data från kopplingshändelseloggar

För att säkerställa att application proxy-loggarna inte har personuppgifter kan du antingen:

- Ta bort eller visa data när det behövs, eller
- Inaktivera loggning

Använd följande avsnitt för att ta bort personliga data från anslutningshändelseloggar. Du måste slutföra borttagningsprocessen för alla enheter där anslutningen är installerad.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Visa eller exportera specifika data

Om du vill visa eller exportera specifika data söker du efter relaterade poster i var och en av kopplingshändelseloggarna. Loggarna finns på `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`.

Eftersom loggarna är textfiler kan du använda [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) för att söka efter textposter som är relaterade till en användare.  

Sök efter användardata för att hitta personuppgifter.

Om du vill hitta personuppgifter som loggas av ett program som använder Kerberos Constrained Delegation söker du efter följande komponenter i användarnamnstypen:

- Lokalt användarnamn
- Användarnamn del av användarens huvudnamn
- Användarnamn del av lokala användarens huvudnamn
- Sam-kontonamn (On-premises security accounts manager)

### <a name="delete-specific-data"></a>Ta bort specifika data

Så här tar du bort specifika data:

1. Starta om Tjänsten Microsoft Azure AD Application Proxy Connector för att generera en ny loggfil. Med den nya loggfilen kan du ta bort eller ändra de gamla loggfilerna. 
1. Följ den [visa eller exportera specifika dataprocess](#view-or-export-specific-data) som beskrivits tidigare för att hitta information som måste tas bort. Sök i alla anslutningsloggar.
1. Ta antingen bort relevanta loggfiler eller ta selektivt bort de fält som innehåller personuppgifter. Du kan också ta bort alla gamla loggfiler om du inte behöver dem längre.

### <a name="turn-off-connector-logs"></a>Inaktivera anslutningsloggar

Ett alternativ för att säkerställa att anslutningsloggarna inte innehåller personuppgifter är att stänga av logggenereringen. Om du vill sluta generera kopplingsloggar `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`tar du bort följande markerade linje från .

![Visar ett kodavsnitt med den markerade koden som ska tas bort](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Nästa steg

En översikt över Programproxy finns i [Så här ger du säker fjärråtkomst till lokala program](application-proxy.md).