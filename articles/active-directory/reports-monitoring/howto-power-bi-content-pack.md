---
title: Så här använder du innehållspaketet för Azure Active Directory Power BI | Microsoft Docs
description: Lär dig hur du använder innehållspaketet för Azure Active Directory Power BI
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d56802e96028b6b01b6be749405c56df2648161
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988240"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Använda Azure Active Directory Power BI innehålls paketet

|  |
|--|
|För närvarande använder Azure AD Power BI-innehållspaketet Azure AD Graph-API:er för att hämta data från din Azure AD-klientorganisation. Därför kan det hända att du ser vissa skillnader mellan data som är tillgängliga i innehållspaketet och de data som hämtas med hjälp av [Microsoft Graph-API:er för rapportering](concept-reporting-api.md). |
|  |

Power BI innehålls paketet för Azure Active Directory (Azure AD) innehåller färdiga rapporter som hjälper dig att förstå hur dina användare använder och använder Azure AD-funktioner. På så sätt kan du få insyn i alla aktiviteter i din katalog med hjälp av den omfattande visualiseringen i Power BI. Du kan också skapa en egen instrument panel och dela den med vem som helst i din organisation. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure AD Premium-licens (P1/P2) för att använda innehålls paketet. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version.

## <a name="install-the-content-pack"></a>Installera innehålls paketet

Kolla in [snabb](quickstart-install-power-bi-content-pack.md) starten för att installera Azure AD Power BI-innehålls paketet.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Rapporter som ingår i den här versionen av innehålls paketet för Azure AD-loggar

Följande rapporter ingår i Azure AD Power BI-innehålls paketet. Rapporterna innehåller data från de **senaste 30 dagarna**.

**App-användning och trend rapport**:  Den här rapporten ger dig inblick i de program som används i din organisation. Du kan hämta en lista över de populäraste programmen eller förstå hur ett program som du nyligen har distribuerat i din organisation används. På så sätt kan du spåra och förbättra användningen över tid.

**Inloggningar efter plats och användare**: Den här rapporten innehåller data om alla inloggningar som utförts med Azure Identity. Med den här rapporten kan du öka detalj nivån till enskilda inloggningar och besvara frågor som:

- Vart loggade den här användaren in?
- Vilken användare har flest inloggningar och varifrån loggade användaren in? 
- Lyckades inloggningen?  
 
Du kan också filtrera resultat genom att välja ett visst datum eller en angiven plats.

**Unika användare per app**:  Den här rapporten innehåller en vy över alla unika användare som använder en specifik app. Den innehåller bara användare som har loggat in på ett program som "*lyckades*".

**Enhets inloggningar**: Den här rapporten hjälper dig att förstå de olika enhets profiler som används i din organisation och fastställa enhets principer baserat på användning. Den tillhandahåller data kring den typ av operativ system och webbläsare som används för att logga in på program, tillsammans med detaljerad information om användarna, inklusive:

- Användarnamn
- IP-adress
- Location 
- Inloggningsstatus 

**SSPR-tratt**: Den här rapporten hjälper dig att förstå hur SSPR-verktyget används i din organisation. Du kan se hur många återställningar av lösen ord som gjorts genom SSPR-verktyget och hur många av dem som lyckades. Du kan också gå djupare till fel vid återställning av lösen ord och förstå varför vissa fel har inträffat. 

## <a name="customize-azure-ad-activity-content-pack"></a>Anpassa innehålls paketet för Azure AD-aktivitet

**Ändra visualisering**:  Du kan ändra en rapport visualisering genom att klicka på **Redigera rapport** och välja den visualisering som du vill använda.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/09.png) 
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/10.png) 

**Inkludera ytterligare fält**:  Du kan lägga till ett fält i rapporten eller ta bort det genom att välja det visuella objekt som du vill lägga till/ta bort fältet i. Du kan till exempel lägga till fältet "inloggnings status" i vyn tabellvy enligt nedan. 
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/11.png) 

