---
title: Kapacitetsplanering för Azure App Service-serverroller i Azure Stack | Microsoft Docs
description: Kapacitetsplanering för Azure App Service-serverroller i Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 06bafbcf3e668ba17b1245b9352e942e02569997
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852380"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Kapacitetsplanering för Azure App Service-serverroller i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Om du vill konfigurera en klar Produktionsdistribution av Azure App Service i Azure Stack, måste du planera för kapacitet som du förväntar dig att systemet stöder.  

Den här artikeln innehåller anvisningar för det minsta antalet instanser och beräkning SKU: er som du bör använda för valfri Produktionsdistribution.

Du kan planera din strategi för App Service-kapacitet som använder dessa riktlinjer.

| App Service-serverrollen | Minsta rekommenderade antalet instanser | Rekommenderade beräkning SKU|
| --- | --- | --- |
| Kontrollenhet | 2 | A1 |
| Klient | 2 | A1 |
| Hantering | 2 | A3 |
| Utgivare | 2 | A1 |
| Web Worker - delade | 2 | A1 |
| Web Worker - dedikerad | 2 per nivå | A1 |

## <a name="controller-role"></a>Kontrollantrollen

**Rekommenderat**: Två instanser av Standard A1

Azure App Service-controller inträffar normalt med låg användning av processor, minne och nätverksresurser. Du måste dock ha två domänkontrollanter för hög tillgänglighet. Två styrenheterna är också det maximala antalet domänkontrollanter som tillåts. Du kan skapa den andra styrenheten för webbplatser direkt från installationsprogrammet under distributionen.

## <a name="front-end-role"></a>Frontend-roll

**Rekommenderat**: Två instanser av Standard A1

Klientdelen vägar begäranden till webbarbetare beroende på tillgänglighet för web worker. Du bör ha fler än en klientdel för hög tillgänglighet, och du kan ha fler än två. Överväg att varje kärna kan hantera cirka 100 begäranden per sekund för planering-kapacitet.

## <a name="management-role"></a>Hanteringsroll

**Rekommenderat**: Två instanser av Standard A3

Azure App Service management-rollen är ansvarig för App Service Azure Resource Manager och API-slutpunkter, portal tillägg (admin, klient, funktionsportalen) och datatjänsten. Hanteringsserverrollen kräver normalt endast om 4 GB RAM-minne i en produktionsmiljö. Det kan dock uppstå höga CPU när många administrationsuppgifter (till exempel skapa en webbplats) utförs. Du bör ha fler än en server som har tilldelats den här rollen för hög tillgänglighet, och minst två kärnor per server.

## <a name="publisher-role"></a>Utgivarroll

**Rekommenderat**: Två instanser av Standard A1

Om många användare publicerar samtidigt, kan publisher-rollen uppstå vid hög CPU-användning. Kontrollera att mer än en utgivarroll är tillgänglig för hög tillgänglighet. Utgivaren hanterar endast FTP/FTPS-trafik.

## <a name="web-worker-role"></a>Web worker-roll

**Rekommenderat**: Två instanser av Standard A1

För hög tillgänglighet bör du ha minst fyra webbarbetsroller, två för delade webbplatsläget och två för varje dedikerad arbetarnivån som du planerar att erbjuda. Delade och dedikerade compute-lägen ge olika nivåer av tjänsten till klienter. Du kanske behöver mer webbarbetare om många av dina kunder är:

- Med hjälp av arbetarnivåer för dedikerade beräkning läge (som är resurskrävande).
- Kör i delat beräkningsläge.

När en användare har skapat en App Service-plan för ett dedikerat beräkningsläge SKU: N, är antalet web arbeten som anges i den här App Service-planen inte längre tillgänglig för användare.

Om du vill ge användarna i planen förbrukningsmodell Azure Functions, måste du distribuera delade webbarbetare.

När du bestämmer dig antalet delade webbarbetsroller ska använda, granska dessa överväganden:

