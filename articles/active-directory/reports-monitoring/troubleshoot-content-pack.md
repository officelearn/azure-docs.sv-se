---
title: Felsöka Azure Active Directory aktivitets loggar fel i innehålls paketet | Microsoft Docs
description: Innehåller en lista med fel meddelanden för Azure Active Directory aktivitets innehålls paketet och stegen för att åtgärda dem.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a0a5b5306414eb50a1928ec8a1854f56055681
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987908"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Felsöka Azure Active Directory aktivitets loggar fel i innehålls paketet 

|  |
|--|
|För närvarande använder Azure AD Power BI-innehållspaketet Azure AD Graph-API:er för att hämta data från din Azure AD-klientorganisation. Därför kan det hända att du ser vissa skillnader mellan data som är tillgängliga i innehållspaketet och de data som hämtas med hjälp av [Microsoft Graph-API:er för rapportering](concept-reporting-api.md). |
|  |

När du arbetar med Power BI innehålls paketet för Azure Active Directory (Azure AD) är det möjligt att du kör följande fel: 

- [Uppdateringen misslyckades](troubleshoot-content-pack.md#refresh-failed) 
- [Det gick inte att uppdatera data källans autentiseringsuppgifter](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Det tar för lång tid att importera data](#data-import-is-too-slow) 

Den här artikeln innehåller information om möjliga orsaker och hur du åtgärdar felen.
 
## <a name="refresh-failed"></a>Det gick inte att uppdatera 
 
**Så här visas felet på skärmen**: E-post från Power BI eller felaktig status i uppdaterings historiken. 


| Orsak | Så här åtgärdar du |
| ---   | ---        |
| Uppdaterings fel kan uppstå när autentiseringsuppgifterna för de användare som ansluter till innehålls paketet har återställts men inte uppdaterats i anslutnings inställningarna för innehålls paketet. | I Power BI letar du upp data uppsättningen som motsvarar instrument panelen för Azure AD-aktivitets loggar (**Azure Active Directory aktivitets loggar**), väljer Schemalägg uppdatering och anger sedan dina autentiseringsuppgifter för Azure AD. |
| En uppdatering kan inte utföras på grund av stora data mängder. | Azure AD-innehålls paketet med Power BI kan för närvarande endast stödja små data uppsättningar (mindre än 500, 00 rader) på grund av vissa begränsningar kring timeout i Power BIs tjänsten. Om du stöter på fel eller om uppdateringen Miss lyckas på grund av timeout-problem kan det bero på att du försöker hämta en stor data uppsättning. Minska tids perioden i frågan och försök igen.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Det gick inte att uppdatera data källans autentiseringsuppgifter 
 
**Så här visas felet på skärmen**: I Power BI när du ansluter till innehålls paketet Azure AD aktivitets loggar. 

| Orsak | Så här åtgärdar du |
| ---   | ---        |
| Den anslutande användaren är inte en global administratör eller en säkerhets läsare eller en säkerhets administratör. | Använd ett konto som är antingen en global administratör eller en säkerhets läsare eller en säkerhets administratör för att komma åt innehålls paketen. |
| Din klient är inte en Premium-klient eller så har den inte minst en användare med en Premium licens fil. | [Fil ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Data importen är för långsam 
 
**Så här visas felet på skärmen**: När du har anslutit ditt innehålls paket i Power BI börjar data import processen förbereda din instrument panel för Azure AD-aktivitets loggar. Det här meddelandet visas: **Importerar data...** utan ytterligare förlopp.  

| Orsak | Så här åtgärdar du |
| ---   | ---        |
| Beroende på klientens storlek kan det här steget ta var som helst från några minuter till 30 minuter. | Om meddelandet inte ändras till att visa din instrument panel inom en timme, [File a Support Ticket](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Nästa steg

* [Installera Power BI Content Pack för Azure AD-rapporter](quickstart-install-power-bi-content-pack.md).
* [Använd Power BI innehålls paket för Azure AD-rapporter för att visualisera dina data](howto-power-bi-content-pack.md)
* [Få support för Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
