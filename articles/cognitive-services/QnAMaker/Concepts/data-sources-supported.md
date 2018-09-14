---
title: Datakällor som stöds – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extrahera automatiskt frågor svar par från vanliga halvstrukturerade innehåll format, till exempel vanliga frågor och svar och produkthandböcker. Innehållet kan också läggas till i knowledge base från strukturerade filer.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 0436d5015ee4b369aeead7691f87ef313259d391
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544004"
---
# <a name="data-sources"></a>Datakällor 
QnA Maker extrahera automatiskt frågor svar par från vanliga halvstrukturerade innehåll format, till exempel vanliga frågor och svar och produkthandböcker. Innehållet kan också läggas till i knowledge base från strukturerade filer.

## <a name="plain-faq-pages"></a>Vanlig vanliga frågor och svar-sidor
Det här är den vanligaste typen av FAQ-sida, som svar direkt efter frågor på samma sida. 

![Vanlig FAQ-sida](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Vanliga frågor och svar sidor med avsnittet länkar 
I den här typen av FAQ-sida kan frågor räknas samman tillsammans och har kopplats till svar som finns i olika avsnitt på samma sida.

 ![Avsnittet länk FAQ-sida](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Vanliga frågor och svar sidor med länkar till olika sidor 
Den här typen av FAQ-sida liknar en länkad avsnittet vanliga frågor och svar sida, förutom att länkarna omdirigera till en annan sida. QnA Maker crawlar alla länkade sidor för att extrahera motsvarande svaren.

 ![Sidan för djuplänk vanliga frågor och svar](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Produkthandböcker

En manuell är vanligtvis anvisningar som medföljer en produkt. Det hjälper användaren att konfigurera, använda, underhålla och felsöka produkten. När QnA Maker bearbetar en manuell, extraheras rubriker och underrubriker som frågor och efterföljande innehållet som svar. Se ett exempel [här](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> Extrahering fungerar bäst på handböcker som har en tabell med innehåll och/eller en indexsida och en tydlig struktur med hierarkisk rubriker.


## <a name="structured-data-format-through-file-upload"></a>Formatet för strukturerade data med filuppladdning

Strukturerade filer, till exempel TSV .xlsx med formaterade kolumner kan också överföras till QnA Maker under skapande av kunskapsbas. Du kan också ladda upp filer från den **inställningar** fliken en kunskapsbas

| Fråga  | Svar  | Metadata                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | ' Key1:Value1|Key2:value2' |
| Question2 | Answer2 |      `Key:Value`           |
Alla övriga kolumner i källfilen ignoreras.

## <a name="structured-data-format-through-import"></a>Format för strukturerade data via import
Importera en kunskapsbas ersätter innehållet i den befintliga kunskapsbasen. Importera kräver en strukturerade TSV-fil som innehåller information om datakällan. Den här informationen hjälper QnA Maker gruppera frågor svar-par och attributet dem till en viss datakälla.

| Fråga  | Svar  | Källa| Metadata                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|' Key1:Value1|Key2:value2' |
| Question2 | Answer2 | Redigering|    `Key:Value`       |

## <a name="editorial"></a>Redigering
Om du inte har redan befintliga innehåll för att fylla i knowledge base, du kan också lägga till dem redigeringsmässigt i QnA Maker Knowledge base. Lär dig hur du uppdaterar din kunskapsbas [här](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ställ in QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Se också 

[Översikt över QnA Maker](../Overview/overview.md)
