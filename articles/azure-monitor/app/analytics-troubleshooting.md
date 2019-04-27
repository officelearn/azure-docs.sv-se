---
title: Felsökningsanalys i Azure Application Insights | Microsoft Docs
description: 'Problem med Application Insights analytics? Börja här. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: mbullwin
ms.openlocfilehash: ecf0638aa999208331603ac30ccf4eb17b3c4500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60692388"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Felsökningsanalys i Application Insights
Problem med [Application Insights Analytics](analytics.md)? Börja här. Analytics är verktyget kraftfull sökning av Azure Application Insights.

## <a name="limits"></a>Limits
* För närvarande är frågeresultat begränsade till bara under en vecka senaste data.
* Webbläsare som vi testa på: senaste versionerna av Chrome, Microsoft Edge och Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Kända inkompatibla webbläsartillägg
* Ghostery

Inaktivera tillägget eller Använd en annan webbläsare.

## <a name="e-a"></a> ”Oväntat fel”
![Ett oväntat felskärmen](media/analytics-troubleshooting/010.png)

Internt fel inträffade under portal runtime ohanterat undantag.

* Rensa webbläsarens cache.

## <a name="e-b"></a>403 ... Försök att läsa in
![403 ... Försök att läsa in](media/analytics-troubleshooting/020.png)

Ett autentiseringsrelaterat fel har inträffat (under autentisering eller under generering av åtkomsttoken). Portalen kanske inte går att återställa utan att ändra inställningar för webbläsaren.

* Kontrollera [cookies från tredje part är aktiverade](#cookies) i webbläsaren. 

## <a name="authentication"></a>403 ... Kontrollera säkerhetszon
![403 ...verify säkerhetszon](media/analytics-troubleshooting/030.png)

Ett autentiseringsrelaterat fel har inträffat (under autentisering eller under generering av åtkomsttoken). Portalen kanske inte går att återställa utan att ändra inställningar för webbläsaren.

1. Kontrollera [cookies från tredje part är aktiverade](#cookies) i webbläsaren. 
2. Använde du en favorit, ett bokmärke eller en sparad länk för att öppna Analytics-portalen? Är du inloggad med andra autentiseringsuppgifter än de du använde när du sparade länken?
3. Försök att använda webbläsarfönstret i läget privat/inkognito (när du har stängt alla sådana fönster). Du måste ange dina autentiseringsuppgifter. 
4. Öppna ett nytt (vanligt) webbläsarfönster och gå till [Azure](https://portal.azure.com). Logga ut. Öppna sedan länken och logga in med rätt autentiseringsuppgifter.
5. Microsoft Edge och Internet Explorer-användare kan också få det här felet om betrodda Zoninställningar inte stöds.
   
    Kontrollera att båda [analysportalen](https://portal.azure.com) och [Azure Active Directory-portalen](https://portal.azure.com) finns i samma säkerhetszon:
   
   * I Internet Explorer, öppna **Internetalternativ**, **Security**, **betrodda platser**, **platser**:
     
     ![Dialogrutan Internetalternativ, att lägga till en plats i tillförlitliga platser](media/analytics-troubleshooting/033.png)
     
     I listan över webbplatser kontrollerar du om någon av följande URL:er finns med. Se i så fall till att även de andra finns med:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Resursen hittades inte
![404... Det gick inte att hitta resursen](media/analytics-troubleshooting/040.png)

Programresursen har tagits bort från Application Insights och är inte tillgänglig längre. Detta kan inträffa om du har sparat URL: en till analyssidan.

## <a name="e-e"></a>403 ... Ingen auktorisering
![403 ... inte behörighet](media/analytics-troubleshooting/050.png)

Du har inte behörighet att öppna det här programmet i Analytics.

* Fick du länken från någon annan? Be dem att kontrollera att du är i den [läsare eller deltagare för den här resursgruppen](../../azure-monitor/app/resources-roles-access-control.md).
* Sparades den länken med olika autentiseringsuppgifter? Öppna den [Azure-portalen](https://portal.azure.com), logga ut och försök sedan den här länken med rätt autentiseringsuppgifter.

## <a name="html-storage"></a>403 ... HTML5-lagring
Vår portal använder HTML5 localStorage och sessionStorage.

* Chrome: Inställningar, sekretess, innehållsinställningarna.
* Internet Explorer: Internet-alternativ, avancerade fliken säkerhet, aktivera DOM-lagring

![403 ... försöker aktivera HTML5-lagring](media/analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Prenumerationen hittades inte
![404... Prenumerationen hittades inte](media/analytics-troubleshooting/070.png)

URL: en är ogiltig. 

* Öppna appen resursen i [Application Insights-portalen](https://portal.azure.com). Använd sedan knappen Analytics.

## <a name="e-h"></a>404 ... sidan finns inte
![404... Sidan finns inte](media/analytics-troubleshooting/080.png)

URL: en är ogiltig.

* Öppna appen resursen i [Application Insights-portalen](https://portal.azure.com). Använd sedan knappen Analytics.

## <a name="cookies"></a>Aktivera cookies från tredje part
  Se [inaktivera cookies från tredje part](https://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), men Observera att vi behöver **aktivera** dem.


[!INCLUDE [app-insights-analytics-footer](../../../includes/app-insights-analytics-footer.md)]

