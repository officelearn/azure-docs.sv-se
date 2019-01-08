---
title: Skapa din virtuella dator som erbjudandet live på Azure Marketplace
description: Skapa din virtuella dator som erbjudandet live på Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ad22f1944d3fe9a088b66da4cf4df7136db497f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075365"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Skapa din virtuella dator som erbjudandet live på Azure Marketplace
=========================================================

När du har fyllt i alla informationen i erbjudandet, är det dags att publicera ditt erbjudande och göra den aktiv på Azure Marketplace. Det finns några steg erbjudandet går igenom. Kontrollera att både ditt marknadsföringsinnehåll och dina VM-avbildning som uppfyller kvalitetskraven, för att vara Azure Certified och publiceras på webbplatsen.

![Erbjud gå Live sekvensen 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

Vi ska gå igenom den här processen i större detalj för att bättre förstå vad som händer under varje steg. I den här processen behöver du vidta åtgärder för att säkerställa att ditt erbjudande fortsätter att se förloppet.

<a name="publishing-process"></a>Process för att publicera
------------------

Klicka på ”Publicera” under fliken redigerare för att starta publiceringsprocessen.

![Erbjuder gå Live sekvens 1 – publicera](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

Under fliken Status visas Publishing stegen och där ditt erbjudande är i processen.

![Erbjud Go Live serie 2 - status](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

När som helst i publiceringsprocessen kan också logga in och klicka på fliken alla erbjuder om du vill visa den senaste statusen för någon av dina erbjudanden. Du kan klicka direkt på status för ditt erbjudande och se informationen om där ditt erbjudande är i publiceringsprocessen.

![Erbjuder Go Live sekvens 3 – alla erbjuder status](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

Låt oss gå igenom varje publishing steg, diskutera vad som händer i varje steg och hur länge du beräkna varje steg tar.

**Verifiera förutsättningar (\<1 dag)**

När du klickar på ”Publicera” tar en automatisk kontroll som ser till att du har fyllt i de obligatoriska fälten på ditt erbjudande. Om inga fält inte är ifylld, visas en varning bredvid fältet och du måste fylla i det korrekt Klicka på ”publicera” igen.

När du har slutfört det här steget korrekt, uppmanas ett popup-fönster för en e-postadress. Det här är den e-postadress som du får publishing Statusmeddelandena för resten av publiceringsprocessen. När du skickar in din e-postadress, har det här steget slutförts.

![Erbjudandet går Live sekvens 4 – publicera ditt erbjudande](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**Certifiering (\<5 dagar)**

Det här steget är där vi kör flera tester för att säkerställa att din avbildning uppfyller kraven för Azure Certified. De riktlinjer som du måste se till att du skickar certifieringskraven är [här](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md).

Eftersom det här steget kan ta flera dagar, kan du logga ut från partnerportalen i molnet. Vi skickar ett e-postmeddelande om det finns några fel som behöver åtgärdas. Om allt går med framgång ska processen automatiskt gå vidare till steg för etablering.

**Etablering (\<1 dag)**

Under det här steget vi replikerar dina avbildningar till alla globala Azure-datacenter och kan ta upp till en dag att slutföra.

**Paketering och leda Generation registrering (\<1 timme)**

Under det här steget kombinerar vi teknik- och innehållet i vad blir sidan på webbplatsen.

Även om du har konfigurerat funktionen leda Generation, kommer vi att verifiera att din CRM-integrering fungerar genom att skicka ett test-lead till din CRM. Du kan se en post med falska data fylla i din CRM eller Azure-tabellen när det här steget har slutförts. All dokumentation för leda Generation finns [här](./cloud-partner-portal-get-customer-leads.md).

**Erbjudandet är tillgängligt i förhandsversionen**

Du får ett e-postmeddelande som ditt erbjudande har slutfört de steg som krävs för att komma åt erbjudandet i förhandsversion. Det här steget ska du förhandsgranskar ditt erbjudande och kontrollera att allt ser ut som den ska vara och som den virtuella datorn distribueras korrekt i mellanlagringsmiljön.

*Godkänd-prenumerationer kan göra den här kontrollen.*

**Publisher-signering**

När du har kontrollerat allt ser korrekt ut och fungerar korrekt i förhandsversion, är du klar för livesändning. Klicka på **Go Live** under den **Status** fliken och vi börjar stegen så att ditt erbjudande live i produktion och på webbplatsen. Vanligtvis tar det flera timmar från det att du klickar på Go Live och när erbjudandet är aktiv på webbplatsen. Vi skickar ett e-postmeddelande när ditt erbjudande är officiellt live på webbplatsen.

![Erbjuder Go Live sekvens 5 – gå live](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**Live**

Erbjudandet är nu Live i Azure Marketplace och Azure portal och kunder kommer att kunna visa och distribuera den virtuella datorn i sina Azure-prenumerationer. När som helst kan du klicka på fliken alla erbjudanden och se status för alla dina erbjudanden som visas på den högra kolumnen. Du kan klicka på status för att se publiceringsstatus för flow i detalj för ditt erbjudande.

<a name="error-handling"></a>Felhantering
--------------

Under publiceringsprocessen, kan ett fel påträffas. Om ett fel inträffar, får du ett e-postmeddelande som informerar dig om att ett fel uppstod med anvisningar om nästa steg. Du kan också se fel när som helst under den här processen genom att klicka på fliken Status. Du ser då välja i processen för felet inträffade tillsammans med ett felmeddelande som beskriver vad som behöver lösas.

![Erbjud Go Live sekvens 6 - felmeddelande](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

Om det uppstår fel under publiceringsprocessen krävs du för att åtgärda dessa problem, och klicka sedan på ”Publicera” om du vill starta om processen. Du måste starta i början av publishing stegen i Validera krav när du publicerar efter eventuella problem.

Om du har problem med hur du löser ett fel, ska du öppna en supportbegäran om att få hjälp från våra supporttekniker.

![Erbjuder Go Live sekvens 7 – få support](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>Publicera förfrågan avbröts
--------------------------------

Du kan starta processen med att publicera och behöver du avbryta din begäran. Du kan bara avbryta en publiceringsbegäran när publiceringsbegäran når Publisher Sign-off-steget. Om du vill avbryta, klickar du på Avbryt publicering. Publiceringsstatus återgår till steg 1 och för att publicera igen, ska du klicka på Publicera och följer du stegen i status.

![Erbjud Go Live sekvens 8 - status](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

