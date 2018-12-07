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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 65747da92a3cad770cd9d474d27645782f7306b9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998743"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Felsöka Azure Active Directory-aktivitet loggarna fel i innehållspaket 

|  |
|--|
|För närvarande använder Azure AD Power BI-innehållspaketet Azure AD Graph-API:er för att hämta data från din Azure AD-klientorganisation. Därför kan det hända att du ser vissa skillnader mellan data som är tillgängliga i innehållspaketet och de data som hämtas med hjälp av [Microsoft Graph-API:er för rapportering](concept-reporting-api.md). |
|  |

När du arbetar med Power BI-Innehållspaketet för Azure Active Directory (AD Azure), är det möjligt att du får följande fel: 

- [Uppdateringen misslyckades](troubleshoot-content-pack.md#refresh-failed) 
- [Det gick inte att uppdatera datakällans autentiseringsuppgifter](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Import av data tar lång tid](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 

Den här artikeln finns information om möjliga orsaker och hur du åtgärdar de här felen.
 
## <a name="refresh-failed"></a>Uppdateringen misslyckades 
 
**Hur det här felet visas**: e-post från Power BI- eller felstatus i historiken för uppdatering. 


| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Uppdatera fel kan inträffa fel när autentiseringsuppgifterna för de användare som ansluter till Innehållspaketet har återställa men inte uppdateras i anslutningsinställningarna för Innehållspaketet. | Leta upp datauppsättningen för instrumentpanelen för aktivitetsloggar i Azure AD i Power BI (**Azure Active Directory-aktivitetsloggar**), välj Uppdatera schema och ange dina autentiseringsuppgifter för Azure AD. |
| En uppdatering kan misslyckas på grund av problem med i det underliggande Innehållspaketet. | [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Det gick inte att uppdatera datakällans autentiseringsuppgifter 
 
**Hur det här felet visas**: I Power BI när du ansluter till Azure AD-aktiviteten loggar Innehållspaketet. 

| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Den anslutande användaren är inte en global administratör eller en säkerhetsläsare eller en säkerhetsadministratör. | Använd ett konto som är antingen en global administratör eller en säkerhetsläsare eller en administratör för att få åtkomst till innehållspaket. |
| Din klient är inte en Premium-klient eller har inte minst en användare med Premium-licens fil. | [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Importera data är för långsam 
 
**Hur det här felet visas**: I Power BI när du har anslutit ditt innehållspaket dataimporten börjar förbereda instrumentpanelen för Azure AD activity logs. Du ser meddelandet: **import av data...**  utan någon ytterligare pågår.  

| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Beroende på storleken på din klient kan kan det här steget ta allt från några minuter och 30 minuter. | Om meddelandet inte ändras till som visar instrumentpanelen inom en timme [öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Nästa steg

* [Installera Power BI contect pack för Azure AD-rapporter](quickstart-install-power-bi-content-pack.md).
* [Använda Power BI content pack för Azure AD-rapporter för att visualisera dina data](howto-power-bi-content-pack.md)
* [Få support för Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
