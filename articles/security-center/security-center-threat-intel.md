---
title: Hotinformation & säkerhetsvarningskarta - Azure Security Center
description: Lär dig hur du använder säkerhetsvarningskartan och hotinformationsfunktionen i Azure Security Center för att identifiera potentiella hot i dina virtuella datorer och datorer.
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
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603428"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Säkerhetsvarningsmappning och hotinformation
Den här artikeln hjälper dig att använda säkerhetsaviseringarmappningen för Azure Security Center och säkerhetshändelsebaserad hotinformationskarta för att åtgärda säkerhetsrelaterade problem.

> [!NOTE]
> Knappen *Säkerhetshändelser* har dragits tillbaka den 31 juli 2019. Mer information och alternativa tjänster finns i [Funktioner för Pensionering av Säkerhetscenter (juli 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Så här fungerar säkerhetsvarningarskartan
Security Center ger dig en karta som hjälper dig att identifiera säkerhetshot mot miljön. Du kan till exempel identifiera om en viss dator är en del av ett botnät och var hotet kommer ifrån. Datorer kan bli noder i ett botnät när angripare olagligt installerar skadlig kod som i hemlighet interagerar med kommando och kontroll som hanterar botnätet. 

För att skapa den här kartan använder Security Center data som kommer från flera källor i Microsoft. Security Center använder dessa data för att kartlägga potentiella hot mot din miljö. 

Ett av stegen i [processen för svar på säkerhetsincidenter](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) är att identifiera allvarlighetsgraden för de komprometterade systemen. I det här steget bör du utföra följande uppgifter:

- Fastställa typen av angrepp.
- Bestäm utgångspunkten för attacken.
- Fastställa syftet med angreppet. Var angreppet riktat mot din organisation för att erhålla viss information eller var det slumpmässigt?
- Identifiera systemen som har drabbats.
- Identifiera de filer som har använts och fastställ filernas känslighet.

Du kan använda säkerhetsaviseringskartan i Security Center för att hjälpa till med dessa uppgifter.

## <a name="access-the-security-alerts-map"></a>Öppna kartan Säkerhetsvarningar
Om du vill visualisera de aktuella hoten på din miljö öppnar du kartan Säkerhetsvarningar:

1. Öppna instrumentpanelen för **Security Center**.
2. I den vänstra rutan väljer du **säkerhetsvarningar på kartan**under **Skydd mot hot.** Kartan öppnas.
3. Om du vill ha mer information om aviseringen och få reparationssteg klickar du på varningspunkten på kartan och följer instruktionerna. 
 
Säkerhetsvarningar kartan är baserad på aviseringar. Dessa aviseringar baseras på aktiviteter för vilka nätverkskommunikation har associerats med en IP-adress som har lösts, oavsett om IP-adressen är en känd riskfylld IP-adress (till exempel en känd kryptominer) eller en IP-adress som inte känns igen tidigare som riskabelt. Kartan innehåller aviseringar för alla prenumerationer som du tidigare har valt i Azure. 

Aviseringarna på kartan visas enligt den geografiska plats där de identifieras som kommer från, och de färgkodas efter allvarlighetsgrad. 
    ![Hotinformation](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Se även
I den här artikeln lärde du dig att använda hotinformation i Security Center som hjälp vid identifiering av misstänkt aktivitet. I följande artiklar kan du lära dig mer om Security Center:

* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.