---
title: "Testa din Data tjänsterbjudande på Marketplace | Microsoft Docs"
description: "Förstå hur du testar dina Data Service-erbjudande för Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e861bd11-f74d-4d77-b4b5-23fb463644ad
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 56a8aad7484fed18b74200ffa7acf22363625a15
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="testing-your-data-service-offer-in-staging"></a>Testa din Data tjänsterbjudande i Förproduktion
> [!IMPORTANT]
> **Just nu vi inte längre onboarding några nya Data Service-utgivare. Nya dataservices kommer inte godkännas för lista.** Om du har ett SaaS-affärsprogram som du vill publicera på AppSource hittar du mer information [här](https://appsource.microsoft.com/partners). Om du har en IaaS-program eller developer-tjänsten som du vill publicera på Azure Marketplace du hittar mer information [här](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

När du har slutfört de två första stegen av [skapar Microsoft Developer-konto](marketplace-publishing-accounts-creation-registration.md) och [skapar ditt Data Service erbjuder i Publishing Portal](marketplace-publishing-data-service-creation.md) är du redo för att göra erbjudandet tillgängliga i Azure Marketplace. Det här avsnittet vägleder dig genom de första, mellanliggande steg som kallas ”mellanlagring”

Mellanlagring innebär distribuera erbjudandet i ett privat ”sandbox” där du kan testa och verifiera dess funktioner innan du skickar den till produktion. Erbjudandet visas i Förproduktion precis som när en kund som har distribuerat den.

## <a name="step-1-pushing-your-offer-to-staging"></a>Steg 1. Push-överföring erbjudandet till Förproduktion
Push-överföring till Förproduktion erbjudandet kan du testa erbjudandet innan den blir tillgänglig för framtida prenumeranter.  Du kan se hur erbjudandet visas och fungerar för de prenumerera på dina data.  

  ![Rita](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1. Logga in på den [publicering av portalen](https://publish.windowsazure.com)
2. Välj **datatjänster** i fönstret vänstra navigeringsfönstret
3. Välj ditt erbjudande som du vill skicka till Förproduktion. Du ser skärmen ovan.
4. Klicka på **Push till Förproduktion** knappen.  
5. Om det finns problem med erbjudandet som behövs för att slutföras innan du sänder till mellanlagring, visas en lista över.  Korrigera objekten genom att klicka på varje objekt i listan. När alla ändringar som gjorts, klickar på **Push till Förproduktion** igen.

Om det inte finns några problem med erbjudandet visas i popup-fönstret nedan.  

Om du är inte planera ej godkänt för att ansluta erbjudandet i Azure-portalen (för närvarande har begränsad kapacitet), bara stänga popupfönstret.

Om du vill testa tjänsten Data i Azure-portalen (förutom DataMarket portal), behöver du ett Azure-prenumerationens ID att testa med.  Prenumerations-ID identifierar det konto som ska kunna testa erbjudandet.  

Klipp ut och klistra in ditt prenumerations-ID och klicka på bockmarkeringen för att fortsätta.

  ![Rita](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [!NOTE]
> Dessa ID: N för Azure-prenumerationer är bara krävs för att testa och mellanlagring i den [Azure-hanteringsportalen](https://manage.windowsazure.com). De krävs inte för att testa i Azure Marketplace.
> 
> 

Nästa skärm som visar att publicering sker genom att visa ”pågående”-ikonen visas markerad gul nedan. Push-överföring till Förproduktion tar mellan 10 – 15 minuter.  Om det tar längre tid först uppdatera webbläsaren (tryck på F5 i IE).  I sällsynta fall där erbjudandet fortfarande pushar till Förproduktion efter en timme, klickar du på länken Kontakta oss att berätta för oss att det finns ett problem.

  ![Rita](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

När Push till Förproduktion har slutförts ”pågående”-ikonen ska stoppa flytta och status uppdateras till ”mellanlagrad”.  Du är nu redo att testa ditt erbjudande.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Steg 2. Testa mellanlagrade erbjudandet i DataMarket
Klickar du på följande texten **”se din tjänst som erbjuder i...”** på skärmen att prenumeranten kommer att se när erbjudandet går till produktion och visas i DataMarket.

  ![Rita](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Testa eller verifiera 12 objekten markerad ovan för alla logotyper, priser-transaktioner, text, bilder, dokumentation och länkar är korrekta och fungerar korrekt.  Det här är ett bra tillfälle att se till att alla testvärden som du angav när du skapar din prenumeration har ersatts med faktiska värden.

1. Logotyp för erbjudande
2. Erbjudandenamn
3. Utgivarens namn/länk till ditt företags webbplats
4. Sök efter kategorier för ditt erbjudande
5. Länk till ditt erbjudande support att hjälpa prenumeranter
6. Kontextuella beskrivning för ditt erbjudande
7. Erbjudande plan illustrerar faktureringsinformation
8. Länka till implementeringskod
9. Exempel bilder som illustrerar användning av erbjudandet data
10. I/o-metadata för varje tjänst i erbjudandet
11. Erbjudandet, villkor för användning
12. Förhandsgranskning av det erbjudande data

Kontrollera slutligen tjänsten fungerar via Datamarket genom att klicka på länken ”Utforska den här DATAUPPSÄTTNINGEN”.  Öppnar ett nytt fönster i verktyget vi kallar ”Service Explorer” så att du kan förhandsgranska resultaten av en fråga mot din tjänst.  I det här fönstret kan du ange parametrar som behövs och se resultaten från en fråga till tjänsten visas.   Dessutom är visas URL-Adressen för din fråga.  

> [!NOTE]
> Se till att granska textbeskrivning av tjänsten visas längst upp.  Och om erbjudandet består av fler än en tjänst anropa, klicka på flikarna längst ned för att växla till nästa service för att granska och testa.
> 
> 

## <a name="next-step"></a>Nästa steg
Om du har problem och behöver hjälp med att lösa dem. Kontakta [Azure utgivarens Support](http://go.microsoft.com/fwlink/?LinkId=272975).

Om du är nöjd och redo att publicera erbjudandet Läs den [begäran om godkännande Push till produktion](marketplace-publishing-push-to-production.md) dokumentation.

## <a name="see-also"></a>Se även
* [Komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

