---
title: BNPR i Azure Active Directory application proxy | Microsoft Docs
description: Läs mer om BNPR i Azure Active Directory-programproxy.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a3df15743b4918d72fac5f8769a2d3ee721a452f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>BNPR i Azure Active Directory application proxy  

Azure Active Directory (Azure AD) Application Proxy är BNPR-kompatibel tillsammans med andra Microsoft-tjänster och funktioner. Mer information om Microsofts BNPR support finns [licensvillkor och dokumentation](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Eftersom den här funktionen innehåller kopplingar på dina datorer, finns det några händelser som du behöver övervaka för att hålla BNPR som är kompatibla. Programproxy skapar följande log-typer som kan innehålla EUII:

- Kopplingen händelseloggar
- Windows-händelseloggar

Det finns två sätt att hålla BNPR kompatibla:

- Ta bort och exportera begäranden när de uppstår

- Inaktivera loggning

För:

- Information om hur du konfigurerar datalagring för Windows-händelseloggar finns i [inställningar för händelseloggar](https://technet.microsoft.com/library/cc952132.aspx). 
- Allmän information om Windows-händelseloggen finns [med hjälp av Windows-händelseloggen](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).


Du kan hitta Anslut händelseloggar vid `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. Följande avsnitt innehåller relaterade steg för koppling händelseloggar. Du måste slutföra dessa steg på alla datorer i kopplingen.
 

## <a name="processing-requests"></a>Bearbetning av begäranden

Det finns tre olika typer av begäranden som du är ansvarig för: 

- Ta bort
- Visa
- Exportera
 
Om du vill bearbeta vy / exportera begäranden som du behöver gå igenom loggfiler för att söka efter relaterade poster. 

Eftersom loggarna är textfiler, du kan söka efter, till exempel med hjälp av den `findstr` kommando för att hitta poster som är relaterade till dina användare. Sök efter följande fält eftersom de kan vara i loggarna: 

- UserId
- Användarnamnet som konfigurerats för alla program som använder Kerberos-begränsad delegering:
    - User Principal Name
    - Lokala användarens huvudnamn
    - Användaren är del av användarens huvudnamn
    - Användarnamnet är del av den lokala användarens huvudnamn
    - Lokalt SAM-kontonamn 

 
Du kan samla in de här fälten och dela dem med användaren.
Du måste ta bort relevanta loggar för att bearbeta delete-begäranden. Du kan starta om kopplingstjänsten (Microsoft Azure AD Application Proxy Connector) att skapa en ny loggfil. Ny loggfil kan du ta bort de gamla loggfilerna. Du kan följa processen för att visa / exportera för att hitta alla relevanta loggar och selektivt ta bort dessa fält eller filer. Du kan också alltid bara ta bort alla gamla loggfiler om du inte längre behövs.


## <a name="turn-off-connector-logs"></a>Stäng av connector-loggar

Om du vill inaktivera connector-loggarna som du behöver justera `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` genom att ta bort den markerade raden för: 


![Konfiguration](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>Nästa steg

En översikt över Azure AD-programproxy finns [ge säker fjärråtkomst till lokala program](manage-apps/application-proxy.md).

