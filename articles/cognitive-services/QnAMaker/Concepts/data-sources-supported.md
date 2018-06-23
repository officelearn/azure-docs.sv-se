---
title: Datakällor stöds - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Datakällor som stöds
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354102"
---
# <a name="data-sources"></a>Datakällor 
Frågor och svar om Maker extraherar automatiskt frågor svar par från vanliga halvstrukturerade innehåll format, till exempel vanliga frågor och produkthandböcker. Innehållet kan också läggas till i kunskapsbasen från strukturerade filer.

## <a name="plain-faq-pages"></a>Vanlig FAQ-sidor
Det här är den vanligaste typen av sida med vanliga frågor, där svaren följa omedelbart efter frågor på samma sida. 

![Enkel sida med vanliga frågor](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Vanliga frågor och svar sidor med avsnittet länkar 
Frågor aggregeras tillsammans i den här typen av sida med vanliga frågor och är kopplade till svar som finns i olika avsnitt på samma sida.

 ![Sida med avsnittet länken vanliga frågor](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Vanliga frågor och svar sidor med länkar till olika sidor 
Den här typen av sida med vanliga frågor liknar en avsnittet länkade vanliga frågor och svar sida, förutom att länkarna omdirigera till en annan sida. Frågor och svar om Maker crawlar länkade sidor för att extrahera motsvarande svar.

 ![Sidan djuplänk vanliga frågor och svar](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Produkthandböcker

En manuell är vanligtvis anvisningar som medföljer en produkt. Det hjälper användare att konfigurera, använda, underhålla och felsöka produkten. När frågor och svar om Maker bearbetar en manuell, extraheras rubriker och nummer som frågor och efterföljande innehållet som svar. Se ett exempel [här](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> Extrahering fungerar bäst på handböcker som har en tabell med innehållet och/eller en indexsida och en tydlig struktur med hierarkiska rubriker.


## <a name="structured-data-format-through-file-upload"></a>Format för strukturerade data via ladda upp filen

Strukturerade filer, till exempel TSV .xlsx med formaterad kolumner kan också överföras till frågor och svar om Maker under skapande av knowledge base. Du kan också ladda upp filer från den **inställningar** fliken för en kunskapsbas

| Fråga  | Svar  | Metadata                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | `Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 |      `Key:Value`           |
Alla övriga kolumner i källfilen ignoreras.

## <a name="structured-data-format-through-import"></a>Format för strukturerade data via import
Importera en kunskapsbas ersätter innehållet i den befintliga knowledge basen. Importera kräver en strukturerad TSV-fil som innehåller information om datakällan. Den här informationen hjälper frågor och svar om Maker gruppera frågor svar-par och attributet dem till en specifik datakälla.

| Fråga  | Svar  | Källa| Metadata                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|`Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 | Redigering|    `Key:Value`       |

## <a name="editorial"></a>Redigering
Om du inte har befintliga innehåll för att fylla i knowledge base om du kan också lägga till dem redigeringsmässigt i frågor och svar om Maker Knowledge base. Lär dig hur du uppdaterar kunskapsbasen [här](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera en tjänst för frågor och svar om Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Se också 

[Frågor och svar om Maker översikt](../Overview/overview.md)