---
title: URL-typer som stöds för import-QnA Maker
description: 'Förstå hur olika typer av URL: er används för att importera och skapa QnA-uppsättningar.'
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651832"
---
# <a name="urls-supported-for-importing-documents"></a>URL: er som stöds för att importera dokument

Förstå hur olika typer av URL: er används för att importera och skapa QnA-uppsättningar.

## <a name="faq-urls"></a>Vanliga frågor och svar-URL: er

QnA Maker kan stödja vanliga frågor och svar webb sidor i tre olika former:

* Vanlig vanliga frågor och svar-sidor
* Vanliga frågor och svar sidor med länkar
* Vanliga frågor och svar med en sida med ämnes sidor

### <a name="plain-faq-pages"></a>Vanlig vanliga frågor och svar-sidor

Det här är den vanligaste typen av FAQ-sida, som svar direkt efter frågor på samma sida.

Nedan visas ett exempel på en vanlig FAQ-sida:

![Vanlig vanliga frågor och svar-sida-exempel för en kunskapsbas](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Vanliga frågor och svar sidor med länkar

I den här typen av FAQ-sida kan frågor räknas samman tillsammans och har kopplats till svar som finns i olika avsnitt i samma sida eller i olika sidor.

Nedan visas ett exempel på en FAQ-sida med länkar i avsnitten som finns på samma sida:

 ![Avsnittet vanliga frågor och svar länk sidan exempel för en kunskapsbas](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Sidan för överordnad ämnes sida länkar till underordnade svars sidor

Den här typen av vanliga frågor och svar innehåller en avsnitts sida där varje ämne är länkat till en motsvarande uppsättning frågor och svar på en annan sida. QnA Maker crawla alla länkade sidor för att extrahera motsvarande frågor & svar.

Nedan visas ett exempel på en ämnes sida med länkar till FAQ-avsnitt på olika sidor.

 ![Vanliga frågor och svar för djuplänk sidan exempel för en kunskapsbas](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Support-URL: er

QnA Maker kan bearbeta delvis strukturerade support webb sidor, till exempel webb artiklar som beskriver hur du utför en specifik uppgift, hur du diagnostiserar och löser ett specifikt problem och vilka metoder som är bästa praxis för en specifik process. Extraktion fungerar bäst på innehåll som har en tydlig struktur med hierarkiska rubriker.

> [!NOTE]
> Extrahering av support artiklar är en ny funktion och är i tidiga steg. Det fungerar bäst för enkla sidor, som är väl strukturerade och inte innehåller komplexa sidhuvuden och sid fötter.

![QnA Maker stöder extrahering från halv strukturerade webb sidor där en tydlig struktur visas med hierarkiska rubriker](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)