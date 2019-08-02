---
title: Översikt över hot information och säkerhets avisering i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder funktionen för säkerhets aviserings mappning och hot information i Azure Security Center för att identifiera potentiella hot i dina virtuella datorer och datorer.
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
ms.openlocfilehash: 0740beb3b1ffc200c36ca4b5c15b25017821587e
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662345"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Säkerhetsvarningsmappning och hotinformation
Den här artikeln hjälper dig att använda säkerhets aviserings mappningen Azure Security Center säkerhets aviseringar och säkerhets händelser som bygger på säkerhetsrelaterade problem.

> [!NOTE]
> Knappen mappning av säkerhets *händelser* har dragits tillbaka den 31 juli 2019. Mer information och alternativa tjänster finns i [dra tillbaka Security Center funktioner (2019 juli)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Så här fungerar kartan för säkerhets aviseringar
Security Center ger dig en karta som hjälper dig att identifiera säkerhetshot mot miljön. Du kan till exempel identifiera om en viss dator är en del av en botnät och var hotet kommer från. Datorer kan bli noder i en botnät när angriparen illegalt installerar skadlig kod som interagerar med kommandot och kontrollen som hanterar botnät. 

För att bygga den här kartan använder Security Center data som kommer från flera källor i Microsoft. Security Center använder dessa data för att mappa potentiella hot mot din miljö. 

Ett av stegen i [processen för svar på säkerhetsincidenter](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) är att identifiera allvarlighetsgraden för de komprometterade systemen. I det här steget bör du utföra följande uppgifter:

- Fastställa typen av angrepp.
- Fastställ Angreppets start punkt.
- Fastställa syftet med angreppet. Var angreppet riktat mot din organisation för att erhålla viss information eller var det slumpmässigt?
- Identifiera systemen som har drabbats.
- Identifiera de filer som har använts och fastställ filernas känslighet.

Du kan använda säkerhets aviserings kartan i Security Center för att få hjälp med dessa uppgifter.

## <a name="access-the-security-alerts-map"></a>Öppna säkerhets aviserings kartan
Om du vill visualisera aktuella hot i miljön öppnar du säkerhets aviserings kartan:

1. Öppna instrumentpanelen för **Security Center**.
2. Välj **säkerhets aviserings karta**i den vänstra rutan under **hot skydd** . Kartan öppnas.
3. Om du vill ha mer information om stegen avisering och mottagnings reparation klickar du på varnings punkten på kartan och följer instruktionerna. 
 
Mappningen av säkerhets aviseringar baseras på aviseringar. De här aviseringarna baseras på aktiviteter för vilka nätverkskommunikation har associerats med en IP-adress som har lösts, oavsett om IP-adressen är en känd riskfylld IP-adress (till exempel en känd cryptominer) eller en IP-adress som inte känns igen tidigare som riskfyllda. Kartan ger aviseringar över alla prenumerationer som du tidigare har valt i Azure. 

Aviseringarna på kartan visas enligt den geografiska plats där de identifieras som härstammar från, och de är färgkodade efter allvarlighets grad. 
    ![Information om hot information](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Se också
I den här artikeln lärde du dig att använda hotinformation i Security Center som hjälp vid identifiering av misstänkt aktivitet. I följande artiklar kan du lära dig mer om Security Center:

* [Hantera och besvara säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
