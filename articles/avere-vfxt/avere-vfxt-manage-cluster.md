---
title: Hantera Avere vFXT-klustret - Azure
description: Hantera Avere-kluster – lägga till eller ta bort noder, starta om, stoppa eller förstöra vFXT-klustret
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 94db4a93025b6e3d633368d924e3e0c518d108ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153487"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Hantera Avere vFXT-klustret

Vid något tillfälle i livscykeln för ditt Avere vFXT för Azure-kluster kan du behöva lägga till klusternoder eller starta om klustret. När projektet är klart måste du veta hur du stoppar klustret och tar bort det permanent.

I den här artikeln beskrivs hur du lägger till eller tar bort klusternoder och andra grundläggande klusteråtgärder. Om du behöver ändra klusterinställningarna eller övervaka dess arbete använder du [Kontrollpanelen i Avere](avere-vfxt-cluster-gui.md).

Beroende på hanteringsuppgiften kan du behöva använda ett av tre olika verktyg: Avere Kontrollpanel, vfxt.py kommandoradsklusterhanteringsskript och Azure-portalen.

Den här tabellen ger en översikt över vilka verktyg som kan användas för varje uppgift.

| Åtgärd | Avere kontrollpanel | vfxt.py  | Azure Portal |
| --- | --- | --- | --- |
| Lägga till klusternoder | nej | ja | nej |
| Ta bort klusternoder | ja | nej | nej |
| Stoppa en klusternod | ja (kan också starta om tjänster eller starta om) | nej | stänga av en nod-VM från portalen tolkas som ett nodfel |
| Starta en stoppad nod | nej | nej | ja |
| Förstöra en enskild klusternod | nej | nej | ja |
| Starta om klustret |  |  |  |
| Stänga av eller stoppa klustret på ett säkert sätt | ja | ja | nej |
| Förstöra klustret  | nej | ja | Ja, men dataintegritet garanteras inte |

Detaljerade instruktioner för varje verktyg finns nedan.

## <a name="about-stopped-instances-in-azure"></a>Om stoppade instanser i Azure

När du stänger av eller stoppar en Virtuell Azure-dator slutar den att ådra sig beräkningsavgifter, men du måste ändå betala för lagringen. Om du stänger av en vFXT-nod eller hela vFXT-klustret och du inte tänker starta om den, bör du använda Azure-portalen för att ta bort relaterade virtuella datorer.

I Azure-portalen visar en *stoppad* nod (som kan startas om) statusen **som stoppats** i Azure-portalen. En *borttagen* nod visar statusen **stoppad (deallocated)** och den medför inte längre beräknings- eller lagringsavgifter.

Innan du tar bort den virtuella datorn kontrollerar du att alla ändrade data har skrivits från cacheminnet till serverdelslagringen med hjälp av Avere-kontrollpanelen eller vfxt.py alternativ för att stoppa eller stänga av klustret.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Hantera klustret med Avere Kontrollpanelen

Kontrollpanelen Avere kan användas för dessa uppgifter:

* Stoppa eller starta om enskilda noder
* Ta bort en nod från klustret
* Stoppa eller starta om hela klustret

Avere Kontrollpanelen prioriterar dataintegritet, så den försöker skriva alla ändrade data till backend-lagring innan en eventuellt destruktiv åtgärd. Detta gör det till ett säkrare alternativ än Azure-portalen.

Få tillgång till Avere Kontrollpanelen från en webbläsare. Följ instruktionerna i [Access vFXT-klustret](avere-vfxt-cluster-gui.md) om du behöver hjälp.

### <a name="manage-nodes-with-avere-control-panel"></a>Hantera noder med Avere Kontrollpanelen

Sidan **FXT-noder** inställningar har kontroller för hantering av enskilda noder.

Om du vill stänga av, starta om eller ta bort en nod hittar du noden i listan på sidan **FXT-noder** och klickar på lämplig knapp i kolumnen **Åtgärder.**

