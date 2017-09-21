---
title: "Övervaka identitet och åtkomst i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet hjälper dig att använda identitets- och åtkomstfunktionerna i Azure Security Center för att övervaka användarnas åtkomstaktiviteter och identitetsrelaterade problem."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: sv-se
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Övervaka identitet och åtkomst i Azure Security Center
Det här dokumentet hjälper dig att använda Azure Security Center för att övervaka användarnas identitets- och åtkomstrelaterade aktiviteter.

## <a name="why-monitor-identity-and-access"></a>Varför ska du övervaka identitet och åtkomst?
Identiteten bör vara kontrollplanet för ditt företag, och din högsta prioritet bör vara att skydda din identitet. Tidigare fanns det gränser runt organisationerna och dessa utgjorde ett av de viktigaste skydden. I dag, då alltmer data och fler appar migreras till molnet, är identiteten dess nya motsvarighet.

Genom att övervaka identitetsaktiviteter kan du vidta proaktiva åtgärder innan en incident inträffar eller reaktiva åtgärder för att stoppa ett angreppsförsök. Instrumentpanelen Identitet och åtkomst innehåller en översikt över din identitetsstatus, inklusive antalet misslyckade inloggningsförsök, det användarkonto som användes vid dessa försök, konton som har låsts, konton med ändrade eller återställda lösenord och antalet konton som är inloggade för tillfället.

## <a name="how-to-monitor-identity-and-access-activities"></a>Hur övervakar du identitets- och åtkomstrelaterade aktiviteter?
Följ stegen nedan för att visualisera aktuella aktiviteter relaterade till identitet och åtkomst. Du behöver komma åt instrumentpanelen **Identitet och åtkomst**:

1.  Öppna instrumentpanelen **Security Center**.
2.  Klicka på **Identitet och åtkomst** i den vänstra rutan under **Förebygga**. Om du har flera arbetsytor visas en lista med arbetsytor du kan välja mellan.

    ![val av arbetsyta](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Om den sista kolumnen visar **UPGRADE PLAN** (uppgradera abonnemang) beror det på att den här arbetsytan använder den kostnadsfria prenumerationen och att du måste uppgradera till standardprenumerationen om du vill kunna använda den här funktionen. Om REQUIRES UPDATE (uppdaterings krävs) visas beror det på att du måste uppdatera [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) om du vill kunna använda den här funktionen. Mer information om prissättning finns i Azure Security Center pricing (Azure Security Center-prissättning). 
    > 
3. Om du har mer än en arbetsyta att undersöka, kan du prioritera vilken du undersöker enligt kolumnen **Misslyckade inloggningar**. Kolumnen visar det aktuella antalet misslyckade inloggningsförsök på den här arbetsytan. Välj den arbetsyta som du vill använda så visas sedan instrumentpanelen **Identitet och åtkomst**.

    ![identitet och åtkomst](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Med informationen på den här instrumentpanelen kan du snabbt identifiera en potentiell misstänkt aktivitet. Den här instrumentpanelen är uppdelad i tre huvudområden:
    * **Identitetsstatus**: sammanfattar de identitetsrelaterade aktiviteter som har förekommit på den här arbetsytan.
    * **Misslyckade inloggningar**: hjälper dig att snabbt identifiera huvudorsaken för ett misslyckat inloggningsförsök och visar en lista över de tio kontona med flest misslyckade inloggningsförsök.
    * **Inloggningsövertid**: hjälper dig att snabbt identifiera mängden inloggningsövertid och visar en lista över datorkonton med flest inloggningsförsök.
    
Oavsett vilken panel du väljer baseras den instrumentpanel som visas på [loggsökfrågan](https://docs.microsoft.com/azure/security-center/security-center-search), den enda skillnaden är typen av fråga och resultatet. Du kan fortfarande markera ett objekt, till exempel en dator, klicka på den och visa relevanta data. 

## <a name="see-also"></a>Se även
I det här dokumentet lär du dig att övervaka identitet och åtkomst i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center. 
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.


