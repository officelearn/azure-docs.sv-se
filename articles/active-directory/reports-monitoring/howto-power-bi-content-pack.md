---
title: Så här använder du innehållspaketet för Azure Active Directory Power BI | Microsoft Docs
description: Lär dig hur du använder innehållspaketet för Azure Active Directory Power BI
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 934562147fedcc81b16fd1ad2534af5662ef4b78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437702"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Hur du använder Azure Active Directory Power BI-innehållspaket

|  |
|--|
|För närvarande använder Azure AD Power BI-innehållspaketet Azure AD Graph-API:er för att hämta data från din Azure AD-klientorganisation. Därför kan det hända att du ser vissa skillnader mellan data som är tillgängliga i innehållspaketet och de data som hämtas med hjälp av [Microsoft Graph-API:er för rapportering](concept-reporting-api.md). |
|  |

Power BI content pack för Azure Active Directory (Azure AD) innehåller fördefinierade rapporter som hjälper dig att förstå hur dina användare införa och använda Azure AD-funktioner. På så sätt kan du få insyn i alla aktiviteter i din katalog med hjälp av den omfattande visualiseringsupplevelsen i Power BI. Du kan också skapa en egen instrumentpanel och dela den med vem som helst i din organisation. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Azure AD premium (P1/P2) licens att använda Innehållspaketet. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version.

## <a name="install-the-content-pack"></a>Installera Innehållspaketet

Kolla in den [snabbstarten](quickstart-install-power-bi-content-pack.md) att installera Azure AD Power BI-Innehållspaketet.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Rapporter som ingår i den här versionen av Azure AD-loggar Innehållspaketet

Följande rapporter ingår i Azure AD Power BI-Innehållspaketet. Rapporterna innehåller data från den **senaste 30 dagarna**.

**Användning och trender rapporten**:  Den här rapporten ger dig insikt i de program som används i din organisation. Du kan hämta en lista över de mest populära programmen eller förstå hur ett program som du nyligen distribuerade i din organisation används. På så sätt kan du spåra och förbättra användning över tid.

**Inloggningar efter plats och användare**: Den här rapporten innehåller data om alla inloggningar utförs med hjälp av Azure Identity. Med den här rapporten kan du öka detaljnivån till enskilda inloggningar och besvara frågor som:

- Där den här användaren logga in från?
- Vilken användare har flest inloggningar och varifrån loggade användaren in? 
- Lyckades inloggningen?  
 
Du kan också filtrera resultaten genom att välja ett visst datum eller plats.

**Unika användare per app**:  Den här rapporten ger en överblick över alla unika användare som använder en viss app. Den innehåller endast användare som har ”*har*” loggat in på ett program.

**Enhetsinloggningar**: Den här rapporten hjälper dig att förstå de olika enhetsprofiler som används inom organisationen och bestämma principer för enheter baserat på användning. Den tillhandahåller data om vilken typ av operativsystem och webbläsare som används för att logga in på program, tillsammans med detaljerad information om användarna som omfattar:

- Användarnamn
- IP-adress
- Location 
- Inloggningsstatus 

**SSPR-tratt**: Den här rapporten hjälper dig att förstå hur SSPR-verktyget används inom organisationen. Du kan visa hur många lösenordsåterställningar som försökte utföras via SSPR-verktyget och hur många av dem lyckades. Du kan också gå på djupet återställer Lösenordsfel och förstå varför vissa fel uppstod. 

## <a name="customize-azure-ad-activity-content-pack"></a>Anpassa Innehållspaketet för Azure AD-aktivitet

**Ändra visualisering**:  Du kan ändra ett visuellt objekt genom att klicka på **Redigera rapport** och välj visualiseringen som du vill.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/09.png) 
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/10.png) 

**Ta med ytterligare fält**:  Du kan lägga till ett fält i rapporten eller ta bort den genom att välja det visuella objektet som du vill lägga till/ta bort fältet. Du kan till exempel lägga till fältet ”inloggningsstatus” i tabellvyn enligt nedan. 
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/11.png) 