- **Minne**: Minnet är den mest kritiska resursen för en web worker-roll. Otillräckligt med minne påverkar prestanda för webbplatsen när virtuellt minne växlar från disken. Varje server kräver cirka 1,2 GB RAM-minne för operativsystemet. RAM-minne över tröskeln kan användas för att köra webbplatser.
- **Procentandelen aktiva webbplatser**: Vanligtvis är cirka 5 procent av program i en Azure App Service i Azure Stack-distributioner aktiva. Procentandel program som är aktiva vid ett givet tillfälle kan dock vara högre eller lägre. Det maximala antalet program ska placeras i en Azure App Service i Azure Stack-distributionen ska vara mindre än 20 gånger antalet aktiva webbplatser (5 x 20 = 100) med en aktiv programmet hastighet av 5 procent.
- **Genomsnittlig minneskrav**: Genomsnittlig minnesavtrycket som krävs för program som observerats i produktionsmiljöer är cirka 70 MB. Med den här tjänsten kan kan det minne som allokerats över alla datorer med roller för web worker eller virtuella datorer beräknas enligt följande:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Om det finns 5 000 program i miljön med 10 web worker-roller, bör varje web worker-roll VM ha 7060 MB RAM-minne:

   `5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= about 7 GB)`

   Information om att lägga till flera arbetsinstanser finns i [att lägga till fler arbetsroller](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>Ytterligare överväganden för dedikerade arbetare under uppgraderingen och underhåll

Under uppgraderingen och underhåll av de anställda kan utför Azure App Service i Azure Stack Underhåll på 20% av varje arbetarnivån åt gången.  Molnadministratörer måste därför alltid ha en pool med 20% av lediga arbetare per arbetarnivån så förlust av tjänsten under uppgraderingen och underhåll inte drabbas av sina klienter.  Till exempel om du har 10 arbetare i en arbetarnivå bör du kontrollera att 2 är lediga så att uppgraderingen och underhåll, om samtliga 10 arbetare blir allokerat du bör skala arbetarnivån upp för att upprätthålla en pool med lediga arbetare. Under uppgraderingen och underhåll Azure App Service kommer att flytta arbetsbelastningar till lediga arbeten för att säkerställa att arbetsbelastningar fortsätter att fungera men om det finns några lediga anställda som är tillgängliga under uppgradera sedan det kommer att risken för klientarbetsbelastning driftstopp.  För delade arbetare behöver kunder inte etablera fler arbeten när tjänsten kommer att fördela klient program i tillgängliga arbeten automatiskt, för hög tillgänglighet men det finns ett minimikrav för två arbetare i det här -nivå.

Cloud administratörerna kan övervaka sina worker nivån allokering i området Administration i App Service i Azure Stack-administrationsportalen.  Gå till App Service och välj sedan Arbetarnivåer i det vänstra fönstret.  Tabellen Arbetarnivåer visar namn på arbetarnivå, storlek, avbildning som används, antalet tillgängliga arbetare (tilldelats), totalt antal arbeten i varje nivå och det övergripande tillståndet för arbetarnivån.

![App Service-Administration - Arbetarnivåer][1]

## <a name="file-server-role"></a>Filserverrollen

För filserverrollen, kan du använda en fristående filserver för utveckling och testning; till exempel när du distribuerar Azure App Service på Azure Stack Development Kit (ASDK) du kan använda detta [mallen](https://aka.ms/appsvconmasdkfstemplate).  För produktion bör du använda en förkonfigurerad Windows-filserver eller en förkonfigurerad icke-Windows server.

Filserverrollen upplevelser beräkningsintensiva disk-i/o i produktionsmiljöer. Eftersom den innehåller alla filer som innehåll och program för webbplatser för användare, bör du konfigurera en av följande resurser för den här rollen före:

- Windows-filserver
- Windows-filserverklustret
- Icke-Windows-filserver
- Icke-Windows-filserverklustret
- NAS (Network Attached Storage)-enhet

Se följande artikel för mer information [etablera en filserver](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Nästa steg

Se följande artikel för mer information:

[Innan du sätter igång med App Service i Azure Stack](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png