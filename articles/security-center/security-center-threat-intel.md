---
title: Hotinformation i Azure Security Center | Microsoft Docs
description: "Lär dig hur du använder hotinformationsfunktionen i Azure Security Center för att identifiera potentiella hot i dina datorer och virtuella datorer."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: b5bdeb1c70893c2477413df02c7b1a4a07999e93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="threat-intelligence-in-azure-security-center"></a>Hotinformation i Azure Security Center
Den här artikeln hjälper dig använda hotinformationen i Azure Security Center till att åtgärda säkerhetsrelaterade problem.

## <a name="what-is-threat-intelligence"></a>Vad är hotinformation?
Genom att använda alternativet för hotinformation i Security Center kan IT-administratörer identifiera säkerhetshot mot miljön. De kan till exempel identifiera om en vissa dator är en del av ett botnät. Datorer kan bli noder i ett botnät när angripare installerar otillåten skadlig kod som i hemlighet ansluter datorn till kommando och kontroll. Hotinformation kan även identifiera möjliga hot som kommer från underjordiska kommunikationskanaler, till exempel mörka internet.

För generering av den här hotinformationen använder Security Center data från flera källor på Microsoft. Security Center använder dessa data för identifiering av potentiella hot mot din miljö. Fönstret **Hotinformation** består av tre huvudsakliga alternativ:

- Identifierade hottyper
- Hotursprung
- Karta för hotinformation


## <a name="when-should-you-use-threat-intelligence"></a>När ska du använda hotinformation?
Ett av stegen i [processen för svar på säkerhetsincidenter](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) är att identifiera allvarlighetsgraden för de komprometterade systemen. I det här steget bör du utföra följande uppgifter:

- Fastställa typen av angrepp.
- Fastställa angreppets ursprung.
- Fastställa syftet med angreppet. Var angreppet riktat mot din organisation för att erhålla viss information eller var det slumpmässigt?
- Identifiera systemen som har drabbats.
- Identifiera de filer som har använts och fastställ de filernas känslighet.

Du kan använda hotinformation i Security Center för de här uppgifterna. 

## <a name="access-the-threat-intelligence"></a>Komma åt hotinformationen
Om du vill visualisera aktuell hotinformation för din miljö måste du först markera arbetsytan där din information finns. Om du inte har flera arbetsytor går du förbi väljaren för arbetsytor och du hamnar direkt på instrumentpanelen **Hotinformation**. Öppna instrumentpanelen:

1. Öppna instrumentpanelen för **Security Center**.

2. Välj **Hotinformation** i den vänstra rutan under **Identifiering**. Instrumentpanelen **Hotinformation** visas.

    ![Instrumentpanelen Hotinformation](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Om kolumnen längst till höger visar **UPGRADE PLAN** (Uppgradera abonnemang) använder den här arbetsytan den kostnadsfria prenumerationen. Uppgradera till Standard om du vill använda den här funktionen. Om kolumnen längst till höger visar **REQUIRES UPDATE** (Kräver uppdatering) uppdaterar [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) om du vill använda funktionen. Mer information om prissättning finns i Azure Security Center pricing (Azure Security Center-prissättning). 
    > 
3. Om du har fler än en arbetsyta att undersöka prioriterar du undersökningen enligt kolumnen **Skadlig IP-adress**. Den visar det aktuella antalet skadliga IP-adresser på den här arbetsytan. Välj den arbetsyta du vill använda så visas instrumentpanelen **Hotinformation**.

    ![Hotinformation](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Instrumentpanelen är indelad i fyra paneler:

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


## <a name="see-also"></a>Se även
I den här artikeln lärde du dig att använda hotinformation i Security Center som hjälp vid identifiering av misstänkt aktivitet. I följande artiklar kan du lära dig mer om Security Center:

* [Hantera och besvara säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center. 
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

