---
title: Kapacitetsplanering för Azure App Service-serverroller i Azure-stacken | Microsoft Docs
description: Kapacitetsplanering för Azure App Service-serverroller i Azure-stacken
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 8926955d5e0260b5971e07b6988bb21df9980847
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29388591"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Kapacitetsplanering för Azure App Service-serverroller i Azure-stacken
*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

För att etablera en klar Produktionsdistribution av Azure App Service på Azure-stacken, måste du planera för kapacitet du förväntar dig att systemet stöder.  Här följer riktlinjer för minsta antal förekomster och beräkning SKU: er som du bör använda för distribution i produktionsmiljöer.

Du kan planera din strategi för kapacitet av Apptjänst med hjälp av dessa riktlinjer. Framtida versioner av Azure-stacken ger alternativ för hög tillgänglighet för Apptjänst.

| Apptjänst-serverrollen | Minsta rekommenderade antalet instanser | Rekommenderade beräkning SKU|
| --- | --- | --- |
| Kontrollenhet | 2 | A1 |
| Klient | 2 | A1 |
| Hantering | 2 | A3 |
| Utgivare | 2 | A1 |
| Web medarbetare – delade | 2 | A1 |
| Web medarbetare – dedikerade | 2 per nivå | A1 |

## <a name="controller-role"></a>Rollen som domänkontrollant

**Rekommenderade minsta**: två instanser av A1 standard

Azure App Service Controller inträffar normalt låg förbrukning av CPU, minne och nätverksresurser. Du måste dock ha två domänkontrollanter för hög tillgänglighet. Två domänkontrollanter är också det maximala antalet domänkontrollanter som är tillåtna. Du kan skapa andra webbplatser styrenheten direkt från installationsprogrammet under distributionen.

## <a name="front-end-role"></a>Front End role

**Rekommenderade minsta**: två instanser av A1 standard

Klientdelen dirigerar begäranden till webbtjänst arbetare beroende på Web Worker tillgänglighet. Du bör ha fler än en klientdel för hög tillgänglighet och du kan ha fler än två. För kapacitetsplanering ändamål, Överväg att varje kärna kan hantera cirka 100 begäranden per sekund.

## <a name="management-role"></a>Roll

**Rekommenderade minsta**: två instanser av A3

Azure App Service Management-rollen är ansvarig för App Service Azure Resource Manager och API-slutpunkter, portal tillägg (admin, klient, Functions-portalen) och datatjänsten. Management Server-rollen kräver normalt bara om 4 GB RAM-minne i en produktionsmiljö. Det kan dock uppstå hög CPU när många administrativa uppgifter (till exempel skapa en webbplats) utförs. Du bör ha fler än en server som den här rollen för hög tillgänglighet och minst två kärnor per server.

## <a name="publisher-role"></a>Utgivare

**Rekommenderade minsta**: två instanser av A1

Om många användare publicerar samtidigt, få rollen Publisher hög CPU-användning. För hög tillgänglighet, göra mer än en utgivare roll som är tillgängliga.  Utgivaren endast hanterar FTP-/ FTPS-trafik.

## <a name="web-worker-role"></a>Worker-webbroll

**Rekommenderade minsta**: två instanser av A1

Du bör ha minst fyra Web arbetsroller, två för delade webbplats läge och två för varje nivå dedikerade arbetarservrar du planerar att erbjuda för hög tillgänglighet. Delade och dedikerad beräkning lägen ge olika nivåer av tjänsten till klienter. Du måste kanske flera Web arbetare om du har många kunder:
 - med hjälp av dedikerade beräkning läge worker nivåer (som är resurskrävande)
 - körs i delade beräknings-läge.

När en användare har skapat en App Service-Plan för dedikerade beräknings-läge SKU: N, antalet Web arbetare som angetts i att Apptjänstplan kommer inte längre vara tillgängliga för användare.

För att ge Azure Functions till användare i förbrukning planmodellen, måste du distribuera delade Web arbetare.

När du funderar över antalet delade Web arbetsroller genom att gå igenom dessa överväganden:

- **Minne**: minne är den mest kritiska resursen för en roll för Web Worker. Otillräckligt med minne påverkar webbplatsprestanda när virtuellt minne växlas från disken. Varje server kräver 1,2 GB RAM för operativsystemet. RAM-minne över tröskeln kan användas för att köra webbplatser.
- **Procentandelen aktiva webbplatser**: vanligtvis cirka 5 procent av program i en Azure App Service för distribution av Azure-stacken är aktiva. Procentandelen av program som är aktiva vid ett givet tillfälle kan dock vara högre eller lägre. Med en aktiv frekvens på 5 procent det maximala antalet program att placera den i en Azure App Service på Azure-stacken distributionen ska vara mindre än:
    - 20 gånger antalet aktiva webbplatser (5 x 20 = 100).
- **Genomsnittlig minneskrav**: genomsnittlig minneskrav för program som förekommer i produktionsmiljöer är på 70 MB. Det minne som allokerats på alla datorer med roller för Web Worker eller virtuella datorer kan därför beräknas enligt följande:

    *Antal etablerad program * 70 MB * 5% - (nummer för Web arbetsroller * 1044 MB)*

   Om det finns 5 000 program i miljön som kör 10 webb-och arbetsroller, måste varje roll för Web Worker VM ha 7060 MB RAM-minne:

   5 000 * 70 * 0,05 – (10 * 1044) = 7060 (= cirka 7 GB)

   Mer information om att lägga till flera worker-instanser finns [att lägga till flera arbetsroller](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Filserverrollen

För rollen filserver kan du använda en fristående filserver för utveckling och testning, till exempel när du distribuerar Azure App Service på Azure-stacken Development Kit du kan använda den här mallen - https://aka.ms/appsvconmasdkfstemplate. För produktion, ska du använda en förkonfigurerad Windows-filserver eller en förkonfigurerad Windows-filserver.

I produktionsmiljöer upplevelser rollen filserver beräkningsintensiva disk-i/o. Eftersom den innehåller alla filer som innehåll och program för användaren för webbplatser, ska du konfigurera något av följande för den här rollen före:
- en Windows-filserver
- Filserverkluster
- en icke-Windows-filserver
- filserverkluster
- NAS (Network Attached Storage)-enhet för mer information, se [etablera en filserver](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Nästa steg

[Innan du börjar med App Service på Azure-stacken](azure-stack-app-service-before-you-get-started.md)
