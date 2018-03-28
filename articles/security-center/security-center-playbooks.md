---
title: Säkerhetsspelbok i Azure Security Center | Microsoft Docs
description: Det här dokumentet hjälper dig att använda strategiböcker för säkerhet i Azure Security Center för att automatisera svaret på säkerhetshändelser.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: yurid
ms.openlocfilehash: 76f2666df76b802c175eadc38d2ac4100799ceae
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Säkerhetsspelbok i Azure Security Center (förhandsversion)
Det här dokumentet hjälper dig att använda strategiböcker för säkerhet i Azure Security Center för att svara på säkerhetsrelaterade problem.

## <a name="what-is-security-playbook-in-security-center"></a>Vad är en säkerhetsspelbok i Security Center?
Säkerhetsspelbok är en samling processer som kan köras från Security Center när en viss spelbok löses ut av en vald avisering. Säkerhetsspelboken kan hjälpa till att automatisera och dirigera svaret på en viss säkerhetshändelse som upptäcks av Security Center. Strategiböcker för säkerhet i Security Center bygger på [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), vilket innebär att du kan använda mallarna som finns i säkerhetskategorin i Logic Apps-mallarna och ändra dem efter dina behov, eller så kan du skapa nya strategiböcker med hjälp av [arbetsflödet i Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) och använda Security Center som utlösare. 

> [!NOTE]
> Spelboken använder Azure Logic Apps och därför tillkommer avgifter. Besök prissidan för [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) om du vill ha mer information. 

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Hur skapar man en säkerhetsspelbok från Security Center?
Gör så här för att skapa en ny säkerhetsspelbok från Security Center:

1.  Öppna instrumentpanelen **Security Center**.
2.  Under **Automation & Orchestration** (Automatisering och orkestrering) i den vänstra rutan klickar du på **Playbooks (Preview)** (Strategiböcker (förhandsversion)).

    ![Logikapp](./media/security-center-playbooks/security-center-playbooks-fig17.png)
 
3. På sidan **Security Center – Playbooks (Preview)** (Security Center – Strategiböcker (förhandsversion)) klickar du på knappen för att **lägga till**.

    ![Skapa en logikapp](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. På sidan **Skapa en logikapp** skriver du in den information som efterfrågas för att skapa den nya logikappen och klickar på knappen **Skapa**. När den har skapats visas den nya spelboken i listan. Om den inte visas klickar du på knappen **Uppdatera**. När den visas klickar du på den för att börja redigera spelboken.

    ![Skapa en logikapp](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. **Logic App Designer** öppnas. Klicka på **Tom logikapp** för att skapa en ny spelbok. Du kan också välja **Säkerhet** under kategorierna och använda en av mallarna.
    
    ![Designer för logikappar](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. I fältet **Sök alla anslutningsappar och utlösare** skriver du *Azure Security Center* och väljer **När ett svar på en Azure Security Center-avisering utlöses**.

    ![Utlösare](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Nu kan du definiera vad som händer när du utlöser spelboken. Du kan lägga till en åtgärd, ett logiskt villkor, förhållanden för switch-fall eller slingor.

    ![Designer för logikappar](./media/security-center-playbooks/security-center-playbooks-fig5.png)
     
## <a name="how-to-run-a-security-playbook-in-security-center"></a>Hur kör man en säkerhetsspelbok i Security Center?

Du kan köra en säkerhetsspelbok i Security Center när du vill dirigera, hämta mer information från andra tjänster eller när du vill åtgärda ett problem. Gör så här för att öppna strategiböckerna:

1.  Öppna instrumentpanelen **Security Center**.
2.  I den vänstra rutan klickar du på **Säkerhetsincidenter och aviseringar** under **Hotidentifiering**.

    ![Aviseringar](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Klicka på aviseringen som du vill undersöka.
4.  Längst upp på aviseringssidan klickar du på knappen för att **köra strategiböcker**.

    ![Kör spelbok](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. På sidan med strategiböcker väljer du den bok som du vill köra och klickar på knappen **Kör**. Om du vill se spelboken innan den löses ut kan du klicka på den för att öppna designern.

    ![Strategiböcker](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Historik

När strategiboken har körts kan du även komma åt tidigare körningar och steg som innehåller mer information om status för strategiböcker som har körts tidigare. Historiken är sammanhangsbaserad efter aviseringar, vilket innebär att spelbokshistoriken som du ser är korrelerad till den avisering som utlöste denna spelbok. 

![Historik](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Om du vill se mer information om körningen av en viss spelbok kan du klicka på själva spelboken. Då visas logikappens körningssida med hela arbetsflödet.

![Information](./media/security-center-playbooks/security-center-playbooks-fig14.png)

I det här arbetsflödet kan du se hur lång tid det tog att köra varje uppgift och du kan utöka varje uppgift för att se resultatet. 

### <a name="changing-an-existing-playbook"></a>Ändra en befintlig spelbok

Du kan ändra en befintlig spelbok i Security Center för att lägga till åtgärder eller villkor. Om du vill göra det behöver du bara klicka på namnet på den strategibok som du vill ändra på fliken med strategiböcker, så öppnas Logic App Designer.

> [!NOTE]
> Om du vill ha mer information om hur du kan skapa en egen spelbok med hjälp av Azure Logic App kan du läsa [Skapa ditt första logikapparbetsflöde för att automatisera processer mellan molnappar och molntjänster](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app#add-an-action-that-responds-to-your-trigger).


## <a name="see-also"></a>Se även
I det här avsnittet har du fått lära dig hur du använder strategiböcker i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center. 
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

