---
title: Hantera slutpunkter i Azure Traffic Manager | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till, tar bort, aktiverar och inaktiverar slutpunkter från Azure Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: ''

ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee

---
# <a name="add,-disable,-enable,-or-delete-endpoints"></a>Lägga till, inaktivera, aktivera eller ta bort slutpunkter
Funktionen Web Apps i Azure App Service tillhandahåller redan trafikroutningsfunktioner för redundans och resursallokering för webbplatser inom ett datacenter, oavsett webbplatsläge. Med Azure Traffic Manager kan du konfigurera trafikroutning för redundans och resursallokering för webbplatser och molntjänster i olika datacenter. Det första steget om du vill att den här funktionen ska vara tillgänglig är att lägga till molntjänstens eller webbplatsens slutpunkt i Traffic Manager.

> [!NOTE]
> Den här artikeln beskriver hur man använder den klassiska portalen. Den klassiska Azure-portalen stöder bara skapande och tilldelning av molntjänster och webbappar som slutpunkter. Den nya [Azure-portalen](https://portal.azure.com) är det föredragna gränssnittet.
> 
> 

Du kan också inaktivera enskilda slutpunkter som ingår i en Traffic Manager-profil. Om du inaktiverar en slutpunkt är den fortfarande en del av profilen, men profilen fungerar som om slutpunkten inte ingår i den. Den här åtgärden är användbar för att tillfälligt ta bort en slutpunkt som är i underhållsläge eller som omdistribueras. När slutpunkten är igång igen kan den aktiveras.

> [!NOTE]
> Inaktiveringen av en slutpunkt har inget att göra med dess distributionsstatus i Azure. En felfri slutpunkt förblir aktiv och kan ta emot trafik även om den är inaktiverad i Traffic Manager. Dessutom påverkar inte inaktiveringen av en slutpunkt i en profil dess status i en annan profil.
> 
> 

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Så här lägger du till slutpunkten för en webbplats eller molntjänst
1. I Traffic Manager-fönstret i den klassiska Azure-portalen, letar du upp den Traffic Manager-profil som innehåller de slutpunktsinställningar du vill ändra. Klicka på pilen till höger om profilnamnet för att öppna inställningssidan.
2. Klicka på **Slutpunkter** överst på sidan för att visa de slutpunkter som redan är en del av konfigurationen.
3. Klicka på **Lägg till** längst ned på sidan för att öppna sidan **Lägg till slutpunkter för tjänst**. Som standard visas molntjänsterna under **Tjänsteslutpunkter** på sidan.
4. För molntjänster, väljer du molntjänsterna i listan för att lägga till dem som slutpunkter för den här profilen. Om du tar bort namnet på en molntjänst tas tjänsten bort från listan med slutpunkter.
5. För webbplatser klickar du i listrutan **Tjänsttyp** och väljer sedan **Webbapp**.
6. Välj webbplatserna i listan för att lägga till dem som slutpunkter för profilen. Om du tar bort namnet på en webbplats tas webbplatsen bort i listan över slutpunkter. Observera att du bara kan välja en webbplats per Azure-datacenter (även kallat en region). När du väljer den första webbplatsen, går andra webbplatser i samma datacenter inte längre att välja. Observera även att endast standardwebbplatser visas.
7. När du har valt slutpunkterna för den här profilen sparar du dina ändringar genom att klicka på bockmarkeringen längst ned till höger.

> [!NOTE]
> När du lägger till eller tar bort en slutpunkt från en profil med trafikroutningsmetoden *Redundans*, så är det möjligt att prioritetslistan för redundans inte längre är i den ordning du önskar. Du kan justera ordningen för prioritetslistan för redundans från konfigurationssidan. Mer information finns i [Konfigurera trafikroutning för redundans](traffic-manager-configure-failover-routing-method.md).
> 
> 

## <a name="to-disable-an-endpoint"></a>Så här inaktiverar du en slutpunkt
1. I Traffic Manager-fönstret i den klassiska Azure-portalen, letar du upp den Traffic Manager-profil som innehåller de slutpunktsinställningar du vill ändra. Klicka på pilen till höger om profilnamnet för att öppna inställningssidan.
2. Du visar slutpunkterna som ingår i din konfiguration genom att klicka på **Slutpunkter** överst på sidan.
3. Klicka på den slutpunkt som du vill inaktivera och klicka sedan på **Inaktivera** längst ned på sidan.
4. Klienter fortsätter att skicka trafik till slutpunkten under hela livstiden (TTL). Du kan ändra TTL-värdet från konfigurationssidan för Traffic Manager-profilen.

## <a name="to-enable-an-endpoint"></a>Så här aktiverar du en slutpunkt
1. I Traffic Manager-fönstret i den klassiska Azure-portalen, letar du upp den Traffic Manager-profil som innehåller de slutpunktsinställningar du vill ändra. Klicka på pilen till höger om profilnamnet för att öppna inställningssidan.
2. Du visar slutpunkterna som ingår i din konfiguration genom att klicka på **Slutpunkter** överst på sidan.
3. Klicka på den slutpunkt som du vill aktivera och klicka sedan på **Aktivera** längst ned på sidan.
4. Klienter dirigeras till den aktiverade slutpunkten enligt profilen.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Så här tar du bort en slutpunkt för en webbplats eller molntjänst
1. I Traffic Manager-fönstret i den klassiska Azure-portalen, letar du upp den Traffic Manager-profil som innehåller de slutpunktsinställningar du vill ändra. Klicka på pilen till höger om profilnamnet för att öppna inställningssidan.
2. Klicka på **Slutpunkter** överst på sidan för att visa de slutpunkter som redan är en del av konfigurationen.
3. På sidan Slutpunkter klickar du på namnet på den slutpunkt som du vill ta bort från profilen.
4. Klicka på **Ta bort** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg
* [Traffic Manager – Inaktivera, aktivera eller ta bort en profil](traffic-manager-manage-profiles.md)
* [Konfigurera routningsmetod vid redundans](traffic-manager-configure-failover-routing-method.md)
* [Konfigurera routningsmetod vid resursallokering](traffic-manager-configure-round-robin-routing-method.md)
* [Konfigurera routningsmetod för prestanda](traffic-manager-configure-performance-routing-method.md)
* [Felsök degraderat tillstånd i Traffic Manager](traffic-manager-troubleshooting-degraded.md)
* [Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
* [Åtgärder för Traffic Manager (REST API-referens)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

<!--HONumber=Oct16_HO3-->


