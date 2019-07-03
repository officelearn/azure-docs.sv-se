---
title: Microsoft Azure FXT Edge Filer klusterkonfiguration – lägga till noder
description: Lägga till noder till lagringscache Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: d84b98b4ab936bbb6978144eb2e89b5e19df7069
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543229"
---
# <a name="tutorial-add-cluster-nodes"></a>Självstudier: Lägg till noder 

Ett nytt kluster i Azure FXT Edge Filer skapas med endast en nod. Du bör lägga till minst två noder och aktivera hög tillgänglighet innan du gör en annan konfiguration. 

Den här självstudien beskrivs hur du lägger till klusternoder och aktivera funktionen för hög tillgänglighet (HA). 

I den här kursen lär du dig: 

> [!div class="checklist"]
> * Lägga till noder i klustret FXT
> * Så här aktiverar du hög tillgänglighet

Stegen i den här självstudiekursen tar cirka 45 minuter att slutföra.

Innan du börjar den här självstudien, sätter på de noder som du vill lägga till och [Ange lösenorden inledande](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Läsa in sidan klusternoder

Öppna Kontrollpanelen för klustrets i en webbläsare och logga in som administratör. (Detaljerade anvisningar finns i översiktsartikeln under [öppna inställningssidorna](fxt-cluster-create.md#open-the-settings-pages).)

I Kontrollpanelen visas den **instrumentpanelen** fliken när den öppnas. 

![Kontrollera panelen instrumentpanelen (fliken först)](media/fxt-cluster-config/dashboard-1-node.png)

Den här bilden visar instrumentpanelen för ett nyskapat kluster med en enda nod.

## <a name="2-locate-the-node-to-add"></a>2. Leta rätt på noden för att lägga till

Om du vill lägga till noder, klickar du på den **inställningar** och välj den **FXT noder** sidan i den **kluster** avsnittet.

![Inställningar för Kontrollpanel fliken (andra fliken) med kluster > FXT noder som lästs in](media/fxt-cluster-config/settings-fxt-nodes.png)

Den **FXT noder – kopplas** listan visar alla otilldelade FXT noderna (de flesta Datacenter har bara några. Hitta de FXT noder som du vill lägga till i klustret.

> [!Tip] 
> Om du inte hittar nod du vill ha på de **Unjoined** listan, kontrollera att den uppfyller dessa krav:
> 
> * Den är påslagen och har haft en [rot lösenordsändring](fxt-node-password.md).
> * Den är ansluten till ett nätverk som du kan komma åt. Om du använder virtuella lokala nätverk, måste den vara på samma VLAN som klustret.
> * Den kan identifieras med Bonjour-protokollet. 
>
>   Vissa brandväggsinställningar blockera TCP/UDP-portar som används av Bonjour, vilket förhindrar att operativsystemet FXT identifieras automatiskt noderna.
> 
> Om den nod som du vill lägga till inte finns i listan, försök med följande: 
> 
> * Klicka på den **manuell identifiera** för att hitta efter IP-adress.
> 
> * Manuellt tilldela tillfälliga IP-adresser. Detta är ovanligt men kan behövas om du använder taggade virtuella lokala nätverk och noderna inte är ansluten till rätt nätverk eller nätverket tillåter inte själva tilldelade IP-adresser. Följ instruktionerna i den äldre versionen av det här dokumentet till [manuellt ange en statisk IP-adress](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Nodnamnet, IP-adress, programvaruversion och berättigande status visas i listan. Normalt den **Status** kolumn som antingen står ”vill gå med i” eller beskriver ett problem med system- eller maskinvara som gör noden inte berättigade att ansluta till klustret.

Den **åtgärder** kolumnen har knappar som låter dig lägga till noden i klustret eller uppdatera sin programvara. Uppdateringsknappen installerar automatiskt programvaruversion som matchar noder redan i klustret.

Alla noder i ett kluster måste använda samma version av Operativsystemet, men du behöver inte uppdatera programmet innan du lägger till en nod. När du klickar på den **Tillåt att Join** knappen processen på klustret kontrollerar och installerar automatiskt OS-programvara som matchar versionen på klustret.

Mer information om alternativen på den här sidan [ **kluster** > **FXT noder** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) i konfigurationsguiden för klustret.

## <a name="3-click-the-allow-to-join-button"></a>3. Klicka på knappen ”Tillåt att anslutning till” 

Klicka på den **tillåta att ansluta till*** knappen i den **åtgärder** kolumnen för den nod som du vill lägga till.

När du klickar på knappen kan nodens status ändras när programvaran som uppdateras som förberedelse för att lägga till den i klustret. 

Bilden nedan visar en nod som håller på att ansluta till klustret (troligen, den får en uppdatering av operativsystem innan du läggs). Inga knappar som visas i den **åtgärder** kolumn för noder som håller på att läggas till i klustret.

![en rad i tabellen nod, som visar en nodens namn, IP-adress, programvaruversion, meddelandet ”tillåts att ansluta till” och en tom sista kolumnen](media/fxt-cluster-config/node-join-in-process.png)

Efter en liten stund visas den nya noden i listan över klusternoder överst i den **FXT noder** inställningssidan. 

Upprepa proceduren för att lägga till de andra noderna i klustret. Du behöver inte vänta tills en nod och slutför ansluta till klustret innan du startar en annan.

## <a name="enable-high-availability"></a>Aktivera hög tillgänglighet

När du har lagt till en andra nod i klustret, kan du se ett varningsmeddelande på Kontrollpanelen instrumentpanel som funktionen för hög tillgänglighet inte är konfigurerad. 

Hög tillgänglighet (HA) gör att noderna i klustret att kompensera för varandra om en kraschar. Hög tillgänglighet är inte aktiverat som standard.

![Fliken instrumentpanel med meddelandet ”klustret har fler än en nod, men hög tillgänglighet är inte aktiverat...” i tabellen villkor](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Aktivera inte HA förrän du har minst tre noder i klustret.

Följ den här proceduren för att aktivera hög tillgänglighet: 

1. Belastningen på **hög tillgänglighet** sidan i den **kluster** delen av den **inställningar** fliken.

   ![Konfigurationssidan för hög tillgänglighet (kluster > hög tillgänglighet). ”Aktivera hög tillgänglighet” kryssrutan är högst upp och skickaknappen som finns längst ned.](media/fxt-cluster-config/enable-ha.png)

2. Klicka på rutan **Aktivera hög tillgänglighet** och klicka på den **skicka** knappen. 

En varning visas på den **instrumentpanelen** att bekräfta att hög tillgänglighet är aktiverat.

![Instrumentpanelen tabell som visar meddelandet ”hög tillgänglighet är nu fullständigt konfigurerad”](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Nästa steg

När du lägger till alla noder i klustret kan fortsätta installationen genom att konfigurera ditt kluster långsiktig lagring.

> [!div class="nextstepaction"]
> [Lägg till backend-lagring och konfigurera virtuella namnområdet](fxt-add-storage.md)