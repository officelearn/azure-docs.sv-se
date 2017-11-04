---
title: "Övervaka och åtgärda säkerhetsaviseringar i Operations Management Suite säkerhet och granska lösningen | Microsoft Docs"
description: "Det här dokumentet hjälper dig att använda hot intelligence alternativ som är tillgänglig i OMS-säkerhet och granska att övervaka och åtgärda säkerhetsaviseringar."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2017
ms.author: yurid
ms.openlocfilehash: df82afab2c38431e134146143524edc080ee38f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Övervaka och åtgärda säkerhetsaviseringar i Operations Management Suite säkerhet och granska lösning
Det här dokumentet kan du använda hot intelligence alternativ som är tillgänglig i OMS-säkerhet och granska för att övervaka och åtgärda säkerhetsaviseringar.

## <a name="what-is-oms"></a>Vad är OMS?
Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur. Mer information om OMS finns i artikeln [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Hotinformation
Det är viktigt att du kan övervaka dina resurser aktivt och snabbt svara på säkerhetsincidenter i en företagsmiljö där användarna har bred åtkomst till nätverket och använder en rad olika enheter för att ansluta till företagets data. Detta är särskilt viktigt från livscykel säkerhetsperspektiv eftersom vissa cybersecurity hot inte kan generera aviseringar eller misstänkta aktiviteter som kan identifieras av traditionella tekniska säkerhetsåtgärder. 

Med hjälp av den **Hotinformation** alternativet finns i OMS-säkerhet och granskning, IT-administratörer kan identifiera säkerhetshot mot miljön, till exempel, identifiera om en viss dator är en del av en [botnät](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Datorer kan bli noder i ett botnät när angripare installerar otillåten skadlig kod som i hemlighet ansluter datorn till kommando och kontroll. Det kan även identifiera potentiella hot som kommer från lagringsutrymmen kommunikationskanaler som [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

För att skapa den här hotinformation använder OMS säkerhet och granska lösningen data från flera källor i Microsoft. OMS säkerhet och granska använder informationen för att identifiera potentiella hot mot din miljö.

Fönstret Hotinformation består av tre huvudsakliga alternativ:

* Servrar med utgående skadlig trafik
* Typer av identifierade hot
* Karta för hotinformation

> [!NOTE]
> en översikt över alla dessa alternativ, läsa [komma igång med Operations Management Suite säkerhet och granska lösningen](oms-security-getting-started.md).
> 
> 

### <a name="responding-to-security-alerts"></a>Åtgärda säkerhetsaviseringar
Ett av stegen i en [säkerhet incidenter](https://technet.microsoft.com/library/cc512623.aspx) processen är att identifiera allvarlighetsgrad för system för angrepp. I det här steget ska du utföra följande uppgifter:

* Fastställa typen av angrepp
* Fastställa angreppets ursprung
* Fastställa syftet med angreppet. Var angreppet uttryckligen riktat mot din organisation för att erhålla viss information eller var det slumpmässigt?
* Identifiera vilka system som har komprometterats
* Identifiera de filer som har öppnats och ta reda på dessa filer

Du kan utnyttja **Hotinformation** information i OMS-säkerhet och granska lösningen för att dessa åtgärder. Följ stegen nedan för att komma åt dessa **Hotinformation** alternativ:

1. Klicka på panelen **Säkerhet och granskning** på huvudinstrumentpanelen för **Microsoft Operations Management Suite**.
   
    ![Säkerhet och granskning](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. I den **säkerhet och granska** instrumentpanel, visas den **Hotinformation** alternativ till höger som visas nedan:
   
    ![Hot intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Dessa tre paneler ger en översikt över aktuella hot. I den **Server med utgående skadlig trafik** du kommer att kunna identifiera om det finns alla datorer som du övervakar (inom eller utanför ditt nätverk) som skickar skadlig trafik till Internet. 

Den **upptäckte hot typer** panelen visas en sammanfattning av de hot som är aktuella ”i jokertecken”, om du klickar på den här panelen visas mer information om dessa hot som visas nedan:

![Identifierade hottyper](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Du kan extrahera mer information om varje hot genom att klicka på den. Exemplet nedan visar mer information om Botnät:

![Mer information om ett hot](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Såsom beskrivs i början av det här avsnittet, kan den här informationen vara väldigt användbar vid ett ärende för incidenter. Det kan också vara viktigt vid kriminaltekniska undersökningar, där du vill söka efter källan för angrepp, vilket system som har drabbats och tidslinjen. I den här rapporten som du lätt kan identifiera vissa viktiga uppgifter om angrepp, exempelvis: källan för angrepp, lokala IP-Adressen som har drabbats och den aktuella sessionen staten för anslutningen. 

Den **hot intelligence kartan** hjälper dig att identifiera de aktuella platser över hela världen som har skadlig trafik. Det finns orange (inkommande) och röda (utgående) pilar i den här kartan som identifierar riktningen på nätverkstrafik, om du klickar på någon av dessa pilar, visas typ av hot och riktningen på trafik som visas nedan:

![hotinformationkarta](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> Du kan se en demonstration om hur du använder den här funktionen under en process för incidenter genom att titta på presentationen [minimera datacenter säkerhetshot interaktiv undersökningen med hjälp av Operations Management Suite](https://myignite.microsoft.com/videos/5000) levereras på Microsoft Ignite.
> 

### <a name="responding-to-distinct-malicious-ip-accessed"></a>Svara på olika skadliga IP nås
I vissa situationer kan finnas det en potentiellt skadlig IP-adress som öppnas från en övervakad dator:

![hotinformationkarta](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.PNG)

Den här aviseringen och andra inom samma kategori, skapas via OMS Security genom att använda [Microsoft Threat Intelligence](https://youtu.be/O4WtxgUrDc8). Hotinformationsdata samlas in av Microsoft samt köpts från ledande säkerhetsexperter. Dessa data uppdateras ofta och anpassade till snabbföränderliga hot. På grund av sin natur, bör det kombineras med andra säkerhetinformationskällor när du [undersöker](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) en säkerhetsavisering. 

## <a name="customize-alerts-received-via-e-mail"></a>Anpassa aviseringar via e-post

Du kan anpassa vilka användare i din organisation ska meddelas när säkerhetsvarningar utlöses av OMS-säkerhet. Det här alternativet är tillgängligt under översikt / inställningar på OMS-instrumentpanelen:

![E-post](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig7.png)

## <a name="see-also"></a>Se även
I det här dokumentet du har lärt dig hur du använder den **Hotinformation** alternativet i OMS-säkerhet och granska lösningen för att åtgärda säkerhetsaviseringar. Mer information om säkerheten i OMS finns i följande artiklar:

* [Översikt över Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Komma igång med Operations Management Suite säkerhet och granska lösningen](oms-security-getting-started.md)
* [Övervaka resurser i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-monitoring-resources.md)