**Fästa visualiseringar på instrumentpanelen**:  Du kan anpassa din instrumentpanel genom att inkludera egna visuella objekt i rapporten och fästa den på instrumentpanelen. 

![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/13.png) 
 
**Dela instrumentpanelen**: Du kan också dela instrumentpanelen med användare i din organisation. När du delar rapporten kan kan användarna se de fält du har valt i rapporten.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Schemalägga en daglig uppdatering av Power BI-rapport

Om du vill schemalägga en daglig uppdatering av Power BI-rapport, gå till **datauppsättningar** > **inställningar** > **Schemalägg uppdatering** och konfigurerar det enligt nedan.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Uppdatera till nyare version av Innehållspaketet

Om du vill uppdatera ditt innehållspaket till en nyare version:

- Ladda ned det nya Innehållspaketet och konfigurera den med hjälp av anvisningarna i den här artikeln.

- När du har ställt in går du till **datakälla** > **inställningar** > **datakällans autentiseringsuppgifter** och ange dina autentiseringsuppgifter igen.

    ![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/16.png) 

När du har kontrollerat att den nya versionen av Innehållspaketet fungerar som förväntat kan du ta bort den gamla versionen vid behov genom att ta bort den underliggande rapporter och datauppsättningar som är associerade med Innehållspaketet.

## <a name="troubleshoot-content-pack-errors"></a>Felsöka fel i innehållspaket

När du arbetar med Innehållspaketet, är det möjligt att du får följande fel: 

- [Uppdateringen misslyckades](#refresh-failed) 
- [Det gick inte att uppdatera datakällans autentiseringsuppgifter](#failed-to-update-data-source-credentials) 
- [Import av data tar lång tid](#data-import-is-too-slow) 

Läs de här [hjälpartiklarna](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) för allmän hjälp med Power BI.

### <a name="refresh-failed"></a>Det gick inte att uppdatera 
 
**Hur det här felet visas**: E-post från Power BI- eller felstatus i historiken för uppdatering. 


| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Uppdatera fel kan inträffa fel när autentiseringsuppgifterna för de användare som ansluter till Innehållspaketet har återställa men inte uppdateras i anslutningsinställningarna för Innehållspaketet. | Leta upp datauppsättningen för instrumentpanelen för aktivitetsloggar i Azure AD i Power BI (**Azure Active Directory-aktivitetsloggar**), välj Uppdatera schema och ange dina autentiseringsuppgifter för Azure AD. |
| En uppdatering kan misslyckas på grund av problem med i det underliggande Innehållspaketet. | [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Det gick inte att uppdatera datakällans autentiseringsuppgifter 
 
**Hur det här felet visas**: I Power BI, när du ansluter till Azure AD-aktivitet loggar Innehållspaketet. 

| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Den anslutande användaren är inte en global administratör eller en säkerhetsläsare eller en säkerhetsadministratör. | Använd ett konto som är antingen en global administratör eller en säkerhetsläsare eller en administratör för att få åtkomst till innehållspaket. |
| Din klient är inte en Premium-klient eller har inte minst en användare med Premium-licens fil. | [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>Importera data är för långsam 
 
**Hur det här felet visas**: I Power BI när du har anslutit ditt innehållspaket dataimporten börjar att förbereda din instrumentpanel för Azure AD activity logs. Det här meddelandet visas: **Importerar data...**  utan någon ytterligare pågår.  

| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Beroende på storleken på din klient kan kan det här steget ta allt från några minuter och 30 minuter. | Om meddelandet inte ändras till som visar instrumentpanelen inom en timme [öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Nästa steg

* [Installera Power BI-Innehållspaketet](quickstart-install-power-bi-content-pack.md).
* [Vad är Azure AD-rapporter? ](overview-reports.md).
