---
title: Vad är nytt i Power BI-arbetsytesamlingar?
description: Hämta den senaste informationen om nyheter i Power BI-Arbetsytesamlingar
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b29996b8e2cd9a95b9c1566036b1009b65c18bfa
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518073"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Vad är nytt i Power BI-arbetsytesamlingar?

Uppdaterar till **Power BI-Arbetsytesamlingar** släpps regelbundet. Men innehåller inte alla versionen nya funktioner för användarinriktade; vissa utgåvor av fokuserar på funktioner för backend-tjänst. Vi fokusera på nya användarinriktade funktioner här.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Mars 2017

**Självbetjäning**

* [Skapa ny rapport](create-report-from-dataset.md)
* [Spara rapporten](save-reports.md)
* Bädda in rapporten i Läs/redigera/skapa nytt läge 
* [Växla mellan lägen för redigering/läsning](toggle-mode.md)

**Dataanslutning med REST API: er**

* [Skapa datauppsättning](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Skicka data 

**Management-API: er**

* Klona rapport och datauppsättning
* Binda rapporten till en annan datauppsättning

**Exempel**

* Uppdatera [rapport för JavaScript-Inbäddningsexempel](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>December 2016

* [Ny JavaScript-inbäddningsexempel](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Oktober 2016

* [Avancerad analys med Power BI-Arbetsytesamlingar och R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>Den 31 augusti 2016
Ingår i den här versionen:

* Alla nya JavaScript SDK som stöder [avancerad filtrering och sidan navigering](interact-with-reports.md).
* Power BI-Arbetsytesamlingar stöds nu i Kanada, centrala datacenter. Kontrollera [datacenter status](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 juli 2016
Ingår i den här versionen:

* **Goda nyheter!** Tjänsten Power BI-Arbetsytesamlingar är inte längre i förhandsgranskning – dess nu allmänt tillgänglig (allmänt tillgänglig).  
* Alla REST API: er har flyttats från **/beta** till **/v1.0**.
* .NET- och JavaScript-SDK: er har uppdaterats för **v1.0**.
* Power BI-API-anrop kan nu autentiseras direkt med hjälp av API-nycklar. Apptoken krävs endast för inbäddning. Etablera och dev-token har tagits bort från v1.0 API: er som en del av detta, men de kommer att fortsätta att arbeta i beta-versionen till den 30 December 2016. Mer information finns i [autentisering och auktorisering med Power BI-Arbetsytesamlingar](app-token-flow.md).
* Rad på radnivå (RLS) stöd för apptoken och inbäddade rapporter. Mer information finns i [säkerhet på radnivå med Power BI-Arbetsytesamlingar](row-level-security.md).
* Uppdatera exempelprogrammet för alla **v1.0** API-anrop.
* Power BI-Arbetsytesamlingar stöd för Azure SDK, PowerShell och CLI.
* Användare kan exportera data för sökvägsvisualisering till en **.csv**.
* Power BI-Arbetsytesamlingar stöds nu i alla de samma/språk som Microsoft Azure. Mer information finns i [Azure – språk](https://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

