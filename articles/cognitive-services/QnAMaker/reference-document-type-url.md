---
title: Url-typer som stöds för import - QnA Maker
description: Förstå hur typerna av webbadresser används för att importera och skapa QnA-uppsättningar.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651832"
---
# <a name="urls-supported-for-importing-documents"></a>Url:er som stöds för import av dokument

Förstå hur typerna av webbadresser används för att importera och skapa QnA-uppsättningar.

## <a name="faq-urls"></a>Vanliga frågor och svar webbadresser

QnA Maker kan stödja vanliga och svar webbsidor i 3 olika former:

* Vanliga frågor och svar sidor
* Vanliga frågor och svar sidor med länkar
* Vanliga frågor och svar sidor med en topics hemsida

### <a name="plain-faq-pages"></a>Vanliga frågor och svar sidor

Detta är den vanligaste typen av FAQ-sida, där svaren omedelbart följer frågorna på samma sida.

Nedan är ett exempel på en vanlig FAQ-sida:

![Vanligt sidexempel på vanliga frågor för en kunskapsbas](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Vanliga frågor och svar sidor med länkar

På den här typen av vanliga frågor och svar sammanställs frågor tillsammans och länkas till svar som antingen finns i olika avsnitt på samma sida eller på olika sidor.

Nedan följer ett exempel på en sida med vanliga frågor och svar med länkar i avsnitt som finns på samma sida:

 ![Vanliga frågor och svar på sidan Avsnittslänk för en kunskapsbas](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Sidlänkar till underordnade svarssidor för överordnade ämnen

Den här typen av vanliga frågor och svar har en informationssida där varje ämne är länkat till en motsvarande uppsättning frågor och svar på en annan sida. QnA Maker genomsöker alla länkade sidor för att extrahera motsvarande frågor & svar.

Nedan följer ett exempel på en informationssida med länkar till vanliga frågor och svar på olika sidor.

 ![Vanliga frågor och svar på djuplänk för en kunskapsbas](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Stöd webbadresser

QnA Maker kan bearbeta halvstrukturerade supportwebbsidor, till exempel webbartiklar som beskriver hur du utför en viss uppgift, hur du diagnostiserar och löser ett visst problem och vilka är de bästa metoderna för en viss process. Extrahering fungerar bäst på innehåll som har en tydlig struktur med hierarkiska rubriker.

> [!NOTE]
> Extraktion för supportartiklar är en ny funktion och är i ett tidigt skede. Det fungerar bäst för enkla sidor, som är väl strukturerade, och inte innehåller komplexa sidhuvuden / sidfötter.

![QnA Maker stöder extrahering från halvstrukturerade webbsidor där en tydlig struktur presenteras med hierarkiska rubriker](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)