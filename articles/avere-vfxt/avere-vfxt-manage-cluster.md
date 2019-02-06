---
title: Hantera Avere vFXT kluster – Azure
description: Hur du hantera Avere kluster – lägga till eller ta bort noder, starta om, stoppa eller förstör klustret vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: bc91b052d3d69924af9afeb012c0ebb5be01dfbf
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745562"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Hantera Avere vFXT-klustret

När du har skapat klustret, kan du behöva lägga till noder eller stoppa eller starta om klustret. Och när projektet är klar måste du veta hur du stoppar och permanent ta bort klustret. 

Beroende på hanteringsaktivitet kluster kan du behöva använda Kontrollpanelen Avere vfxt.py kommandoraden kluster Skapandeskriptet eller Azure-portalen för att göra det. 

Den här tabellen innehåller en översikt av vilka verktyg kan användas för varje aktivitet. 

| Åtgärd | Avere Kontrollpanelen | vfxt.py  | Azure Portal |
| --- | --- | --- | --- |
| Lägg till noder | nej | ja | nej |
| Tar bort klusternoder | ja | nej | nej |
| Stoppa en klusternod | Ja (kan också starta om tjänsterna eller omstart) | nej | stänga av en nod VM från portalen tolkas som ett nodfel |
| Starta en stoppad nod | nej | nej | ja |
| Ta bort en enskild klusternod | nej | nej | ja |
| Starta om klustret |  |  |  |
| Stäng av eller stoppa klustret på ett säkert sätt | ja | ja | nej |
| Förstör klustret  | nej | ja | Ja, men är inte att garantera dataintegriteten |

Detaljerade anvisningar för varje verktyg finns nedan.

## <a name="about-stopped-instances-in-azure"></a>Om stoppad instanser i Azure

När du stänger ner eller stoppa alla virtuella Azure-datorer stoppas ådrar sig några beräkningskostnader, men du måste betala för sin lagring. Om du stänger en vFXT nod eller hela vFXT klustret och du inte planerar att starta om den, bör du använda Azure-portalen för att ta bort de relaterade virtuella datorerna. 

I Azure-portalen en *stoppats* noden (som kan startas) visar statusen **stoppats** i Azure-portalen; en *bort* noden visar statusen **stoppas (frigjord)**  och det inte längre tillkommer avgifter för beräkning eller lagring.

Kontrollera att alla ändrade data har skrivit från cachen till backend-lagring med hjälp av alternativen Avere på Kontrollpanelen eller vfxt.py att stoppa eller stänga av klustret innan du tar bort den virtuella datorn.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Hantera kluster med Avere Kontrollpanelen 

Kontrollpanelen för Avere kan användas för dessa uppgifter: 

* Stoppa eller starta om enskilda noder
* Ta bort en nod från klustret
* Stoppa eller starta om hela klustret

Avere Kontrollpanelen prioriterar dataintegritet, så görs ett försök att skriva ändrade data till serverdelslagring innan en eventuellt destruktiv åtgärd. Detta gör det till en säkrare alternativ än Avere-portalen. 

Åtkomst till Avere Kontrollpanelen från en webbläsare. Följ instruktionerna i [åtkomst till klustret vFXT](avere-vfxt-cluster-gui.md) om du behöver hjälp.

### <a name="manage-nodes-with-avere-control-panel"></a>Hantera noder med Avere Kontrollpanelen

Den **FXT noder** inställningssidan har kontroller för att hantera enskilda noder.

Om du vill stänga av, starta om eller ta bort en nod, hitta noden i listan på den **FXT noder** sidan och klicka på lämplig i dess **åtgärder** kolumn.

> [!NOTE] 
> IP-adresser kan också flytta mellan klusternoderna när antalet aktiva noder ändras.

Läs [kluster > noder FXT](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) i Avere kluster inställningar för mer information.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Stoppa eller starta om klustret med Avere Kontrollpanelen