**Fäst visualiseringar på instrument panelen**:  Du kan anpassa instrument panelen genom att inkludera dina egna visualiseringar i rapporten och fästa den på instrument panelen. 

![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/13.png) 
 
**Dela instrument panelen**: Du kan också dela instrument panelen med användare i din organisation. När du delar rapporten kan användarna se de fält som du har valt i rapporten.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Schemalägg en daglig uppdatering av Power BI rapporten

Om du vill schemalägga en daglig uppdatering av Power BI rapporten går du till **data uppsättnings** > **Inställningar** > **Schemalägg uppdatering** och anger den enligt nedan.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Uppdatera till en nyare version av innehålls paketet

Om du vill uppdatera innehålls paketet till en nyare version:

- Hämta det nya innehålls paketet och konfigurera det med hjälp av anvisningarna i den här artikeln.

- När du har konfigurerat det går du till **data källans** > **Inställningar** > **data källans autentiseringsuppgifter** och anger dina autentiseringsuppgifter på nytt.

    ![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/16.png) 

När du har kontrollerat att den nya versionen av innehålls paketet fungerar som förväntat, kan du ta bort den gamla versionen om det behövs genom att ta bort de underliggande rapporterna och data uppsättningarna som är associerade med innehålls paketet.

## <a name="troubleshoot-content-pack-errors"></a>Felsöka innehålls paket fel

När du arbetar med innehålls paketet är det möjligt att du kör följande fel: 

- [Uppdateringen misslyckades](#refresh-failed) 
- [Det gick inte att uppdatera data källans autentiseringsuppgifter](#failed-to-update-data-source-credentials) 
- [Det tar för lång tid att importera data](#data-import-is-too-slow) 

Läs de här [hjälpartiklarna](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) för allmän hjälp med Power BI.

### <a name="refresh-failed"></a>Det gick inte att uppdatera 
 
**Så här visas felet på skärmen**: E-post från Power BI eller felaktig status i uppdaterings historiken. 


| Orsak | Så här åtgärdar du |
| ---   | ---        |
| Uppdaterings fel kan uppstå när autentiseringsuppgifterna för de användare som ansluter till innehålls paketet har återställts men inte uppdaterats i anslutnings inställningarna för innehålls paketet. | I Power BI letar du upp data uppsättningen som motsvarar instrument panelen för Azure AD-aktivitets loggar (**Azure Active Directory aktivitets loggar**), väljer Schemalägg uppdatering och anger sedan dina autentiseringsuppgifter för Azure AD. |
| En uppdatering kan inte utföras på grund av data problem i det underliggande innehålls paketet. | [Fil ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Det gick inte att uppdatera data källans autentiseringsuppgifter 
 
**Så här visas felet på skärmen**: I Power BI när du ansluter till innehålls paketet Azure AD aktivitets loggar. 

| Orsak | Så här åtgärdar du |
| ---   | ---        |
| Den anslutande användaren är inte en global administratör eller en säkerhets läsare eller en säkerhets administratör. | Använd ett konto som är antingen en global administratör eller en säkerhets läsare eller en säkerhets administratör för att komma åt innehålls paketen. |
| Din klient är inte en Premium-klient eller så har den inte minst en användare med en Premium licens fil. | [Fil ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>Data importen är för långsam 
 
**Så här visas felet på skärmen**: När du har anslutit ditt innehålls paket i Power BI börjar data import processen förbereda din instrument panel för Azure AD-aktivitets loggar. Det här meddelandet visas: **Importerar data...** utan ytterligare förlopp.  

| Orsak | Så här åtgärdar du |
| ---   | ---        |
| Beroende på klientens storlek kan det här steget ta var som helst från några minuter till 30 minuter. | Om meddelandet inte ändras till att visa din instrument panel inom en timme, [File a Support Ticket](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Nästa steg

* [Installera Power BI innehålls paket](quickstart-install-power-bi-content-pack.md).
* [Vad är Azure AD-rapporter?](overview-reports.md).
