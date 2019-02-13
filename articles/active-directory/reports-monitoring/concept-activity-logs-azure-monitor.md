---
title: Azure Active Directory-aktivitetsloggar i Azure Monitor (förhandsversion) | Microsoft Docs
description: Introduktion till Azure Active Directory-loggarna i Azure Monitor (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e533f3db8a9d40ee062e65d96fa9bf33a366e3a8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206363"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Azure AD-aktivitetsloggar i Azure Monitor (förhandsversion)

Du kan nu vidarebefordra aktivitetsloggar i Azure Active Directory (AD Azure) till flera slutpunkter för länge termen kvarhållning och data insikter. Den offentliga förhandsversionen av Azure AD-loggar i Azure Monitor kan du:

* Arkivera Azure AD-aktivitetsloggar till ett Azure storage-konto att behålla data under en längre tid.
* Stream Azure AD aktivitetsloggar till en Azure event hub för analys, med hjälp av populära verktyg för säkerhetsinformation och händelsehantering (SIEM), till exempel Splunk och QRadar.
* Integrera Azure AD-aktivitetsloggar med dina egna anpassade loggen lösningar genom att strömma dem till en händelsehubb.
* Skicka Azure AD-aktivitetsloggar till Log Analytics för att aktivera kraftfulla visualiseringar, övervakning och avisering för den anslutna data.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Rapporter som stöds

Du kan dirigera Azure AD granska loggar och logga in med loggar på din Azure storage-konto, händelsehubb, Log Analytics eller anpassad lösning med hjälp av den här funktionen. 

* **Granskningsloggar**: Den [granskningsloggar](concept-audit-logs.md) ger dig tillgång till historiken för varje aktivitet som utförs i din klient.
* **Logga in loggar**: Med den [inloggningsaktivitet rapporten](concept-sign-ins.md), du kan bestämma vem som utfört de uppgifter som rapporteras i granskningsloggarna.

> [!NOTE]
> B2C-relaterade spårnings- och inloggningsaktivitetsloggar stöds inte just nu.
>

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* [Licens](https://azure.microsoft.com/pricing/details/active-directory/) för Azure AD Free, Basic, Premium 1 eller Premium 2 för åtkomst till Azure AD-granskningsloggar på Azure Portal. 
* En Azure AD-klientorganisation.
* En användare som är **global administratör** eller **säkerhetsadministratör** för Azure AD-klientorganisationen.
* [Licens](https://azure.microsoft.com/pricing/details/active-directory/) för Azure AD Premium 1 eller Premium 2 för åtkomst till Azure AD-inloggningsloggar på Azure Portal. 

Beroende på vart du vill dirigera spårningsloggdata behöver du något av följande:

* Ett Azure-lagringskonto som du har *ListKeys*-behörigheter för. Vi rekommenderar att du använder ett allmänt lagringskonto och inte ett blob-lagringskonto. Prisinformation för lagring finns i [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* En Azure Event Hubs-namnrymd för att integrera med lösningar från tredje part.
* En Azure Log Analytics-arbetsyta för att skicka loggar till Log Analytics.

## <a name="cost-considerations"></a>Kostnadsöverväganden

Om du redan har en Azure AD-licens behöver du en Azure-prenumeration för att konfigurera lagringskontot och händelsehubben. Azure-prenumerationen är kostnadsfri, men du behöver betala för att använda Azure-resurser, inklusive det lagringskonto som du använder för arkivering och den händelsehubb som du använder för strömning. Mängden data och därmed kostnaden kan variera avsevärt beroende på storleken på klientorganisationen. 

### <a name="storage-size-for-activity-logs"></a>Lagringsstorlek för aktivitetsloggar

Varje spårningslogghändelse använder cirka 2 KB datalagring. I en klientorganisation med 100 000 användare skulle det ske ungefär 1,5 miljoner händelser per dag, vilket skulle kräva ungefär 3 GB datalagring per dag. Eftersom skrivningar sker i batchar om cirka fem minuter kan du förvänta dig ungefär 9 000 skrivåtgärder per månad. 

Följande tabell innehåller en uppskattning av kostnaden, beroende på klientorganisationens storlek, för ett GPv2-lagringskonto i västra USA för minst ett års kvarhållning. För att skapa en mer tillförlitlig uppskattning av den datavolym som du förväntar dig att du behöver för programmet använder du [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). 

| Loggkategori | Antal användare | Händelser per dag | Datavolym per månad (uppskattad) | Kostnad per månad (uppskattad) | Kostnad per år (uppskattad) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Granska | 100 000 | 1,5&nbsp;miljoner | 90 GB | 1,93 USD | 23,12 USD |
| Granska | 1,000 | 15 000 | 900 MB | 0,02 USD | 0,24 USD |
| Inloggningar | 1,000 | 34 800 | 4 GB | 0,13 USD | 1,56 USD |
| Inloggningar | 100 000 | 15&nbsp;miljoner | 1,7 TB | 35,41 USD | 424,92 USD | 


### <a name="event-hub-messages-for-activity-logs"></a>Händelsehubbmeddelanden för aktivitetsloggar

Händelser batchas i intervall på ungefär fem minuter och skickas som ett enskilt meddelande som innehåller alla händelser inom den tidsramen. Ett meddelande i en händelsehubb har en maximal storlek på 256 KB, och om den totala storleken på alla meddelanden inom tidsramen överstiger den volymen skickas flera meddelanden. 

Exempelvis inträffar normalt cirka 18 händelser per sekund för en stor klientorganisation med fler än 100 000 användare, vilket är en hastighet som motsvarar 5 400 händelser var femte minut. Eftersom spårningsloggar är cirka 2 KB per händelse motsvarar detta 10,8 MB data. Därför skickas 43 meddelanden till händelsehubben i det femminutsintervallet. 

Följande tabell innehåller uppskattad kostnad per månad för en grundläggande händelsehubb i västra USA, beroende på mängden händelsedata. För att beräkna en tillförlitlig uppskattning av den datavolym som du förväntar dig att du behöver för programmet använder du [priskalkylatorn för händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/).

| Loggkategori | Antal användare | Händelser per sekund | Händelser per femminutsintervall | Volym per intervall | Meddelanden per intervall | Meddelanden per månad | Kostnad per månad (uppskattad) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Granska | 100 000 | 18 | 5 400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Granska | 1,000 | 0,1 | 52 | 104 KB | 1 | 8 640 | 10,80 USD |
| Inloggningar | 1,000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 USD |  

### <a name="log-analytics-cost-considerations"></a>Log Analytics kostnadsöverväganden

Kostnaderna för att hantera Log Analytics-arbetsytan finns [hantera kostnader genom att kontrollera datavolymer och kvarhållning i Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Det här avsnittet innehåller svar på vanliga frågor och beskriver kända problem med Azure AD-loggar i Azure Monitor.

**F: Vilka loggar ingår?**

**A**: Logga in aktivitetsloggar och granskningsloggar är du båda att dirigeras via den här funktionen, även om B2C-relaterade granskningshändelser inte ingår för närvarande. Om du vill ta reda på vilka typer av loggar och vilka funktionsbaserade loggar som stöds för närvarande kan du läsa [schemat för spårningslogg](reference-azure-monitor-audit-log-schema.md) och [schemat för inloggningslogg](reference-azure-monitor-sign-ins-log-schema.md). 

---

**F: Hur strax efter en åtgärd motsvarande loggarna visas i min event hub?**

**A**: Loggarna visas i din event hub inom två till fem minuter efter att åtgärden utförs. Mer information om Event Hubs finns i [Vad är Azure Event Hubs?](../../event-hubs/event-hubs-about.md).

---

**F: Hur strax efter en åtgärd motsvarande loggarna visas i mitt lagringskonto?**

**A**: För Azure storage-konton är svarstiden var som helst från 5 till 15 minuter efter att åtgärden utförs.

---

**F: Vad händer om en administratör ändrar kvarhållningsperioden för en diagnostikinställning?**

**A**: Den nya kvarhållningsprincipen kommer att gälla loggar som samlats in efter ändringen. Insamlade loggar innan principändringen påverkas.

---

**F: Hur mycket kostar det för att lagra Mina data?**

**A**: Kostnader för lagring är beroende av både storleken på dina loggar och kvarhållningsperioden som du väljer. En lista över de uppskattade kostnaderna för klientorganisationer, vilket beror på mängden loggar som genereras, finns i avsnittet [Lagringsstorlek för aktivitetsloggar](#storage-size-for-activity-logs).

---

**F: Hur mycket kostar det för att strömma data till en event hub?**

**A**: Strömmande kostnaderna beror på hur många meddelanden du får per minut. Den här artikeln beskriver hur kostnaderna beräknas och visar kostnadsberäkningar som baseras på antalet meddelanden. 

---

**F: Hur jag för att integrera Azure AD-aktivitetsloggar med mitt SIEM-system?**

**A**: Du kan göra detta på två sätt:

- Använd Azure Monitor med Event Hubs för att streama loggar till ditt SIEM-system. [Strömma loggarna till en händelsehubb](tutorial-azure-monitor-stream-logs-to-event-hub.md) och [konfigurera sedan SIEM-verktyget](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) med den konfigurerade händelsehubben. 

- Använd [Reporting Graph API](concept-reporting-api.md) för att komma åt data och skicka dem till SIEM-systemet med hjälp av dina egna skript.

---

**F: Vilka SIEM-verktyg stöds för närvarande?** 

**A**: Azure Monitor är för närvarande av [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar, och [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory). Mer information om hur anslutningsapparna fungerar finns på sidan om att [strömma Azure-övervakningsdata till en händelsehubb för användning av ett externt verktyg](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

---

**F: Hur jag för att integrera Azure AD-aktivitetsloggar med mitt Splunk instans?**

**A**: Först [vägen med Azure AD-aktivitetsloggar till en händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md), Följ stegen för att [integrera aktivitetsloggar med Splunk](tutorial-integrate-activity-logs-with-splunk.md).

---

**F: Hur jag för att integrera Azure AD-aktivitetsloggar med Sumo logik?** 

**A**: Först [vägen med Azure AD-aktivitetsloggar till en händelsehubb](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), Följ stegen för att [installerar Azure AD-programmet och visa instrumentpaneler i SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**F: Kan jag komma åt data från en händelsehubb utan att använda något externt SIEM-verktyg?** 

**A**: Ja. Om du vill komma åt loggarna från ditt anpassade program kan du använda [Event Hub API](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md). 

---


## <a name="next-steps"></a>Nästa steg

* [Arkivera aktivitetsloggar till ett lagringskonto](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Dirigera aktivitetsloggar till en händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Integrera aktivitetsloggar med Log Analytics](howto-integrate-activity-logs-with-log-analytics.md)
