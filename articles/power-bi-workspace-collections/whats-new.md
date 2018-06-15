---
title: Vad är nytt i Power BI arbetsytan samlingar
description: Få den senaste informationen om vad är nytt i Power BI arbetsytan samlingar
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 647b571fc05dca12ab9d4db45236962e4794ef4b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408259"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Vad är nytt i Power BI arbetsytan samlingar

Uppdateringar för **Power BI arbetsytan samlingar** släpps regelbundet. Dock innehåller inte alla viktig nya funktioner för användarinriktad; vissa versioner fokuserar på funktioner för backend-tjänst. Vi Markera nya användarinriktad funktioner här.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Mars 2017

**Självbetjäning funktioner**

* [Skapa ny rapport](create-report-from-dataset.md)
* [Spara rapporten](save-reports.md)
* Bädda in rapporten i Läs/redigera/skapa nya läge 
* [Växla mellan lägen redigera/läsning](toggle-mode.md)

**Dataanslutning med REST API: er**

* [Skapa datauppsättningen](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Skicka data 

**Management-API: er**

* Klona rapporten och datamängd
* Binda rapporten till en annan datauppsättning

**Exempel**

* Uppdatera [JavaScript rapporten bäddas in exempel](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>December 2016

* [Ny JavaScript bädda in exempel](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Oktober 2016

* [Avancerade analyser med Power BI arbetsytan samlingar och R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>Den 31 augusti 2016
Ingår i den här versionen:

* Alla nya JavaScript SDK som stöder [avancerade filtrering och sidan navigering](interact-with-reports.md).
* Power BI arbetsytan samlingar stöds nu i Kanada Central datacentret. Kontrollera [datacenter status](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 juli 2016
Ingår i den här versionen:

* **Goda nyheter!** Arbetsytan samlingar för Power BI-tjänsten är inte längre i preview - dess nu GA (allmänt tillgänglig).  
* Alla REST API: er har flyttats från **/beta** till **/v1.0**.
* .NET- och JavaScript SDK har uppdaterats för **v1.0**.
* Power BI-API-anrop kan nu autentiseras direkt med hjälp av API-nycklar. Apptoken krävs endast för att bädda in. Etablera och dev token har tagits bort v1.0 API: er som en del av detta, men de kommer att fortsätta att arbeta i betaversionen till 30 December 2016. Läs mer i [autentisera och auktorisera med Power BI arbetsytan samlingar](app-token-flow.md).
* Raden säkerhet på radnivå (RLS) stöd för apptoken och inbäddade rapporter. Läs mer i [rad säkerhet på radnivå med Power BI arbetsytan samlingar](row-level-security.md).
* Uppdatera exempelprogram för alla **v1.0** API-anrop.
* Power BI arbetsytan samlingar stöd för Azure SDK, PowerShell och CLI.
* Användare kan exportera visualiseringen data till en **.csv**.
* Power BI arbetsytan samlingar stöds nu i alla de samma/språk som Microsoft Azure. Läs mer i [Azure - språk](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

