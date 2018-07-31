---
title: Azure Active Directory-aktivitetsloggar i Azure Monitor (förhandsversion) | Microsoft Docs
description: Översikt över Azure Active Directory-aktivitetsloggar i Azure Monitor (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240124"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Azure Active Directory-aktivitetsloggar i Azure Monitor (förhandsversion)

Du kan nu dirigera Azure AD-aktivitetsloggar med hjälp av Azure Monitor till ditt eget lagringskonto eller din händelsehubb. Med den offentliga förhandsversionen av Azure Active Directory-loggar i Azure Monitor kan du:

* Arkivera spårningsloggar för ett Azure-lagringskonto, så att du kan behålla data under en längre tid
* Strömma spårningsloggar till en Azure-händelsehubb för analys med hjälp av populära SIEM-verktyg som Splunk och QRadar
* Integrera spårningsloggar med dina egna anpassade logglösningar genom att strömma dem till en händelsehubb

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Rapporter som stöds

Du kan dirigera aktivitetsspårningsloggar och inloggningsaktivitetsloggar till ditt Azure-lagringskonto, en händelsehubb eller en anpassad lösning med hjälp av den här funktionen. 

* **Spårningsloggar**: [Rapporten om spårningsloggaktivitet](active-directory-reporting-activity-audit-logs.md) ger dig tillgång till historiken för varje aktivitet i din klient.
* **Inloggningar**: Med [rapporten om inloggningsaktivitet](active-directory-reporting-activity-sign-ins.md) kan du se vem som har utfört de uppgifter som rapporteras i granskningsloggarna.

> [!NOTE]
> B2C-relaterade spårnings- och inloggningsaktivitetsloggar stöds inte just nu.
>

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda den här funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* [Licens](https://azure.microsoft.com/pricing/details/active-directory/) för Azure AD Free, Basic, Premium 1 eller Premium 2 för att få åtkomst till Azure AD-loggar i Azure-portalen. 

Beroende på var du vill dirigera spårningsloggdata behöver du antingen:

* Ett Azure-lagringskonto som du har *ListKeys*-behörigheter för. Vi rekommenderar att du använder ett allmänt lagringskonto och inte ett blob-lagringskonto. Prisinformation för lagring finns i [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Namnområde för Azure-händelsehubbar för att kunna integrera med lösningar från tredje part.

> [!NOTE]
> Du måste vara *global administratör* eller *säkerhetsadministratör* i Azure AD-klientorganisationen för att få åtkomst till Azure AD-aktivitetsloggar.
>

## <a name="cost-considerations"></a>Kostnadsöverväganden

Om du redan har en Azure AD-licens behöver du en Azure-prenumeration för att konfigurera lagringskontot och händelsehubben. Azure-prenumerationen är kostnadsfri, men du behöver betala för att använda Azure-resurser, inklusive det lagringskonto som du använder för arkivering och den händelsehubb som du använder för strömning. Mängden data och därmed kostnaden varierar avsevärt beroende på storleken på klientorganisationen. 

### <a name="storage-size-for-activity-logs"></a>Lagringsstorlek för aktivitetsloggar

Varje spårningslogghändelse använder cirka 2 KB datalagring. I en klientorganisation med 100 000 användare skulle det ske ungefär 1,5 miljoner händelser per dag, vilket skulle kräva ungefär 3 GB datalagring per dag. Eftersom skrivningar sker i batchar om cirka 5 minuter kan du förvänta dig ungefär 9000 skrivåtgärder per månad. Följande tabell innehåller en ungefärlig uppskattning av kostnaden, beroende på klientorganisationens storlek, för ett GPv2-lagringskonto i västra USA för minst ett års kvarhållning. Använd [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) för att skapa en mer tillförlitlig uppskattning av den datavolym som förväntar dig att du behöver för programmet. Varje spårningslogghändelse använder cirka 2 KB datalagring. I en klientorganisation med 100 000 användare skulle det ske ungefär 1,5 miljoner händelser per dag, vilket skulle kräva ungefär 3 GB datalagring per dag. Eftersom skrivningar sker i batchar om cirka 5 minuter kan du förvänta dig ungefär 9000 skrivåtgärder per månad. Följande tabell innehåller en ungefärlig uppskattning av kostnaden, beroende på klientorganisationens storlek, för ett GPv2-lagringskonto i västra USA för minst ett års kvarhållning. Använd [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) för att skapa en mer tillförlitlig uppskattning av den datavolym som förväntar dig att du behöver för programmet. 

| Loggkategori | Antal användare | Antal händelser per dag | Ungefärlig datavolym per månad | Ungefärlig kostnad per månad | Ungefärlig kostnad per år |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Granska | 100 000 | 1,5 miljoner | 90 GB | 1,93 USD | 23,12 USD |
| Granska | 1000 | 15 000 | 900 MB | 0,02 USD | 0,24 USD |
| Inloggningar | 1000 | 34 800 | 4 GB | 0,13 USD | 1,56 USD |
| Inloggningar | 100 000 | 15 miljoner | 1,7 TB | 35,41 USD | 424,92 USD | 


### <a name="event-hub-messages-for-activity-logs"></a>Händelsehubbmeddelanden för aktivitetsloggar

Händelser batchas i intervall på ungefär fem minuter och skickas som ett enskilt meddelande som innehåller alla händelser inom den tidsramen. Ett meddelande i en händelsehubb har en maximal storlek på 256 kB, och om storleken på alla meddelanden inom tidsramen överstiger den volymen skickas flera meddelanden. 

Till exempel sker det vanligtvis cirka 18 händelser per sekund för en stor klientorganisation med fler än 100 000 användare, vilket blir 5400 händelser var 5:e minut. Eftersom spårningsloggar är på cirka 2 kB per händelse motsvarar detta 10,8 MB data. Därför skickas 43 meddelanden till händelsehubben i det 5-minutersintervallet. Följande tabell innehåller en ungefärlig kostnad för en grundläggande händelsehubb i västra USA, beroende på mängden händelsedata. Använd [priskalkylatorn för händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/) för att beräkna en tillförlitlig uppskattning av den datavolym som förväntar dig att du behöver för programmet.

| Loggkategori | Antal användare | Antal händelser per sekund | Antal händelser per 5-minutersintervall | Volym per intervall | Antal meddelanden per intervall | Antal meddelanden per månad | Ungefärlig kostnad per månad |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Granska | 100 000 | 18 | 5400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Granska | 1000 | 0,1 | 52 | 104 KB | 1 | 8640 | 10,8 USD |
| Inloggningar | 1000 | 178 | 53 400 | 106,8 MB | 418 | 3 611 520 | 11,06 USD |  


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Det här avsnittet innehåller vanliga frågor och kända problem med Azure Active Directory-loggar i Azure Monitor.

**F: Var börjar jag?** 

**S:** Börjar med [översikten](reporting-azure-monitor-diagnostics-overview.md) för att få en uppfattning om vad du behöver för att distribuera den här funktionen. När du är bekant med förutsättningarna kan du använda självstudierna för att få hjälp med att konfigurera och dirigera dina loggar till Event Hubs.

---

**F: Vilka loggar ingår?**

**S:** Både inloggningar och spårningsloggar kan användas för att dirigeras via den här funktionen, men B2C-relaterade spårningsloggar ingår inte för närvarande. Läs [schemat för spårningslogg](reporting-azure-monitor-diagnostics-audit-log-schema.md) och [schemat för inloggningslogg](reporting-azure-monitor-diagnostics-sign-in-log-schema.md) för att ta reda på vilka typer av loggar och vilka funktionsbaserade loggar som stöds för närvarande. 

---

**F: Hur snart efter en åtgärd visas motsvarande loggar i Event Hubs?**

**S:** Loggarna bör visas i Event Hubs mellan två och fem minuter efter att en åtgärd har genomförts. Mer information om händelsehubbar finns i [Vad är händelsehubbar?](/azure/event-hubs/event-hubs-what-is-event-hubs.md)

---

**F: Hur snart efter en åtgärd visas motsvarande loggar i lagringskonton?**

**S:** för Azure-lagringskonton är svarstiden mellan 5 och 15 minuter efter att en åtgärd har genomförts.

---

**F: Hur mycket kostar det att lagra mina data?**

**S:** Lagringskostnaden beror på storleken på dina loggar samt den kvarhållningsperiod som du väljer. En ungefärlig uppskattning av kostnaderna för klientorganisationer beroende på den mängd loggar som genereras finns i [översikten](reporting-azure-monitor-diagnostics-overview.md).

---

**F: hur mycket kostar det att strömma mina data till Event Hubs?**

**S:** Kostnaden för strömning beror på hur många meddelanden du får per minut. Läs [översikten](reporting-azure-monitor-diagnostics-overview.md) för att få mer information om hur kostnaden beräknas samt för att få en ungefärlig uppskattning baserat på antalet meddelanden. 

---

**F: Vilka SIEM-verktyg stöds för närvarande?** 

**S:** Azure Monitor stöds för närvarande av Splunk, QRadar och Sumologic. Splunk är dock det enda SIEM-verktyget som stöds för Azure Active Directory-loggar. Mer information om hur anslutningsapparna fungerar finns på sidan om att [strömma Azure-övervakningsdata till en händelsehubb för användning av ett externt verktyg](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs).

---

**F: Kan jag komma åt data från Event Hub utan att använda ett externt SIEM-verktyg?** 

**S:** Ja, du kan använda [Event Hub API](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) för att komma åt loggarna från ditt anpassade program. 

---


## <a name="next-steps"></a>Nästa steg

* [Arkivera aktivitetsloggar till ett lagringskonto](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Strömma aktivitetsloggar till händelsehubb](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Läs mer om Azure-diagnostikloggar](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)