---
title: Intelligens och avisering säkerhetskarta i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder den karta och threat intelligence funktionen för säkerhetsavisering i Azure Security Center för att identifiera potentiella hot i dina virtuella datorer och datorer.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 07592c6852a7fce057381cb9086baedc9d2b1bd6
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111755"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Säkerhetsvarningsmappning och hotinformation
Den här artikeln hjälper dig att använda Azure Security Center säkerhetskarta aviseringar och säkerhet händelsebaserad karta för hotinformation att åtgärda säkerhetsrelaterade problem med.

## <a name="how-the-security-alerts-map-works"></a>Hur säkerhetsaviseringar mappar fungerar
Security Center ger dig en karta som hjälper dig att identifiera säkerhetshot mot miljön. Du kan till exempel identifiera om en specifik dator är en del av ett botnät, och där hotet kommer från. Datorer kan bli noder i ett botnät när angripare installerar otillåten skadlig kod som hemlighet interagerar med kommando och kontroll som hanterar botnet. 

Om du vill skapa den här kartan använder Security Center data som kommer från flera källor på Microsoft. Security Center använder dessa data för att mappa potentiella hot mot din miljö. 

Ett av stegen i [processen för svar på säkerhetsincidenter](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) är att identifiera allvarlighetsgraden för de komprometterade systemen. I det här steget bör du utföra följande uppgifter:

- Fastställa typen av angrepp.
- Fastställa referenspunkt av angrepp.
- Fastställa syftet med angreppet. Var angreppet riktat mot din organisation för att erhålla viss information eller var det slumpmässigt?
- Identifiera systemen som har drabbats.
- Identifiera de filer som har använts och fastställ filernas känslighet.

Du kan använda säkerhetskarta för aviseringar i Security Center för att de här uppgifterna.

## <a name="access-the-security-alerts-map"></a>Få åtkomst till aviseringar säkerhetskarta
Öppna aviseringar säkerhetskarta för att visualisera aktuella hot på din miljö:

1. Öppna instrumentpanelen för **Security Center**.
2. I den vänstra rutan under **Hotskydd** Välj **aviseringar säkerhetskarta**. Kartan öppnas.
3. Klicka på aviseringen prick på kartan för att få mer information om aviseringen och ta emot steg, och följ instruktionerna. 
 
Aviseringar säkerhetskarta baseras på aviseringar. Dessa aviseringar baseras på aktiviteter för vilka nätverk kommunikation associerades med en IP-adress som löstes har, oavsett om IP-adressen är en känd riskfyllda IP-adress (till exempel en känd cryptominer) eller en IP-adress som inte känns igen tidigare som riskfyllt. Kartan innehåller varningar för alla prenumerationer som du tidigare valt i Azure. 

Aviseringar på kartan visas enligt den geografiska platsen där de identifieras som kommer från, och de är färgkodade efter allvarlighetsgrad. 
    ![Hotinformation](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Visa händelsebaserad instrumentpanelen för hotinformation
Du kan följa den här proceduren om du vill visa den karta för hotinformation baserat på raw säkerhetshändelser. Den här kartan visar endast de händelser som rör en IP-adress som anses risk, till exempel en IP-adress av ett känt botnät.

1. Öppna instrumentpanelen för **Security Center**.

1. I den vänstra rutan under **Hotskydd** Välj **aviseringar säkerhetskarta**. Kartan öppnas.
2. I övre högra hörnet, klickar du på **går du till händelser säkerhetskarta**.
3. Välj den arbetsyta som du vill visa instrumentpanelen.
4. Högst upp på kartan, Välj **Visa klassiska hotinformation**. Den **Hotinformation** instrumentpanelen öppnas.

   > [!NOTE]
   > Om kolumnen längst till höger visar **UPGRADE PLAN** (Uppgradera abonnemang) använder den här arbetsytan den kostnadsfria prenumerationen. Uppgradera till Standard om du vill använda den här funktionen. Om kolumnen längst till höger visar **REQUIRES UPDATE** (Kräver uppdatering) uppdaterar [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) om du vill använda funktionen. Mer information om prissättning finns i Azure Security Center pricing (Azure Security Center-prissättning).
   >
5. Om du har fler än en arbetsyta att undersöka prioriterar du undersökningen enligt kolumnen **Skadlig IP-adress**. Den visar det aktuella antalet skadliga IP-adresser på den här arbetsytan. Välj den arbetsyta du vill använda så visas instrumentpanelen **Hotinformation**.

    ![Hotinformation](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. Instrumentpanelen är indelad i fyra paneler:

    a.  **Hottyper**. Sammanfattar de typer av hot som upptäcktes på den valda arbetsytan.

    b.  **Ursprungsland**. Aggregerar mängden trafik enligt ursprungsplats.

    c.  **Hotplats**. Hjälper dig att identifiera de aktuella platserna runtom i världen som kommunicerar med din miljö. I kartan som visas identifierar orange (inkommande) och röd (utgående) pil trafikriktningarna. Om du väljer en av dessa pilar visas typen av hot och trafikriktningen.

    d.  **Hotinformation**. Visar mer information om hotet du markerade på kartan.

Oavsett vilken alternativpanel du väljer baseras instrumentpanelen som visas på loggsökningsfrågan. Den enda skillnaden är frågetyp och resultat.

### <a name="threat-types"></a>Hottyper
Välj panelen **Hottyper** för att öppna instrumentpanelen **Loggsökning**. Filteralternativ visas till vänster och frågeresultat visas till höger.

![Loggsökning](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Frågeresultatet visar hoten efter namn. Du kan använda den vänstra rutan till att välja attributet du vill filtrera. Om du till exempel bara vill se hoten som för närvarande är kopplade till datorerna går du till **SESSIONSTATE** (Sessionstillstånd) och väljer **Ansluten** > **Använd**.

![Sessionstillstånd](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

För virtuella Azure-datorer visas bara de nätverksdata som flödar genom agenten i instrumentpanelen **Hotinformation**. Följande datatyper används också för hotinformation:

- CEF-data (typ = CommonSecurityLog)
- WireData (typ = WireData)
- IIS-loggar (typ = W3CIISLog)
- Windows-brandväggen (typ = WindowsFirewall)
- DNS-händelser (typ = DnsEvents)


## <a name="see-also"></a>Se också
I den här artikeln lärde du dig att använda hotinformation i Security Center som hjälp vid identifiering av misstänkt aktivitet. I följande artiklar kan du lära dig mer om Security Center:

* [Hantera och besvara säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
