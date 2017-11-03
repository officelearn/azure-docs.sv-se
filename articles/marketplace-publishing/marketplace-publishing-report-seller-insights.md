---
title: "Förstå Azure Marketplace användningsbaserad rapporter och säljare insikter reporting | Microsoft Docs"
description: "Förstå användningsbaserad rapporten, kallas även en rapport för säljare-insights som en säljare på Azure Marketplace"
services: Azure Marketplace
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: v-jeana; hascipio; v-dabosl
ms.openlocfilehash: e098e27e32f7b7ae2009580a430f262aa7225206
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-seller-insights-report"></a>Förstå säljare insikter rapporten
**Vad är säljare insikter?**

Alla virtuella datorn och användningsbaserad fakturering developer services utgivare får Månadsrapporter från Microsoft om sina erbjudanden genererar användning.

**Hur får jag?**

* **Välkomstmeddelandet:** som en ny utgivare, du får välkomstmeddelandet som meddelar dig om att du erhåller Månadsrapporter för säljare insikter.
* **Månatliga försäljningsrapporter:** om du har användning, får du ett andra e-postmeddelande som innehåller rapporter och anvisningar om hur du kommer åt ditt lösenord:

  * Om du har en virtuell dator eller användningsbaserad fakturering developer tjänster SKU som genererar användning, visas månatliga rapporten information om order, användning, marknader och kunduppgifter för dina erbjudanden.
  * Rapporter är låsta med ett lösenord som endast du och Microsoft veta för att skydda dina kunders data.
  * Microsoft kommer inte att skicka en rapport om ingen av dina erbjudanden har genererat användning under månaden.

## <a name="understand-your-seller-insights-report"></a>Förstå säljare insikter rapporten
**Order under SKU, och licens: Marknadsplatsorder fliken**

![readingreportbyorders][2]

* Utsnitt att filtrera rapporten efter varje element.
* Ett diagram som visar månatliga order per Azure licenstypen. Varje stapel visar totalt order nedbrutna per Azure licenstyp för den månaden.
* Ett diagram som visar månatliga order av SKU. Varje visas totala månatliga order för alla SKU: er, nedbrutna per SKU.
* Ett diagram visar månatliga trend order genom Azure licenstyp och licenstyp för Azure Marketplace.
* Cirkeldiagram visar order genom Azure licenstyp och Marketplace licenstypen.
* En tabell visar totala månatliga order per Marketplace licenstypen, månatliga summor och kumulativa funktioner för alla månader.

**Order under SKU, och licens: Marketplace användning fliken**

![readingreportbyusage][3]

* Utsnitt att filtrera rapporten efter varje element.
* Du bör välja användning av VM-normaliserat eller raw användning.
* Ett diagram som visar månatlig användning av Azure licenstypen. Varje visas total användning för den månaden nedbrutna per Azure licenstypen.
* Ett diagram som visar månatlig användning av SKU. Varje stapel visar total månatlig användning för alla SKU: er, nedbrutna per SKU.
* Ett diagram som visar månatliga trend för användning av Azure licenstyp och Marketplace licenstypen.
* Cirkeldiagram visar användning av Azure licenstyp och Marketplace licenstypen.
* En tabell visar total månatlig användning av Marketplace licenstypen, månatliga summor och kumulativa funktioner för alla månader.

**Flikarna ordning Data- och användningsdata**

De här flikarna ger detaljerad information som används för att generera rapporten.

![orderdata][4]

![usagedata][5]

**Användning av SKU och licens: kunder fliken**

![customerstab][6]

* Observera sekretess-satsen.
* Den här fliken innehåller en lista över kunder genom SKU profilinformation, transaktionsdatum och välja i erbjudanden kontakta.
* Rapporten innehåller ordning antal vid SKU och funktioner.

**Juridiska FRISKRIVNINGAR**

![legal][1]

Läs de juridiska FRISKRIVNINGAR noggrant. Om du har några frågor eller feedback, klicka på länken längst ned i FRISKRIVNINGAR ska omdirigeras till sidan Marketplace-stöd.

## <a name="request-a-password-reminder"></a>Begär en Lösenordspåminnelse
Navigera till https://publish.windowsazure.com/ och logga in med ditt Microsoft-kontouppgifter.
![passwordreminder][7]

Välj den **utgivare** fliken ![selectpublisherstab][8]

Hitta Publicerings-ID i URL-Adressen:

* Använda detta ID som lösenord för att öppna Excel-filen säljare insikter.
  Det här är ditt lösenord tills vidare.
* Vi rekommenderar att du använder Microsoft Office 2013 med Windows som läsaren arbetsboken om du väljer.  Vissa användare har rapporterat problem med att använda Microsoft Office för Mac.

![publisherid][9]

## <a name="next-steps"></a>Nästa steg
Om du har frågor om rapporter och insikter kontaktar du supportteamet:

1. Gå till sidorna support på https://publish.windowsazure.com/.
2. I den **problemtyp** väljer **rapporter och insikter**.
3. I den **kategori** väljer **frågor som rör rapporter**.
4. Klicka på **startbegäran**.
   ![sellerinsightsquestions][10]

[1]: ./media/marketplace-publishing-report-seller-insights/legal.png
[2]: ./media/marketplace-publishing-report-seller-insights/readingreportbyorders.png
[3]: ./media/marketplace-publishing-report-seller-insights/readingreportbyusage.png
[4]: ./media/marketplace-publishing-report-seller-insights/orderdata.png
[5]: ./media/marketplace-publishing-report-seller-insights/usagedata.png
[6]: ./media/marketplace-publishing-report-seller-insights/customerstab.png
[7]: ./media/marketplace-publishing-report-seller-insights/passwordreminder.png
[8]: ./media/marketplace-publishing-report-seller-insights/selectpublisherstab.png
[9]: ./media/marketplace-publishing-report-seller-insights/publisherid.png
[10]: ./media/marketplace-publishing-report-seller-insights/sellerinsightsquestions.png
