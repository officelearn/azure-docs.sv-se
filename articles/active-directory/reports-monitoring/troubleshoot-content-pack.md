---
title: Felsöka Azure Active Directory-aktivitet loggarna fel i innehållspaket | Microsoft Docs
description: Ger dig en lista över felmeddelanden av Innehållspaketet för Azure Active Directory-aktivitet och steg för att åtgärda felen.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: eafbe25a5a0fa9182030304e9142a6013c9fb29b
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057369"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Felsöka Azure Active Directory-aktivitet loggarna fel i innehållspaket 


När du arbetar med Power BI-Innehållspaketet för Azure Active Directory-förhandsgranskning, är det möjligt att du får följande fel: 

- [Uppdateringen misslyckades](troubleshoot-content-pack.md#refresh-failed) 
- [Det gick inte att uppdatera datakällans autentiseringsuppgifter](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Import av data tar lång tid](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Den här artikeln finns information om möjliga orsaker och hur du åtgärdar de här felen.
 
## <a name="refresh-failed"></a>Uppdateringen misslyckades 
 
**Hur det här felet visas**: e-post från Power BI- eller felstatus i historiken för uppdatering. 


| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Uppdatera fel kan inträffa fel när autentiseringsuppgifterna för de användare som ansluter till Innehållspaketet har återställa men inte uppdateras i anslutningsinställningarna för Innehållspaketet. | Leta reda på datauppsättningen för instrumentpanelen för aktivitetsloggar för Azure Active Directory-aktivitet (Azure Active Directory aktivitetsloggar) i Power BI, välj Uppdatera schema och ange dina autentiseringsuppgifter för Azure AD. |
| En uppdatering kan misslyckas på grund av problem med i det underliggande Innehållspaketet. | Öppna ett supportärende. Mer information finns i [få support för Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Det gick inte att uppdatera datakällans autentiseringsuppgifter 
 
**Hur det här felet visas**: I Power BI när du ansluter till Innehållspaketet för Azure Active Directory-aktivitet loggar (förhandsversion). 

| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Den anslutande användaren är inte en global administratör eller en säkerhetsläsare eller en säkerhetsadministratör. | Använd ett konto som är antingen en global administratör eller en säkerhetsläsare eller en säkerhetsadministratör för att få åtkomst till innehållspaket. |
| Din klient är inte en Premium-klient eller har inte minst en användare med Premium-licens fil. | Öppna ett supportärende. Mer information finns i [få support för Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Import av data tar lång tid 
 
**Hur det här felet visas**: I Power BI när du har anslutit ditt innehållspaket dataimporten börjar förbereda instrumentpanelen för Azure Active Directory aktivitetsloggen. Du ser meddelandet ”:*importerar data...* ”  

| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Beroende på storleken på din klient kan kan det här steget ta allt från några minuter och 30 minuter. | Bara ha tålamod. Om meddelandet inte ändras till som visar instrumentpanelen inom en timme, skicka ett supportärende. Mer information finns i [få support för Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Nästa steg

Om du vill installera Power BI-Innehållspaketet för Azure Active Directory-förhandsgranskning klickar du på [här](https://powerbi.microsoft.com/blog/azure-active-directory-meets-power-bi/).


