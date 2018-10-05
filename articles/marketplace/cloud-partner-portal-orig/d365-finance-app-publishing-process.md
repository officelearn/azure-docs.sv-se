---
title: App som publicerar steg | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 0fc82229e158ed35b97203b11d08841c683c45ce
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811542"
---
<a name="app-publishing-steps"></a>App som publicerar steg
====================

Starta publiceringsprocessen ska du klicka på ”Publicera” under fliken redigerare.

![CPP publicera App-knappen](./media/d365-financials/image014.jpg)


Under fliken Status visas Publishing stegen som anger där ditt erbjudande är i publiceringsprocessen. När som helst i publiceringsprocessen kan också logga in och klicka på fliken alla erbjuder om du vill visa den senaste statusen för någon av dina erbjudanden. Du kan klicka direkt på status för ditt erbjudande och se informationen om där ditt erbjudande är i publiceringsprocessen.

Låt oss gå igenom varje publishing steg, diskutera vad som händer i varje steg och hur länge du beräkna varje steg tar.

![Publicera processdiagram](./media/d365-financials/image017.png)


**Verifiera förutsättningar**

När du klickar på ”Publicera” tar en automatisk kontroll som ser till att du har fyllt i de obligatoriska fälten på ditt erbjudande. Om inga fält inte är ifylld, visas en varning bredvid fältet och du måste fylla i det korrekt Klicka på ”publicera” igen.

När du har slutfört det här steget korrekt, visas ett popup-fönster där du får en e-postadress som ska användas för att skicka du publicerar meddelanden. När du skickar in din e-postadress, har det här steget slutförts.


**Automatisk verifiering**

I det här steget kontrollerar vår tjänst för automatiska certifiering programtillägg som medföljer ett erbjudande som sitt innehåll överensstämmer med erbjuder metadata. Alltid se till att ditt namn, version, utgivare och ID: T matchar med i tilläggsmanifestet med namnet `app.json`.


**Test Drive-verifiering**

Om du har valt för att ställa in Test Drive är det här skedet där din Test Drive-inställningarna verifieras.


**Leda management verifiering och registrering**

Om du har konfigurerat funktionen leda Generation under det här steget ska vi Kontrollera att din CRM-integrering fungerar genom att skicka ett test-lead till din CRM. Du kan se en post med falska data fylla i din CRM eller Azure-tabellen när det här steget har slutförts. All dokumentation för leda Generation finns här.


**AppSource-paketering**

Artefakterna Storefront information kontrolleras och AppSource förhandsversion paketet skapas.


**Publisher-utloggning**

Under det här steget i **Go Live** knappen kommer nu att bli aktiv. Du får nu även en länk du förhandsgranskar ditt erbjudande (med din hidekey). När du är nöjd med hur ser ut i förhandsgranskningen kan du klicka på knappen Go Live.
Tänk på, den här begäran inte gör din app live på Appsource, men i stället utlöser våra interna verifieringsprocessen.


**Marknadsföring och teknisk tillämpning verifiering**

Det här steget är där vi Genomför marknadsföring och tekniska verifieringar parallellt. Referera till den [checklista för att skicka in din App](https://aka.ms/CheckBeforeYouSubmit) och [utveckla appar för Dynamics 365 för Finance and Operations faktablad](https://go.microsoft.com/fwlink/?linkid=841518) vägledning dokumenten för obligatoriska kraven och rekommendationerna. Under verifieringsprocessen, kommer vi att:
-  Arbeta med dig med alla utestående frågor och problem.  
- Du får en app publiceringsdatum och meddelar dig när din app har publicerats. 
- ger dig en första feedback om tekniska och marknadsföring verifieringen inom 5 – 7 arbetsdagar.

De här stegen kan vanligtvis ta över en vecka och det finns inget behov av att du är inloggad kontinuerligt till partnerportalen i molnet.


**Publicera program med tjänsten**

Ditt erbjudande genomgår viss slutlig behandling. Din app har passerat både marknadsföring och teknisk verifiering, men nu gå igenom viss slutlig behandling så att den kan för App-källa.


**Live**

Kunder kommer att kunna visa och distribuera din app i Microsoft Dynamics 365 Business Central prenumerationerna ditt erbjudande är nu Live på AppSource. Du får ett e-postmeddelande från oss om att din app har gjorts offentliga på Appsource. När som helst kan du klicka på fliken alla erbjudanden och se status för alla dina erbjudanden som visas på den högra kolumnen. Du kan klicka på status för att se publiceringsstatus för flow i detalj för ditt erbjudande.


<a name="error-handling"></a>Felhantering
--------------

Under publiceringsprocessen, kan ett fel påträffas. Om ett fel inträffar, får du ett e-postmeddelande som informerar dig om att ett fel uppstod med anvisningar om nästa steg. Du kan också se fel när som helst under den här processen genom att klicka på fliken Status. Du ser då välja i processen för felet inträffade tillsammans med ett felmeddelande som beskriver vad som behöver lösas.

Om det uppstår fel under publiceringsprocessen du krävs för att åtgärda dessa fel och klicka sedan på **publicera** att starta om processen. Du måste starta i början av publishing stegen i **Verifiera förutsättningar** när publicera efter eventuella problem.

Om du har problem med hur du löser ett fel, ska du öppna en supportbegäran om att få hjälp från våra supporttekniker.


<a name="canceling-the-publishing-request"></a>Publicera förfrågan avbröts
--------------------------------

Du kan starta processen med att publicera och behöver du avbryta din begäran. Du kan bara avbryta en publiceringsbegäran när publiceringsbegäran når Publisher Kvittering steg. Om du vill avbryta, klickar du på **Avbryt publicering**. Publiceringsstatus återgår till steg 1 och för att publicera igen, ska du klicka på Publicera och följer du stegen i status.

![Publicera knappen Avbryt](./media/d365-financials/image013.png)
