---
title: Så här använder du innehållspaketet för Azure Active Directory Power BI | Microsoft Docs
description: Lär dig hur du använder innehållspaketet för Azure Active Directory Power BI
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d810b8be496bbfd1c5bd88d8221e77dd5b76c889
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622981"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Hur du använder Azure Active Directory Power BI-innehållspaket

|  |
|--|
|För närvarande använder Azure AD Power BI-innehållspaketet Azure AD Graph-API:er för att hämta data från din Azure AD-klientorganisation. Därför kan det hända att du ser vissa skillnader mellan data som är tillgängliga i innehållspaketet och de data som hämtas med hjälp av [Microsoft Graph-API:er för rapportering](concept-reporting-api.md). |
|  |

Power BI content pack för Azure Active Directory (Azure AD) innehåller fördefinierade rapporter som hjälper dig att förstå hur dina användare införa och använda Azure AD-funktioner. På så sätt kan du få insyn i alla aktiviteter i din katalog med hjälp av den omfattande visualiseringsupplevelsen i Power BI. Du kan också skapa en egen instrumentpanel och dela den med vem som helst i din organisation. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure AD premium (P1/P2) licens att använda Innehållspaketet. 

## <a name="install-the-content-pack"></a>Installera Innehållspaketet

Kolla in den [snabbstarten](quickstart-install-power-bi-content-pack.md) att installera Azure AD Power BI-Innehållspaketet.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Rapporter som ingår i den här versionen av Azure AD-loggar Innehållspaketet

Följande rapporter ingår i Azure AD Power BI-Innehållspaketet. Rapporterna innehåller data från den **senaste 30 dagarna**.

**Användning och trender rapporten**: den här rapporten ger dig insikt i de program som används i din organisation. Du kan hämta en lista över de mest populära programmen eller förstå hur ett program som du nyligen distribuerade i din organisation används. På så sätt kan du spåra och förbättra användning över tid.

**Inloggningar efter plats och användare**: den här rapporten innehåller data om alla inloggningar utförs med hjälp av Azure Identity. Med den här rapporten kan du öka detaljnivån till enskilda inloggningar och besvara frågor som:

- Där den här användaren logga in från?
- Vilken användare har flest inloggningar och varifrån loggade användaren in? 
- Lyckades inloggningen?  
 
Du kan också filtrera resultaten genom att välja ett visst datum eller plats.

**Unika användare per app**: den här rapporten ger en överblick över alla unika användare som använder en viss app. Den innehåller endast användare som har ”*har*” loggat in på ett program.

**Enhetsinloggningar**: den här rapporten hjälper dig att förstå de olika enhetsprofiler som används inom organisationen och bestämma principer för enheter baserat på användning. Den tillhandahåller data om vilken typ av operativsystem och webbläsare som används för att logga in på program, tillsammans med detaljerad information om användarna som omfattar:

- Användarnamn
- IP-adress
- Plats 
- Inloggningsstatus 

**SSPR-tratt**: den här rapporten hjälper dig att förstå hur SSPR-verktyget används inom organisationen. Du kan visa hur många lösenordsåterställningar som försökte utföras via SSPR-verktyget och hur många av dem lyckades. Du kan också gå på djupet återställer Lösenordsfel och förstå varför vissa fel uppstod. 

## <a name="customize-azure-ad-activity-content-pack"></a>Anpassa Innehållspaketet för Azure AD-aktivitet

**Ändra visualisering**: Du kan ändra ett visuellt objekt i en rapport genom att klicka på **Redigera rapport** och välja det önskade visuella objektet.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/09.png) 
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/10.png) 

**Ta med ytterligare fält**: Du kan lägga till ett fält i rapporten eller ta bort det genom att välja det visuella objekt som du vill lägga till/ta bort från fältet. Du kan till exempel lägga till fältet ”inloggningsstatus” i tabellvyn enligt nedan. 
 
![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/11.png) 

**Fästa visualiseringar på instrumentpanelen**: du kan anpassa din instrumentpanel genom att inkludera egna visuella objekt i rapporten och fästa den på instrumentpanelen. 

![Innehållspaketet för Azure Active Directory Power BI](./media/howto-power-bi-content-pack/13.png) 
 
**Dela instrumentpanelen**: du kan också dela instrumentpanelen med användare i din organisation. När du delar rapporten kan kan användarna se de fält du har valt i rapporten.
 
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

## <a name="still-having-issues"></a>Har du fortfarande problem? 

Läs vår [felsökningsguide](troubleshoot-content-pack.md). Läs de här [hjälpartiklarna](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) för allmän hjälp med Power BI.
 
 
## <a name="next-steps"></a>Nästa steg

* [Installera Power BI-Innehållspaketet](quickstart-install-power-bi-content-pack.md).
* [Felsöka fel i innehållspaket](troubleshoot-content-pack.md).
* [Vad är Azure AD-rapporter? ](overview-reports.md).
