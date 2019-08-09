---
title: Så här dokumenterar du data källor i Azure Data Catalog
description: Instruktions artikel för att markera hur du ska dokumentera data till gångar i Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 5c0bc97e5a5b362ea78f82ea99bb967a9607093a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882493"
---
# <a name="document-data-sources"></a>Dokumentera datakällor
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad moln tjänst som fungerar som ett system för registrering och identifierings system för företags data källor. Med andra ord är **Azure Data Catalog** att hjälpa människor att upptäcka, *förstå*och använda data källor och hjälpa organisationer att få mer värde än befintliga data.

När en data källa registreras med **Azure Data Catalog**kopieras och indexeras dess metadata av tjänsten, men artikeln slutar inte där. **Azure Data Catalog** ger också användarna möjlighet att ange sin egen fullständiga dokumentation som kan beskriva användningen och vanliga scenarier för data källan.

När du [kommenterar data källor](data-catalog-how-to-annotate.md), lär du dig att experter som vet att data källan kan kommentera den med taggar och en beskrivning. **Azure Data Catalog** -portalen innehåller en RTF-redigerare så att användarna helt kan dokumentera data till gångar och behållare. Redigeraren innehåller styckeformatering, till exempel rubriker, textformatering, punkt listor, numrerade listor och tabeller.

Taggar och beskrivningar är fantastiska för enkla anteckningar. Men för att hjälpa data konsumenter att bättre förstå användningen av en data källa och affärs scenarier för en data källa kan en expert tillhandahålla fullständig, detaljerad dokumentation. Det är enkelt att dokumentera en data källa. Välj en data till gång eller behållare och välj **dokumentation**.

![Fliken dokumentation i en Data Catalog](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentera data till gångar
Fördelen med **Azure Data Catalog** dokumentation gör att du kan använda din Data Catalog som en innehålls lagrings plats för att skapa en fullständig text av dina data till gångar. Du kan utforska detaljerad information som beskriver behållare och tabeller. Om du redan har innehåll i en annan innehålls lagrings plats, till exempel SharePoint eller en fil resurs, kan du lägga till i till gångs dokumentations länkarna för att referera till det befintliga innehållet. Den här funktionen gör dina befintliga dokument mer synliga.

> [!NOTE]
> Dokumentationen ingår inte i Sök indexet.
>

![Fliken dokumentation och hyperlänken till webb länken](media/data-catalog-documentation/data-catalog-documentation2.png)

Informations nivån kan variera från att beskriva egenskaperna och värdet för en data till gångs behållare till en detaljerad beskrivning av tabell schema i en behållare. Vilken dokumentations nivå som tillhandahålls bör drivas av dina affärs behov. Men i allmänhet är det några få och nack delar med att dokumentera data till gångar:

* Dokumentera bara en behållare: Allt innehåll är på ett ställe, men det kan hända att användare saknar nödvändig information för att användarna ska kunna fatta ett välgrundat beslut.
* Dokumentera bara tabellerna: Innehållet är bara för det objektet, men användarna har flera platser för dokument.
* Dokument behållare och tabeller: Mest omfattande metod, men kan introducera mer underhåll av dokumenten.

## <a name="summary"></a>Sammanfattning
Genom att dokumentera data källor med **Azure Data Catalog** kan du skapa en berättelse om dina data till gångar i så mycket information som du behöver.  Med hjälp av länkar kan du länka till innehåll som lagras i en befintlig innehålls lagrings plats, vilket ger dina befintliga dokument och data till gångar tillsammans. När användarna har identifierat lämpliga data till gångar kan de ha en fullständig uppsättning dokumentation.
