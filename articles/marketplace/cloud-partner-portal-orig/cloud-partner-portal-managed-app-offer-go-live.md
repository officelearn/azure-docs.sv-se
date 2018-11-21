---
title: Gör ditt Azure-program som erbjudandet live på Azure Marketplace | Microsoft Docs
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
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: 18a8e6ae8ab3bd4299c6a014f938e73a2a021492
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263603"
---
<a name="make-your-azure-application-offer-live-on-azure-marketplace"></a>Gör ditt Azure-program som erbjudandet live på Azure Marketplace 
===========================================================

Du\'stött fylls i erbjudandeinformationen den\'s dags att publicera ditt erbjudande och gör det publicerats på Azure Marketplace. Det finns några steg, kontrollera att både ditt marknadsföringsinnehåll och ditt program som uppfyller kvalitetskraven vara Azure Certified, innan de aktiveras på webbplatsen.

![Publicera flöde](./media/cloud-partner-portal-publish-managed-app/publish_flow.png)

Låt\'s går igenom den här processen i större detalj för att bättre förstå vad som händer under varje steg och uppgifter i den här processen att se till att ditt erbjudande fortsätter att se förloppet.

<a name="publishing-process"></a>Process för att publicera 
------------------

Du kan klicka på \"publicera\" under den **redigeraren** flik för att starta publiceringsprocessen.

![Erbjuder Go Live serie 1 – publicera](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish.png)

Under den **Status** fliken visas Publishing stegen och vilket steg ditt erbjudande är på.

![Erbjuder Go Live serie 2 - arbetsflöde](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_workflow.png)

När som helst i publiceringsprocessen kan du också logga in och klicka på den **alla erbjuder** fliken för att visa den senaste statusen för någon av dina erbjudanden. Du kan klicka direkt på status för ditt erbjudande och se informationen om där ditt erbjudande är i publiceringsprocessen.

> [!WARNING]
> Om kunden användning attribution inte är aktiverad, visas följande felmeddelande under publiceringsprocessen: ”Azure-partner kundens användning attribution saknas i en eller flera Azure Resource Manager-mallar. Lös genom att lägga till en spårning GUID för Azure Resource Manager-mallen i mainTemplate.json paket för följande plan: servicenow. Mer information finns i http://aka.ms/customerusageattribution”. 

Nu ska vi gå igenom varje publishing steg och diskutera vad som händer i varje steg och hur länge du beräkna varje steg tar.

### <a name="validate-prerequisites-1-day"></a>Verifiera förutsättningar (\<1 dag) 

När du klickar på \"publicera\", automatiska kontroller kommer att äga rum för att säkerställa att du\'stött fylls i alla obligatoriska fält i ditt erbjudande. Om alla fält inte har fyllts i, visas en varning bredvid fältet och du måste fylla i korrekt och klicka sedan på \'publicera\' igen.

När du\'stött slutfört det här steget korrekt, visas ett popup-fönster och be om en e-postadress. Det här är den e-postadress som du får publishing Statusmeddelandena för resten av publiceringsprocessen. När du skickar in din e-postadress, har det här steget slutförts.

![Erbjuder Go Live serie 1 – publicera 1](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish1.png)

### <a name="certification-5-days"></a>Certifiering (\<5 dagar) 

Det här steget är där vi kör flera tester för att säkerställa att din Azure-programpaket uppfyller kraven för att vara Azure Certified.

Eftersom det här steget kan ta flera dagar, kan du logga ut från partnerportalen i molnet. Vi skickar ett e-postmeddelande om det finns några fel. Om allt går med framgång ska processen automatiskt gå vidare till steg för etablering.

### <a name="packaging-and-lead-generation-registration-1-hour"></a>Paketera och leda generation registrering (\<1 timme) 

Under det här steget kombinerar vi teknik- och innehållet i vad blir sidan på webbplatsen.

### <a name="offer-available-in-preview"></a>Erbjudandet är tillgängligt i förhandsversionen 

Du får ett e-postmeddelande som ditt erbjudande har slutfört de steg som krävs för att komma åt erbjudandet i förhandsversion. I det här steget ska du förhandsgranskar ditt erbjudande och kontrollera att allt ser ut som det ska. Kontrollera om den virtuella datorn har distribuerats korrekt i mellanlagringsmiljön.

Godkänd-prenumerationer kan göra den här kontrollen.\*

### <a name="publisher-sign-out"></a>Publisher-utloggning 

När du har kontrollerat allt ser korrekt ut och fungerar korrekt i förhandsversion, är du klar för livesändning. Klicka på Go Live under fliken status och vi börjar stegen så att ditt erbjudande live i produktion och på webbplatsen. Vanligtvis tar det flera timmar från det att du klickar på Go Live och när erbjudandet är aktiv på webbplatsen. Vi skickar ett e-postmeddelande när ditt erbjudande är officiellt live på webbplatsen.

![Erbjuder gå Live sekvens 6 – gå Live](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_goLive.png)

### <a name="microsoft-review"></a>Microsoft-granskning 

När du är redo att sätta igång och klickade på på \"Go Live\", en arbetsplatsen kommer att skapas i Visual Studio online, följt av en Microsoft Kodgranskning av paketinnehållet. Kodgranskning täcker mönster/metodtipsen för att använda vid redigering av mallar, tips och trick för att optimera resurser får skapas. Om din feedback resulterar i utgivaren gör ändringar i filerna, måste publiceringsprocessen startas igen. Den aktuella publiceringen kommer att avbrytas och du måste publicera igen med feedbackkommentarer åtgärdas.

### <a name="live"></a>Live

Erbjudandet är nu Live på Azure Marketplace och Azure-portalen. Kunderna kommer att kunna visa och distribuera dina azure-hanterade program i sina Azure-prenumerationer. Du kan klicka på fliken alla erbjudanden och se status för alla dina erbjudanden som visas på den högra kolumnen. Du kan klicka på status för att se publiceringsstatus för flow i detalj för ditt erbjudande.

### <a name="error-handling"></a>Felhantering 

Om det uppstår ett fel, får du ett e-postmeddelande som informerar dig om att ett fel uppstod med anvisningar om nästa steg. Du kan också se fel när som helst under den här processen genom att klicka på fliken Status. Du ser då välja i processen för felet inträffade tillsammans med ett felmeddelande som beskriver vad som behöver lösas.

Om det uppstår fel under publiceringsprocessen du behöver åtgärda dem och klicka sedan på \'publicera\' att starta om processen. Du måste starta i början av publishing stegen i Validera krav när du publicerar igen när du har några problem.

Om du har problem med hur du löser ett fel, ska du öppna en supportbegäran om att få hjälp från våra supporttekniker.

### <a name="canceling-the-publishing-request"></a>Publicera förfrågan avbröts

Du kan starta processen med att publicera och behöver du avbryta din begäran. Du kan bara avbryta en publiceringsbegäran när publiceringsbegäran når Publisher Kvittering steg. Om du vill avbryta, klickar du på \'Avbryt publicering\'. Publiceringsstatus återgår till steg 1 och för att publicera igen, ska du klicka på Publicera och följer du stegen i status.
