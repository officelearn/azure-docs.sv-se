---
title: Övervaka identitet och åtkomst i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder identitets- och åtkomstfunktionerna i Azure Security Center för att övervaka användarnas åtkomstaktiviteter och identitetsrelaterade problem.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 5ee263ef8fb0f20049215eda53e0d58a45342b7e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774836"
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Övervaka identitet och åtkomst i Azure Security Center
Den här artikeln hjälper dig att använda Azure Security Center för att övervaka användarnas identitets- och åtkomstrelaterade aktiviteter.

## <a name="why-monitor-identity-and-access"></a>Varför ska du övervaka identitet och åtkomst?
Identiteten bör vara kontrollplanet för ditt företag, och din högsta prioritet bör vara att skydda din identitet. Tidigare fanns det perimetrar kring organisationer och dessa var en av de primära försvarsgränserna. Nuförtiden, då mer data och fler appar flyttas till molnet, blir identiteten den nya perimetern.

Genom att övervaka identitetsaktiviteter kan du vidta proaktiva åtgärder innan en incident inträffar eller reaktiva åtgärder för att stoppa ett angreppsförsök. Med instrumentpanelen Identitet och åtkomst får du en översikt över ditt identitetstillstånd, bl.a:

* Antal misslyckade inloggningsförsök. 
* Användarkonton som användes vid de försöken.
* Konton som låstes ute.
* Konton med ändrade eller återställda lösenord. 
* Aktuellt antal konton som är inloggade.

## <a name="monitor-identity-and-access-activities"></a>Övervaka identitets- och åtkomstrelaterade aktiviteter
Om du vill se aktuella aktiviteter relaterade till identitet och åtkomst måste du öppna instrumentpanelen **Identitet och åtkomst**.

1. Öppna instrumentpanelen för **Security Center**.

2. Välj **Identitet och åtkomst** i den vänstra rutan under **Förebygga**. Om du har flera arbetsytor visas en lista med arbetsytor du kan välja mellan.

    ![Val av arbetsyta](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Om kolumnen längst till höger visar **UPGRADE PLAN** (Uppgradera abonnemang) använder den här arbetsytan den kostnadsfria prenumerationen. Uppgradera till standardprenumerationen om du vill använda den här funktionen. Om kolumnen längst till höger visar **REQUIRES UPDATE** (Kräver uppdatering) uppdaterar [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) om du vill använda funktionen. Mer information om prissättning finns i Security Center pricing (Security Center-prissättning). 
    > 
3. Om du har fler än en arbetsyta att undersöka kan du prioritera undersökningen enligt kolumnen **FAILED LOGONS** (Misslyckade inloggningar). Den visar det aktuella antalet misslyckade inloggningsförsök på den här arbetsytan. Välj den arbetsyta som du vill använda så visas instrumentpanelen **Identitet och åtkomst**.

    ![Identitet och åtkomst](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Med informationen på den här instrumentpanelen kan du snabbt identifiera en potentiell misstänkt aktivitet. Den här instrumentpanelen är uppdelad i tre huvudområden:

    a. **Identitetsstatus**. Sammanfattar identitetsrelaterade aktiviteter som äger rum på den här arbetsytan.

    b. **Misslyckade inloggningar**. Hjälper dig att snabbt identifiera huvudorsaken till misslyckade inloggningsförsök. Visar en lista över de 10 konton som har flest misslyckade inloggningsförsök.

    c. **Inloggningar över tid**. Hjälper dig att snabbt identifiera antalet inloggningar över tid. En lista visas med datorer som har flest kontoinloggningsförsök.
    
Oavsett vilken panel du väljer baseras instrumentpanelen som visas på loggsökningsfrågan. Den enda skillnaden är frågetyp och resultat. Du kan fortfarande markera ett objekt, till exempel en dator, och visa relevanta data. 

## <a name="see-also"></a>Se även
I den här artikeln har du lärt dig hur du övervakar identitet och åtkomst i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Hantera och besvara säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center. 
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om användningen av Security Center.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

