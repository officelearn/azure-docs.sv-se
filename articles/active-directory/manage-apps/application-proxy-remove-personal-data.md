---
title: Ta bort personliga data - Azure Active Directory Application Proxy | Microsoft Docs
description: Ta bort personliga data från kopplingarna är installerad på enheter för Azure Active Directory Application Proxy.
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 94cf0464aca3c46e0c6425b0fb3e24fcd767f95c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655332"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Ta bort personliga data för Azure Active Directory Application Proxy  

Azure Active Directory Application Proxy kräver att du installerar kopplingar på dina enheter, vilket innebär att det kan vara personliga data på dina enheter. Den här artikeln innehåller anvisningar att ta bort den personliga informationen för att förbättra sekretess. 


## <a name="where-is-the-personal-data"></a>Var finns personliga data?
Det är möjligt för Application Proxy skriva personliga data till följande i loggen:

- Kopplingen händelseloggar
- Windows-händelseloggar

## <a name="remove-personal-data-from-windows-event-logs"></a>Ta bort personliga data från Windows-händelseloggar

Information om hur du konfigurerar datalagring för Windows-händelseloggar finns [inställningar för händelseloggar](https://technet.microsoft.com/library/cc952132.aspx). Mer information om Windows-händelseloggar, se [med hjälp av Windows-händelseloggen](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Ta bort personliga data från anslutningen händelseloggar

För att säkerställa Application Proxy-loggar inte har personliga data, kan du antingen:

- Ta bort eller visa data vid behov, eller
- Inaktivera loggning

Använd följande avsnitt för att ta bort personliga data från händelseloggarna för anslutningen. Du måste slutföra borttagningen för alla enheter där kopplingen är installerad.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Visa eller exportera specifika data

Om du vill visa eller exportera specifika data, söker du efter relaterade poster i alla händelseloggar för anslutningen. Loggarna finns i `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Eftersom loggarna är textfiler, kan du använda [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) att söka efter textposter som är relaterade till en användare.  

Sök loggfiler för användar-ID för att hitta personliga data. 

Sök efter de här komponenterna av typen Användarnamn för att hitta personliga data som loggats av ett program som använder Kerberos-begränsad delegering:

- Lokala användarens huvudnamn
- Användaren är del av användarens huvudnamn
- Användarnamnet är del av den lokala användarens huvudnamn
- Lokala konton manager (SAM) kontonamnet 


### <a name="delete-specific-data"></a>Ta bort specifika data

Ta bort specifika data:

1. Starta om tjänsten Microsoft Azure AD Application Proxy Connector för att generera en ny loggfil. Ny loggfil kan du ta bort eller ändra de gamla loggfilerna. 
2. Följ den [vy eller export specifika data](#view-or-export-specific-data) process som beskrivs ovan för att hitta information som behöver tas bort. Söka i alla connector-loggarna.
3. Ta bort de relevanta loggfilerna eller selektivt ta bort de fält som innehåller personuppgifter. Du kan också ta bort alla gamla loggfiler om du inte längre behövs.

### <a name="turn-off-connector-logs"></a>Stäng av connector-loggar

Ett alternativ till att kontrollera connector-loggarna innehåller inga personliga data är att inaktivera genereringen av loggen. Stoppa generering connector-loggarna genom att ta bort den markerade raden för följande från `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Konfiguration](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Nästa steg

En översikt över Webbprogramproxy, se [ge säker fjärråtkomst till lokala program](application-proxy.md).

