---
title: Kapacitetsplanering för Azure App Service-serverroller i Azure Stack | Microsoft Docs
description: Kapacitetsplanering för Azure App Service-serverroller i Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: 7cdcd8b7e9814c206255077fae0af2029fab6583
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078121"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Kapacitetsplanering för Azure App Service-serverroller i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Om du vill konfigurera en klar Produktionsdistribution av Azure App Service i Azure Stack, måste du planera för kapacitet som du förväntar dig att systemet stöder.  

Den här artikeln innehåller anvisningar för det minsta antalet instanser och beräkning SKU: er som du bör använda för valfri Produktionsdistribution.

Du kan planera din strategi för App Service-kapacitet som använder dessa riktlinjer. Framtida versioner av Azure Stack ger alternativ för hög tillgänglighet för App Service.

| App Service-serverrollen | Minsta rekommenderade antalet instanser | Rekommenderade beräkning SKU|
| --- | --- | --- |
| Kontrollenhet | 2 | A1 |
| Klient | 2 | A1 |
| Hantering | 2 | A3 |
| Utgivare | 2 | A1 |
| Web Worker - delade | 2 | A1 |
| Web Worker - dedikerad | 2 per nivå | A1 |

## <a name="controller-role"></a>Kontrollantrollen

**Rekommenderade lägsta**: två instanser av Standard A1

Azure App Service-Controller inträffar normalt med låg användning av processor, minne och nätverksresurser. Du måste dock ha två domänkontrollanter för hög tillgänglighet. Två styrenheterna är också det maximala antalet domänkontrollanter som tillåts. Du kan skapa den andra styrenheten för Web Sites direkt från installationsprogrammet under distributionen.

## <a name="front-end-role"></a>Front End role

**Rekommenderade lägsta**: två instanser av Standard A1

Klientdelen dirigerar begäranden till Webbarbetare beroende på tillgänglighet för Web Worker. Du bör ha fler än en klientdel för hög tillgänglighet, och du kan ha fler än två. Överväg att varje kärna kan hantera cirka 100 begäranden per sekund för planering-kapacitet.

## <a name="management-role"></a>Hanteringsroll

**Rekommenderade lägsta**: två instanser av Standard A3

Azure App Service Management-rollen är ansvarig för App Service Azure Resource Manager och API-slutpunkter, portal tillägg (admin, klient, Functions-portalen) och data service. Management Server-rollen kräver normalt endast om 4 GB RAM-minne i en produktionsmiljö. Det kan dock uppstå höga CPU när många administrationsuppgifter (till exempel skapa en webbplats) utförs. Du bör ha fler än en server som har tilldelats den här rollen för hög tillgänglighet, och minst två kärnor per server.

## <a name="publisher-role"></a>Utgivarroll

**Rekommenderade lägsta**: två instanser av Standard A1

Om många användare publicerar samtidigt, kan Publisher-rollen uppstå vid hög CPU-användning. För hög tillgänglighet, göra mer än en roll för utgivaren som är tillgänglig.  Utgivaren hanterar endast FTP/FTPS-trafik.

## <a name="web-worker-role"></a>Web worker-roll

**Rekommenderade lägsta**: två instanser av Standard A1

För hög tillgänglighet bör du ha minst fyra Webbarbetsroller, två för delat webbplatsläge och två för varje dedikerad arbetarnivån du planerar att erbjuda. Delade och dedikerade beräkning lägen ge olika nivåer av tjänsten till klienter. Du kanske behöver mer Webbarbetare om många av dina kunder är:

- Med hjälp av arbetarnivåer för dedikerade beräkning läge (som är resurskrävande.)
- Kör i delat beräkningsläge.

När en användare har skapat en App Service Plan för ett dedikerat beräkningsläge SKU, antalet Web arbeten som angetts i att App Service-planen inte längre tillgänglig för användare.

Om du vill ge användarna i planen förbrukningsmodell Azure Functions, måste du distribuera delade Webbarbetare.

När du bestämmer dig antalet delade Web Worker-roller ska använda, granska dessa överväganden:

- **Minne**: minne är den mest kritiska resursen för en Web Worker-roll. Otillräckligt med minne påverkar prestanda för webbplatsen när virtuellt minne växlar från disken. Varje server kräver cirka 1,2 GB RAM-minne för operativsystemet. RAM-minne över tröskeln kan användas för att köra webbplatser.
- **Procentandelen aktiva webbplatser**: vanligtvis cirka 5 procent av program i en Azure App Service i Azure Stack-distributioner är aktiva. Procentandel program som är aktiva vid ett givet tillfälle kan dock vara högre eller lägre. Med en aktiv programmet hastighet av 5 procent det maximala antalet program ska placeras i en Azure App Service i Azure Stack-distributionen ska vara mindre än:
  - 20 gånger antalet aktiva webbplatser (5 x 20 = 100).
- **Genomsnittlig minneskrav**: genomsnittlig minnesavtrycket som krävs för program som observerats i produktionsmiljöer är cirka 70 MB. Med den här tjänsten kan kan det minne som allokerats över alla datorer med roller för Web Worker eller virtuella datorer beräknas enligt följande:

    *Antal etablerade program * 70 MB * 5% - (tal av Webbarbetsroller * 1044 MB)*

   Om det finns 5 000 program i miljön med 10 Web Worker-roller, bör varje Web Worker-roll VM ha 7060 MB RAM-minne:

   5 000 * 70 * 0,05 – (10 * 1044) = 7060 (= cirka 7 GB)

   Information om att lägga till flera arbetsinstanser finns i [att lägga till fler arbetsroller](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Filserverrollen

För rollen filserver kan du använda en fristående filserver för utveckling och testning, till exempel när du distribuerar Azure App Service i Azure Stack Development Kit kan du använda den här mallen - <https://aka.ms/appsvconmasdkfstemplate>. För produktion bör du använda en förkonfigurerad Windows-filserver eller en förkonfigurerad icke-Windows server.

Rollen filserver upplevelser beräkningsintensiva disk-i/o i produktionsmiljöer. Eftersom den innehåller alla filer som innehåll och program för webbplatser för användare, bör du konfigurera något av följande för den här rollen före:

- en Windows-filserver
- en Windows-Filserverklustret
- en icke-Windows-filserver
- en icke-Windows-filserverklustret
- en enhet för NAS (Network Attached Storage)

Mer information finns i [etablera en filserver](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Nästa steg

[Innan du sätter igång med App Service i Azure Stack](azure-stack-app-service-before-you-get-started.md)