Den **systemunderhåll** inställningssidan har kommandon för att starta om klustertjänsterna, starta om klustret eller på ett säkert sätt behöva stänga klustret av. Läs [Administration > systemunderhåll](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (i Avere kluster inställningar guide) för information.

När ett kluster stängs av, den publicerar meddelanden till den **instrumentpanelen** fliken först. Efter en liten stund svarar Avere Kontrollpanelen sessionen, vilket innebär att klustret har avslutats.

## <a name="manage-the-cluster-with-vfxtpy"></a>Hantera kluster med vfxt.py

vfxt.PY är ett kommandoradsverktyg för att skapa och hantera kluster. 

vfxt.PY förinstallerats på kontrollanten kluster VM. Om du vill installera det på ett annat system finns i dokumentationen på <https://github.com/Azure/AvereSDK>.

Skriptet vfxt.py kan användas för dessa hanteringsaktiviteter för klustret:

* Lägga till nya noder i ett kluster
* Stoppa eller starta ett kluster  
* Ta bort ett kluster

Vfxt.py operations försöker kontrollera ändrade data lagras permanent på backend-storage innan du stänger av eller förstöra det kluster eller en nod som Avere på Kontrollpanelen. Detta gör det till en säkrare alternativ än Avere-portalen.

En fullständig vfxt.py användningsguiden finns på GitHub: [Hantering av molnet med vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Lägg till noder med vfxt.py

Ett kommandoskript för att lägga till noder i klustret ska tas med på kontrollanten kluster. Leta upp ``./add-nodes`` på kontrollanten och öppna den i en textredigerare för att anpassa den med din klusterinformation. 

Klustret måste köras för att använda det här kommandot. 

Ange följande värden: 

* Resursgruppens namn för klustret, samt för nätverk och lagringsresurser om de inte är samma som klustret
* Klusterplats
* Klusternätverk och undernät 
* Klusterrollen noden åtkomst 
* Klustrets IP-adress för hantering och administrativa lösenord 
* Antalet noder för att lägga till (1, 2 eller 3)
* Noden instans typ och cache storlek värden 

Om du inte använder prototypen, måste du skapa ett kommando som i följande, inklusive alla uppgifter som beskrivs ovan. 

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \ 
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role ROLE_NAME \
   --log ~/vfxt.log
```

Mer information finns i [lägga till noder i ett kluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) i guiden vfxt.py användning.

### <a name="stop-a-cluster-with-vfxtpy"></a>Stoppa ett kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Starta ett stoppat kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

Eftersom klustret har stoppats, måste du ange instans-ID: n för att ange klusternoderna. Läs [anger vilka kluster för att ändra](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) i guiden vfxt.py användning om du vill veta mer.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Ta bort ett kluster med vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Alternativet ``--quick-destroy`` kan användas om du inte vill skriva ändrade data från kluster-cachen.

Läs den [vfxt.py användningsguiden](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) för ytterligare information.  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Hantera kluster virtuella datorer från Azure portal 

Azure-portalen kan användas för att ta bort klustret virtuella datorer separat, men dataintegritet är inte säkert om klustret inte stänga av strömmen först. 

Azure-portalen kan användas för dessa hanteringsaktiviteter för klustret: 

* Starta en stoppad vFXT-nod
* Stoppa en nod för enskilda vFXT (klustret tolkar det som ett nodfel)
* Ta bort ett kluster för vFXT *om* du inte behöver så att ändrade data i cacheminnet för klustret är avsedd att core-filer
* Ta bort permanent vFXT noder och andra resurser i klustret när de har stängts av på ett säkert sätt

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Starta om vFXT instanser från Azure portal

Om du måste starta om en stoppad nod, måste du använda Azure-portalen. Välj **virtuella datorer** i den vänstra menyn och sedan klicka på VM-namnet i listan för att öppna dess översiktssidan.

Klicka på den **starta** längst upp på översiktssidan för att återaktivera den virtuella datorn.

![Azure portal skärmbild som visar alternativet att starta en stoppad virtuell dator](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Ta bort noder

Om du vill ta bort en nod från klustret vFXT men behålla resten av klustret bör du först [ta bort noden från klustret](#manage-nodes-with-avere-control-panel) med Avere på Kontrollpanelen.

> [!CAUTION]
> Om du tar bort en nod utan att ta bort den först från vFXT klustret kan data gå förlorade.

För att permanent ta bort en eller flera instanser som används som vFXT nod, använder du Azure portal.
Välj **virtuella datorer** i den vänstra menyn och sedan klicka på VM-namnet i listan för att öppna dess översiktssidan.

Klicka på den **ta bort** längst upp på översiktssidan att permanent ta bort den virtuella datorn.

Du kan använda den här metoden om du permanent ta bort noder när de har stängts av på ett säkert sätt. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Förstör klustret från Azure portal

> [!NOTE] 
> Om du vill att alla återstående klientändringar i cacheminnet som ska skrivas till backend-lagring antingen använda vfxt.py `--destroy` alternativet eller använda Avere på Kontrollpanelen för att stänga av klustret korrekt innan du tar bort noden-instanser i Azure-portalen.

Du kan ta bort noden instanser permanent genom att ta bort dem i Azure-portalen. Du kan ta bort dem en i taget som beskrivs ovan, eller du kan använda den **virtuella datorer** sidan för att hitta alla virtuella datorer i klustret, markerar du dem med kryssrutorna och klicka på den **ta bort** knappen Ta bort dem alla en åtgärd.

![Lista över virtuella datorer i portalen, filtreras efter termen ”kluster”, med tre av fyra kontrolleras och markerat](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Ta bort ytterligare klusterresurser från Azure portal

Om du har skapat ytterligare resurser specifikt för klustret vFXT kanske du vill ta bort dem som en del av man bryta ner klustret. Du bör inte ta bort element som innehåller data som du behöver eller alla objekt som delas med andra projekt.

Överväg att ta bort dessa komponenter utöver tar bort noder i klustret: 

* Kluster-controller VM
* Datadiskar kopplade klusternoder
* Nätverksgränssnitt och offentliga IP-adresser som är associerade med klusterkomponenter
* Virtuella nätverk
* Storage-konton (**endast** om de innehåller inga viktiga data)
* Tillgänglighetsuppsättning 

![Azure-portalen ”alla resurser” lista över resurser som skapats för ett testkluster](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Ta bort resursgruppen i ett kluster från Azure portal

Om du har skapat en resursgrupp specifikt för att rymma både klustret kan du förstöra alla relaterade resurser för klustret genom att förstöra resursgruppen. 

> [!Caution] 
> Ta bort resursgruppen bara om du är säker på att inget värde finns i gruppen. Kontrollera exempelvis att du har flyttat alla nödvändiga data från alla storage-behållare i resursgruppen.  

Ta bort en resursgrupp genom att klicka på **resursgrupper** på den vänstra menyn i portalen och filtrera listan över resursgrupper för att ta reda på vilken du skapade för vFXT-klustret. Välj resursgruppen och klicka på de tre punkterna till höger i panelen. Välj **Ta bort resursgrupp**. Portalen uppmanas du att bekräfta borttagningen, som inte går att ångra.  

![Resursgruppen som visar ”ta bort resursgrupp”-åtgärd](media/avere-vfxt-delete-resource-group.png)
