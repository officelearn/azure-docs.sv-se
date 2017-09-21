---
title: Hotinformation i Azure Security Center | Microsoft Docs
description: "Det här dokumentet hjälper dig att använda hotinformationsfunktionen i Azure Security Center för att identifiera potentiella hot i dina datorer och virtuella datorer."
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
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: sv-se
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Hotinformation i Azure Security Center
Det här dokumentet hjälper dig använda hotinformationen i Azure Security Center till att åtgärda säkerhetsrelaterade problem.

## <a name="what-is-threat-intelligence"></a>Vad är hotinformation?
När IT-administratörer använder alternativet för hotinformation i Security Center kan de identifiera säkerhetshot mot miljön, till exempel identifiera om en specifik dator är en del av ett botnät. Datorer kan bli noder i ett botnät när angripare installerar otillåten skadlig kod som i hemlighet ansluter datorn till kommando och kontroll. Hotinformation kan också identifiera potentiella hot från kommunikationskanaler under marken, som darknet.

För generering av den här hotinformationen använder Security Center data från flera källor på Microsoft. Security Center använder dessa data för identifiering av potentiella hot mot din miljö. Fönstret Hotinformation består av tre huvudsakliga alternativ:

- Identifierade hottyper
- Hotursprung
- Karta för hotinformation


## <a name="when-should-i-use-threat-intelligence"></a>När ska jag använda hotinformation?
Ett av stegen i [processen för svar på säkerhetsincidenter](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) är att identifiera allvarlighetsgraden för de komprometterade systemen. I det här steget bör du utföra följande uppgifter:

- Fastställa typen av angrepp
- Fastställa angreppets ursprung
- Fastställa syftet med angreppet. Var angreppet uttryckligen riktat mot din organisation för att erhålla viss information eller var det slumpmässigt?
- Identifiera vilka system som har komprometterats
- Identifiera de filer som har använts och ta reda på hur känsliga de filerna är. Du kan använda hotinformationen i Security Center till hjälp med dessa åtgärder. 

## <a name="how-to-access-the-threat-intelligence"></a>Hur kommer du åt hotinformationen?
Om du vill visualisera aktuell hotinformation för din miljö måste du först markera arbetsytan där din information finns. Om du inte har flera arbetsytor visas inte väljaren för arbetsytor och du hamnar direkt på instrumentpanelen **Hotinformation**. Följ stegen nedan för att komma åt instrumentpanelen Hotinformation:

1.  Öppna instrumentpanelen **Security Center**.
2.  Klicka på **Hotinformation** i den vänstra rutan under **Identifiering**. Instrumentpanelen **Hotinformation** visas.

    ![Hotinformation](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Om den sista kolumnen visar **UPGRADE PLAN** (uppgradera abonnemang) beror det på att den här arbetsytan använder den kostnadsfria prenumerationen och att du måste uppgradera till standardprenumerationen om du vill kunna använda den här funktionen. Om REQUIRES UPDATE (uppdaterings krävs) visas beror det på att du måste uppdatera [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) om du vill kunna använda den här funktionen. Mer information om prissättning finns i Azure Security Center pricing (Azure Security Center-prissättning). 
    > 
3. Om du har fler än en arbetsyta att undersöka, kan du prioritera undersökningen enligt kolumnen **Skadlig IP-adress**. Kolumnen visar det aktuella antalet skadliga IP-adresser på den här arbetsytan. Välj den arbetsyta du vill använda så visas instrumentpanelen **Hotinformation**.

    ![Hotinformation](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Den här instrumentpanelen är indelad i fyra paneler:
    * **Hottyper**: sammanfattar de typer av hot som upptäcktes på den valda arbetsytan.
    * **Ursprungsland**: aggregerar mängden trafik enligt ursprungsplats.
    * **Hotplats**: hjälper dig att identifiera de aktuella platserna runtom i världen som kommunicerar med din miljö. Det finns orangefärgade (inkommande) och rödfärgade (utgående) pilar på den här kartan som identifierar nätverkstrafikens riktning. Om du klickar på någon av dessa pilar, visas typen av hot och riktningen för nätverkstrafiken.
    * **Hotinformation**: visar mer information om hotet du markerade på kartan.

Oavsett vilken alternativpanel du väljer baseras den instrumentpanel som visas på [loggsökfrågan](https://docs.microsoft.com/azure/security-center/security-center-search), den enda skillnaden är typen av fråga och resultatet.

### <a name="threat-types"></a>Hottyper
När du klickar på panelen **Hottyper** visas instrumentpanelen **Loggsökning**. I den har du filteralternativen till vänster och frågeresultaten till höger.

![Hotinformationssökning](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Frågeresultatet visar hoten sorterade efter namn. Du kan använda den vänstra rutan och välja det attribut du vill filtrera efter. Om du till exempel bara vill se de hot som för närvarande är anslutna till datorerna, måste du välja **Anslutna** i **Sessionstillstånd** och klicka på knappen **Tillämpa**.

![sessionstillstånd](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

För virtuella Azure-datorer visas bara de nätverksdata som flödar genom agenten i instrumentpanelen för hotinformation. Följande datatyper används också för hotinformation:

- CEF-data (typ = CommonSecurityLog)
- WireData (typ = WireData)
- IIS-loggar (typ = W3CIISLog)
- Windows-brandväggen (typ = WindowsFirewall)
- DNS-händelser (typ = DnsEvents)


## <a name="see-also"></a>Se även
I det här dokumentet lärde du dig att använda hotinformation i Security Center som hjälp vid identifiering av misstänkt aktivitet. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center. 
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.


