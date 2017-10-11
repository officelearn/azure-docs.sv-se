---
title: "Felsökning av Azure Active Directory-aktivitet loggar Innehållspaketet fel | Microsoft Docs"
description: "Ger dig en lista över felmeddelanden i Azure Active Directory-aktivitet Innehållspaketet och steg för att åtgärda dem."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: c880e9eb6d48bd1e38075fbd867d3906ec67b547
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Felsökning av Azure Active Directory-aktivitet loggar Innehållspaketet fel 


När du arbetar med Power BI-Innehållspaketet för Azure Active Directory-förhandsgranskning, är det möjligt att du får följande fel: 

- [Uppdateringen misslyckades](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [Det gick inte att uppdatera datakällans autentiseringsuppgifter](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importen av data tar för lång](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Det här avsnittet ger information om möjliga orsaker och hur du korrigera felen.
 
## <a name="refresh-failed"></a>Uppdateringen misslyckades 
 
**Hur det här felet är anslutna**: e-post från Power BI- eller felstatus i uppdateringshistoriken. 


| Orsak | Hur du löser |
| ---   | ---        |
| Uppdatera fel kan bero på fel när autentiseringsuppgifterna för användare som ansluter till Innehållspaketet har återställts men inte uppdateras i anslutningsinställningarna för den av Innehållspaketet. | Leta reda på datauppsättningen motsvarar infopanelen loggar aktivitet för Azure Active Directory (Azure Active Directory aktivitetsloggar), Välj schemalägga en uppdatering ange dina autentiseringsuppgifter för Azure AD i Power BI. |
| En uppdatering kan misslyckas på grund av dataproblem med i det underliggande Innehållspaketet. | Filen ett supportärende. Mer information finns i [få support för Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Det gick inte att uppdatera datakällans autentiseringsuppgifter 
 
**Hur det här felet är anslutna**: I Power BI, när du ansluter till Innehållspaketet Azure Active Directory-aktivitet loggar (förhandsversion). 

| Orsak | Hur du löser |
| ---   | ---        |
| Den anslutande användaren är varken en global administratör eller en läsare säkerhet eller säkerhet-administratör. | Använd ett konto som är antingen en global administratör eller en läsare säkerhet eller en administratör för säkerhet för att komma åt innehållspaket. |
| Din klient är inte en Premium-klientorganisation eller har inte minst en användare med Premium-licens filen. | Filen ett supportärende. Mer information finns i [få support för Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Importen av data tar för lång 
 
**Hur det här felet är anslutna**: I Power BI när du har anslutit till ditt innehållspaket dataimporten börjar förbereda din instrumentpanel för Azure Active Directory aktivitetsloggen. Du ser meddelandet ”:*importerar data...* ”  

| Orsak | Hur du löser |
| ---   | ---        |
| Beroende på storleken på din klient kan kan det här steget ta allt från några minuter till 30 minuter. | Bara vara öppen. Om meddelandet inte ändras till visar instrumentpanelen inom en timme, kontrollera filen ett supportärende. Mer information finns i [få support för Azure Active Directory](active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Nästa steg

Om du vill installera Power BI-Innehållspaketet för Azure Active Directory-förhandsgranskning klickar du på [här](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/).


