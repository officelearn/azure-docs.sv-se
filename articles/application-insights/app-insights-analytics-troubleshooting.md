---
title: Felsöka analyser i Azure Application Insights | Microsoft Docs
description: 'Problem med Application Insights analytics? Börja här. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: c479298187cfc4af6d9b33d9bd3839e8fa579566
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294167"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Felsökningsanalys i Application Insights
Problem med [Application Insights Analytics](app-insights-analytics.md)? Börja här. Analytics är ett kraftfullt sökverktyg av Azure Application Insights.

## <a name="limits"></a>Begränsningar
* För närvarande är frågeresultat begränsade till bara via en vecka med senaste data.
* Webbläsare som vi testa på: senaste versionerna av Chrome, kant och Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Kända inkompatibla webbläsartillägg
* Ghostery

Inaktivera tillägget eller Använd en annan webbläsare.

## <a name="e-a"></a> ”Fel”
![Oväntat felskärmen](./media/app-insights-analytics-troubleshooting/010.png)

Internt fel inträffade under portal runtime-undantag.

* Rensa webbläsarens cacheminne. 

## <a name="e-b"></a>403 ... Försök att läsa in
![403 ... Försök att läsa in](./media/app-insights-analytics-troubleshooting/020.png)

Ett autentiseringsrelaterat fel har inträffat (under autentisering eller under generering av åtkomsttoken). Portalen har kanske ingen möjlighet att återställa utan att ändra inställningarna för webbläsaren.

* Kontrollera [cookies från tredje part är aktiverade](#cookies) i webbläsaren. 

## <a name="authentication"></a>403 ... Kontrollera säkerhetszon
![403 ...verify säkerhetszon](./media/app-insights-analytics-troubleshooting/030.png)

Ett autentiseringsrelaterat fel har inträffat (under autentisering eller under generering av åtkomsttoken). Portalen har kanske ingen möjlighet att återställa utan att ändra inställningarna för webbläsaren.

1. Kontrollera [cookies från tredje part är aktiverade](#cookies) i webbläsaren. 
2. Använde du en favorit, ett bokmärke eller en sparad länk för att öppna Analytics-portalen? Är du inloggad med andra autentiseringsuppgifter än de du använde när du sparade länken?
3. Försök att använda webbläsarfönstret i läget privat/inkognito (när du har stängt alla sådana fönster). Du måste ange dina autentiseringsuppgifter. 
4. Öppna ett nytt webbläsarfönster som (vanlig) och gå till [Azure](https://portal.azure.com). Logga ut. Öppna sedan länken och logga in med rätt autentiseringsuppgifter.
5. Microsoft Edge- och Internet Explorer-användare kan också få det här felet om betrodda zoninställningar inte stöds.
   
    Kontrollera att båda [Analytics-portalen](https://analytics.applicationinsights.io) och [Azure Active Directory-portalen](https://portal.azure.com) finns i samma säkerhetszon:
   
   * I Internet Explorer, öppna **Internetalternativ**, **säkerhet**, **tillförlitliga platser**, **platser**:
     
     ![Dialogrutan Internetalternativ, lägger till en plats i betrodda platser](./media/app-insights-analytics-troubleshooting/033.png)
     
     I listan över webbplatser kontrollerar du om någon av följande URL:er finns med. Se i så fall till att även de andra finns med:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Resursen hittades inte
![404... resursen hittades inte](./media/app-insights-analytics-troubleshooting/040.png)

Programresursen har tagits bort från Application Insights och är inte tillgänglig längre. Detta kan inträffa om du har sparat Webbadressen till sidan Analytics.

## <a name="e-e"></a>403 ... Ingen åtkomstbehörighet för
![403 ... inte behörighet](./media/app-insights-analytics-troubleshooting/050.png)

Du har inte behörighet att öppna det här programmet i Analytics.

* Fick du länken från någon annan? Be dem att se till att du befinner dig i den [läsare eller deltagare för den här resursgruppen](app-insights-resources-roles-access-control.md).
* Sparades länken med andra autentiseringsuppgifter? Öppna den [Azure-portalen](https://portal.azure.com), logga ut och försök sedan den här länken anger rätt autentiseringsuppgifter.

## <a name="html-storage"></a>403 ... HTML5-lagring
Vår portal använder HTML5 localStorage och sessionStorage.

* Chrome: Inställningar, sekretess, inställningar för innehåll.
* Internet Explorer: Internet-alternativ, fliken Avancerat, säkerhet, aktivera DOM-lagring

![403 ... försöker aktivera HTML5-lagring](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Det gick inte att hitta prenumerationen
![404... Det gick inte att hitta prenumerationen](./media/app-insights-analytics-troubleshooting/070.png)

URL: en är ogiltig. 

* Öppna app-resurs i [Application Insights-portalen](https://portal.azure.com). Använd sedan knappen Analytics.

## <a name="e-h"></a>404 ... sidan finns inte
![404... Sidan finns inte](./media/app-insights-analytics-troubleshooting/080.png)

URL: en är ogiltig.

* Öppna app-resurs i [Application Insights-portalen](https://portal.azure.com). Använd sedan knappen Analytics.

## <a name="cookies"></a>Aktivera cookies från tredje part
  Se [inaktivera cookies från tredje part](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), men vi behöver meddelande **aktivera** dem.


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

