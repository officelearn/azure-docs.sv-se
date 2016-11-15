---
title: Hantera slutpunkter i Azure Traffic Manager | Microsoft Docs
description: "Den här artikeln beskriver hur du lägger till, tar bort, aktiverar och inaktiverar slutpunkter från Azure Traffic Manager."
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: tysonn
ms.assetid: 038270d1-28ba-4078-9c5d-37fc5d683be6
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cbdd48397319cb840b05570ba90093ff8f6887b0

---

# <a name="add-disable-enable-or-delete-endpoints"></a>Lägga till, inaktivera, aktivera eller ta bort slutpunkter

Funktionen Web Apps i Azure App Service tillhandahåller redan trafikroutningsfunktioner för redundans och resursallokering för webbplatser inom ett datacenter, oavsett webbplatsläge. Med Azure Traffic Manager kan du konfigurera trafikroutning för redundans och resursallokering för webbplatser och molntjänster i olika datacenter. Det första steget om du vill att den här funktionen ska vara tillgänglig är att lägga till molntjänstens eller webbplatsens slutpunkt i Traffic Manager.

> [!NOTE]
> Du kan inte lägga till externa platser eller Traffic Manager-profiler som slutpunkter via den klassiska Azure-portalen. Du måste använda [Skapa definition](http://go.microsoft.com/fwlink/p/?LinkId=400772) i REST-API:et eller Windows PowerShell-kommandot [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774).

Du kan också inaktivera enskilda slutpunkter som ingår i en Traffic Manager-profil. Slutpunkter är både molntjänster och webbplatser. Om du inaktiverar en slutpunkt är den fortfarande en del av profilen, men profilen fungerar som om slutpunkten inte ingår i den. Den här åtgärden är användbar om du tillfälligt vill ta bort en slutpunkt som är i underhållsläge eller som omdistribueras. När slutpunkten är igång igen kan den aktiveras.

> [!NOTE]
> Inaktiveringen av en slutpunkt har inget att göra med dess distributionsstatus i Azure. En felfri slutpunkt förblir aktiv och kan ta emot trafik även om den är inaktiverad i Traffic Manager. Dessutom påverkar inte inaktiveringen av en slutpunkt i en profil dess status i en annan profil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Så här lägger du till slutpunkten för en webbplats eller molntjänst

1. Leta upp Traffic Manager-profilen som innehåller de slutpunktsinställningar som du vill ändra i Traffic Manager-fönstret på den klassiska Azure-portalen och klicka på pilen till höger om profilnamnet. Nu öppnas inställningssidan för profilen.
2. Klicka på **Slutpunkter** överst på sidan för att visa de slutpunkter som redan är en del av konfigurationen.
3. Klicka på **Lägg till** längst ned på sidan för att öppna sidan **Lägg till slutpunkter för tjänst**. Som standard visas molntjänsterna under **Tjänsteslutpunkter** på sidan.
4. För molntjänster väljer du molntjänsterna i listan för att aktivera dem som slutpunkter för den här profilen. Om du tar bort namnet på en molntjänst tas tjänsten bort från listan med slutpunkter.
5. För webbplatser klickar du i listrutan **Tjänsttyp** och väljer sedan **Webbapp**.
6. Välj webbplatserna i listan för att lägga till dem som slutpunkter för profilen. Om du tar bort namnet på en webbplats tas webbplatsen bort i listan över slutpunkter. Observera att du bara kan välja en enda webbplats per Azure-datacenter (även kallat en region). När du väljer den första webbplatsen i ett datacenter som är värd för flera webbplatser blir övriga webbplatser i samma datacenter otillgängliga och kan inte väljas. Observera även att endast standardwebbplatser visas.
7. När du har valt slutpunkterna för den här profilen sparar du dina ändringar genom att klicka på bockmarkeringen längst ned till höger.

> [!NOTE]
> När du har lagt till eller tagit bort en slutpunkt och om du använder trafikroutningsmetoden för *redundans* måste du justera prioritetslistan för redundans på sidan Konfiguration så att den återspeglar den redundansordning som du vill använda för konfigurationen. Mer information finns i [Konfigurera trafikroutning för redundans](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Så här inaktiverar du en slutpunkt

1. Leta upp Traffic Manager-profilen som innehåller de slutpunktsinställningar som du vill ändra i Traffic Manager-fönstret på den klassiska Azure-portalen och klicka på pilen till höger om profilnamnet. Nu öppnas inställningssidan för profilen.
2. Du visar slutpunkterna som ingår i din konfiguration genom att klicka på **Slutpunkter** överst på sidan.
3. Klicka på den slutpunkt som du vill inaktivera och klicka sedan på **Inaktivera** längst ned på sidan.
4. Trafiken slutar flöda till slutpunkten baserat på TTL-värdet (Time-To-Live) för DNS som har konfigurerats för Traffic Manager-domännamnet. Du kan ändra TTL-värdet från konfigurationssidan för Traffic Manager-profilen.

## <a name="to-enable-an-endpoint"></a>Så här aktiverar du en slutpunkt

1. Leta upp Traffic Manager-profilen som innehåller de slutpunktsinställningar som du vill ändra i Traffic Manager-fönstret på den klassiska Azure-portalen och klicka på pilen till höger om profilnamnet. Nu öppnas inställningssidan för profilen.
2. Du visar slutpunkterna som ingår i din konfiguration genom att klicka på **Slutpunkter** överst på sidan.
3. Klicka på den slutpunkt som du vill aktivera och klicka sedan på **Aktivera** längst ned på sidan.
4. Trafiken börjar flöda till tjänsten igen baserat på inställningarna för profilen.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Så här tar du bort en slutpunkt för en webbplats eller molntjänst

1. Leta upp Traffic Manager-profilen som innehåller de slutpunktsinställningar som du vill ändra i Traffic Manager-fönstret på den klassiska Azure-portalen och klicka på pilen till höger om profilnamnet. Nu öppnas inställningssidan för profilen.
2. Klicka på **Slutpunkter** överst på sidan för att visa de slutpunkter som redan är en del av konfigurationen.
3. På sidan Slutpunkter klickar du på namnet på den slutpunkt som du vill ta bort från profilen.
4. Klicka på **Ta bort** längst ned på sidan.

> [!NOTE]
> Du kan inte ta bort externa platser eller Traffic Manager-profiler som slutpunkter med hjälp av den klassiska Azure-portalen. Du måste använda Windows PowerShell. Mer information finns i [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera routningsmetod vid redundans](traffic-manager-configure-failover-routing-method.md)
* [Konfigurera routningsmetod vid resursallokering](traffic-manager-configure-round-robin-routing-method.md)
* [Konfigurera routningsmetod för prestanda](traffic-manager-configure-performance-routing-method.md)
* [Felsök degraderat tillstånd i Traffic Manager](traffic-manager-troubleshooting-degraded.md)
* [Åtgärder för Traffic Manager (REST API-referens)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO2-->


