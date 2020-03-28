---
title: 'Självstudiekurs: Lägga till noder i ett Azure FXT Edge Filer-kluster'
description: Så här lägger du till noder i Azure FXT Edge Filer-lagringscachen
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 6251fe8f88b7db25e3c09898540e07754d72fb0d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551955"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Självstudiekurs: Lägga till klusternoder i ett Azure FXT Edge Filer-kluster

Ett nytt Azure FXT Edge Filer-kluster skapas med endast en nod. Du bör lägga till minst två noder och aktivera hög tillgänglighet innan du gör andra konfigurationer. 

I den här självstudien beskrivs hur du lägger till klusternoder och aktiverar funktionen Hög tillgänglighet (HA). 

I den här kursen lär du dig: 

> [!div class="checklist"]
> * Så här lägger du till noder i FXT-klustret
> * Så här aktiverar du HA

Stegen i den här självstudien tar cirka 45 minuter att slutföra.

Innan du startar den här självstudien, slå på noderna som du vill lägga till och [ställa in sina ursprungliga lösenord](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Läs in sidan Klusternoder

Öppna klustrets Kontrollpanel i en webbläsare och logga in som administratör. (Detaljerade instruktioner finns i översiktsartikeln under [Öppna sidan Inställningar](fxt-cluster-create.md#open-the-settings-pages).)

Kontrollpanelen visar fliken **Instrumentpanel** när den öppnas. 

![Instrumentpanelens instrumentpanel (första fliken)](media/fxt-cluster-config/dashboard-1-node.png)

Den här bilden visar instrumentpanelen för ett nyskapat kluster med en enda nod.

## <a name="2-locate-the-node-to-add"></a>2. Leta upp noden för att lägga till

Om du vill lägga till noder klickar du på fliken **Inställningar** och väljer sidan **FXT-noder** i avsnittet **Kluster.**

![Fliken Inställningar på kontrollpanelen (andra fliken) med kluster > FXT-noder inlästa](media/fxt-cluster-config/settings-fxt-nodes.png)

**FXT-noder - Ojoined-listan** visar alla otilldelade FXT-noder (de flesta datacenter har bara ett fåtal. Leta reda på de FXT-noder som du vill lägga till i klustret.

> [!Tip] 
> Om du inte hittar den nod du vill använda i listan **Ej inspelad** kontrollerar du att den uppfyller följande krav:
> 
> * Den är påslagen och har haft en [rot lösenord som](fxt-node-password.md).
> * Den är ansluten till ett nätverk som du kan komma åt. Om du använder VLAN måste det finnas på samma VLAN som klustret.
> * Det kan identifieras med Bonjour-protokollet. 
>
>   Vissa brandväggsinställningar blockerar TCP/UDP-portarna som används av Bonjour, vilket förhindrar att operativsystemet FXT automatiskt identifierar noderna.
> 
> Om noden som du vill lägga till inte finns med i listan provar du följande lösningar: 
> 
> * Klicka på knappen **Manuell upptäck** för att hitta den efter IP-adress.
> 
> * Tilldela tillfälliga IP-adresser manuellt. Detta är sällsynt men kan behövas om du använder taggade VLAN och noderna inte finns i rätt nätverk, eller om nätverket inte tillåter självtilldelade IP-adresser. Följ instruktionerna i den äldre versionen av det här dokumentet för att [manuellt ange en statisk IP-adress](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Nodnamnet, IP-adressen, programversionen och behörighetsstatusen visas i listan. Vanligtvis står det i kolumnen **Status** antingen "Vill gå med" eller beskriver ett system- eller maskinvaruproblem som gör noden olämplig att ansluta till klustret.

Kolumnen **Åtgärder** har knappar som gör att du kan lägga till noden i klustret eller uppdatera dess programvara. Uppdateringsknappen installerar automatiskt den programvaruversion som matchar de noder som redan finns i klustret.

Alla noder i ett kluster måste använda samma version av operativsystemet, men du behöver inte uppdatera programvara innan du lägger till en nod. När du har klickat på knappen **Tillåt att gå med** kontrollerar och installerar klusterkopplingen automatiskt os-programvaran som matchar versionen i klustret.

Mer information om alternativen på den här sidan finns i [ **Kluster-FXT-noder** > ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) i klusterkonfigurationsguiden.

## <a name="3-click-the-allow-to-join-button"></a>3. Klicka på knappen "Tillåt att gå med" 

Klicka på knappen **Tillåt att gå med*** i kolumnen **Åtgärder** för den nod som du vill lägga till.

När du har klickat på knappen kan nodens status ändras när programvaran uppdateras som förberedelse för att lägga till den i klustret. 

Bilden nedan visar en nod som håller på att ansluta till klustret (troligen får den en OS-uppdatering innan den läggs till). Inga knappar visas i kolumnen **Åtgärder** för noder som håller på att läggas till i klustret.

![en rad i nodtabellen, som visar en nods namn, IP-adress, programvaruversion, meddelandet "Tillåtet att gå med" och en tom sista kolumn](media/fxt-cluster-config/node-join-in-process.png)

Efter en liten stund ska den nya noden visas i listan över klusternoder högst upp på **inställningssidan för FXT-noder.** 

Upprepa den här processen om du vill lägga till de andra noderna i klustret. Du behöver inte vänta på att en nod ska slutföra anslutningen till klustret innan du startar en annan.

## <a name="enable-high-availability"></a>Aktivera hög tillgänglighet

När du har lagt till en andra nod i klustret kan du se ett varningsmeddelande på kontrollpanelens instrumentpanel om att funktionen med hög tillgänglighet inte är konfigurerad. 

Hög tillgänglighet (HA) gör att klusternoderna kan kompensera varandra om man går ner. HA är inte aktiverat som standard.

![Fliken Instrumentpanel med meddelandet "Klustret har mer än en nod, men HA är inte aktiverat ..." i tabellen Villkor](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Aktivera inte HA förrän du har minst tre noder i klustret.

Följ den här proceduren för att aktivera HA: 

1. Läs in sidan **Hög tillgänglighet** i avsnittet **Kluster** på fliken **Inställningar.**

   ![HA-konfigurationssida (Kluster > hög tillgänglighet). Kryssrutan "Aktivera HA" är överst och skicka-knappen längst ned.](media/fxt-cluster-config/enable-ha.png)

2. Klicka på rutan **Aktivera HA** och klicka på knappen **Skicka.** 

En avisering visas på **instrumentpanelen** för att bekräfta att HA är aktiverat.

![Instrumentpanelstabell som visar meddelandet "HA är nu helt konfigurerad"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Nästa steg

När du har lagt till alla noder i klustret fortsätter du installationen genom att konfigurera klustrets långtidslagring.

> [!div class="nextstepaction"]
> [Lägga till backend-lagring och konfigurera det virtuella namnområdet](fxt-add-storage.md)