> [!NOTE]
> IP-adresser kan flyttas mellan klusternoder när antalet aktiva noder ändras.

Läs [Kluster > FXT-noder](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) i Avere-klusterinställningsguiden för mer information.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Stoppa eller starta om klustret med Avere Kontrollpanelen

Sidan **Inställningar för systemunderhåll** har kommandon för att starta om klustertjänster, starta om klustret eller stänga av klustret på ett säkert sätt. Mer information finns i [administration > systemunderhåll](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (i avere-klusterinställningsguiden).

När ett kluster börjar stängas av bokför det tillståndsmeddelanden på fliken **Instrumentpanel.** Efter en stund slutar meddelandena och så småningom slutar Avere Kontrollpanelen-sessionen att svara, vilket innebär att klustret har stängts av.

## <a name="manage-the-cluster-with-vfxtpy"></a>Hantera klustret med vfxt.py

vfxt.py är ett kommandoradsverktyg för att skapa och hantera kluster.

vfxt.py är förinstallerad på klustret controller VM. Om du vill installera det på ett annat <https://github.com/Azure/AvereSDK>system läser du dokumentationen på .

Det vfxt.py skriptet kan användas för dessa klusterhanteringsuppgifter:

* Lägga till nya noder i ett kluster
* Stoppa eller starta ett kluster  
* Förstöra ett kluster

Precis som Avere Kontrollpanelen försöker vfxt.py åtgärder se till att ändrade data lagras permanent på backend-lagring innan du stänger av eller förstör klustret eller noden. Detta gör det till ett säkrare alternativ än Azure-portalen.

En komplett vfxt.py användningsguide finns på GitHub: [Molnklusterhantering med vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Lägga till klusternoder med vfxt.py

Ett exempelkommandoskript för att lägga till klusternoder ingår i klusterstyrenheten. Leta ``./add-nodes`` upp på handkontrollen och öppna den i en redigerare för att anpassa den med klusterinformationen.

Klustret måste köras för att använda det här kommandot.

Ange följande värden:

* Resursgruppsnamn för klustret och även för nätverks- och lagringsresurser om de inte finns i samma resursgrupp som klustret
* Klusterplats
* Klusternätverk och undernät
* Åtkomstroll för klusternod (använd den inbyggda [rollavere-operatorn)](../role-based-access-control/built-in-roles.md#avere-operator)
* IP-adress för klusterhantering och administrativt lösenord
* Antal noder som ska läggas till (1, 2 eller 3)
* Nodförekomsttyp och cachestorleksvärden

Om du inte använder prototypen måste du skapa ett kommando som följande, inklusive all information som beskrivs ovan.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Mer information finns i [Lägg till noder i ett kluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) i användningsguiden för vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Stoppa ett kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Starta ett stoppat kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Eftersom klustret stoppas måste du skicka instansidentifierare för att ange klusternoderna. Läs [Ange vilket kluster som ska ändras](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) i vfxt.py användningsguide för att lära dig mer.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Förstöra ett kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Alternativet ``--quick-destroy`` kan användas om du inte vill spara ändrade data från klustercachen.

Läs [vfxt.py användningsguiden](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) för ytterligare information.

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Hantera virtuella kluster-datorer från Azure-portalen

Azure-portalen kan användas för att förstöra kluster virtuella datorer individuellt, men dataintegritet garanteras inte om klustret inte stängs av rent först.

Azure-portalen kan användas för dessa klusterhanteringsuppgifter:

* Starta en stoppad vFXT-nod
* Stoppa en enskild vFXT-nod (klustret tolkar detta som ett nodfel)
* Förstör ett vFXT-kluster *om* du inte behöver se till att ändrade data i klustercachen skrivs till kärnfilerna
* Ta bort vFXT-noder och andra klusterresurser permanent efter att de har stängts av på ett säkert sätt

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Starta om vFXT-instanser från Azure-portalen

Om du behöver starta om en stoppad nod måste du använda Azure-portalen. Välj **Virtuella datorer** i den vänstra menyn och klicka sedan på vm-namnet i listan för att öppna dess översiktssida.

Klicka på **Start-knappen** högst upp på översiktssidan för att återaktivera den virtuella datorn.

![Azure portal skärm som visar alternativet att starta en stoppad virtuell dator](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Ta bort klusternoder

Om du vill ta bort en nod från vFXT-klustret men behålla resten av klustret bör du först [ta bort noden från klustret](#manage-nodes-with-avere-control-panel) med Avere-kontrollpanelen.

> [!CAUTION]
> Om du tar bort en nod utan att först ta bort den från vFXT-klustret kan data gå förlorade.

Om du vill förstöra en eller flera instanser som används som vFXT-nod permanent använder du Azure-portalen.
Välj **Virtuella datorer** i den vänstra menyn och klicka sedan på vm-namnet i listan för att öppna dess översiktssida.

Klicka på knappen **Ta bort** högst upp på översiktssidan för att permanent förstöra den virtuella datorn.

Du kan använda den här metoden för att permanent ta bort klusternoder när de har stängts av på ett säkert sätt.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Förstöra klustret från Azure-portalen

> [!NOTE]
> Om du vill att eventuella återstående klientändringar i cacheminnet ska skrivas `--destroy` till serverdelslagring, antingen använda alternativet vfxt.py eller använd Avere Kontrollpanelen för att stänga av klustret på ett rent sätt innan nodinstanserna tas bort i Azure-portalen.

Du kan förstöra nodinstanser permanent genom att ta bort dem i Azure-portalen. Du kan ta bort dem en i taget enligt beskrivningen ovan, eller så kan du använda sidan **Virtuella datorer** för att hitta alla virtuella klusterdatorer, markera dem med kryssrutorna och klicka på knappen **Ta bort** för att ta bort dem alla i en åtgärd.

![Lista över virtuella datorer i portalen, filtrerad efter termen "kluster", med tre av de fyra markerade och markerade](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Ta bort ytterligare klusterresurser från Azure-portalen

Om du har skapat ytterligare resurser specifikt för vFXT-klustret kanske du vill ta bort dem som en del av att riva klustret. Förstör inte element som innehåller data som du behöver, eller objekt som delas med andra projekt.

Förutom att ta bort klusternoderna bör du överväga att ta bort dessa komponenter:

* Vm-styrenheten för klusterstyrenhet
* Datadiskar som är associerade med klusternoder
* Nätverksgränssnitt och offentliga IPs som är associerade med klusterkomponenter
* Virtuella nätverk
* Förvaringsbehållare och lagringskonton **(endast** om de inte innehåller några viktiga data)
* Tillgänglighetsuppsättning

![Azure portal "alla resurser" lista som visar resurser som skapats för ett testkluster](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Ta bort ett klusters resursgrupp från Azure-portalen

Om du har skapat en resursgrupp specifikt för att gruppera klustret kan du förstöra alla relaterade resurser för klustret genom att förstöra resursgruppen.

> [!Caution]
> Förstör bara resursgruppen om du är säker på att inget av värde finns i gruppen. Kontrollera till exempel att du har flyttat alla nödvändiga data från alla lagringsbehållare i resursgruppen.  

Om du vill ta bort en resursgrupp klickar du på **Resursgrupper** på portalens vänstra meny och filtrerar listan över resursgrupper för att hitta den som du skapade för vFXT-klustret. Markera resursgruppen och klicka på de tre punkterna till höger på panelen. Välj **Ta bort resursgrupp**. Portalen kommer att be dig att bekräfta radering, vilket är oåterkalleligt.

![Resursgrupp med åtgärden "Ta bort resursgrupp"](media/avere-vfxt-delete-resource-group.png)
