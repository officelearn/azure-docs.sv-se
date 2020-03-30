---
title: Använda Azure HPC-cache- och Azure NetApp-filer
description: Så här använder du Azure HPC-cache för att förbättra åtkomsten till data som lagras med Azure NetApp-filer
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238680"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Använda Azure HPC-cache med Azure NetApp-filer

Du kan använda [Azure NetApp-filer](https://azure.microsoft.com/services/netapp/) som ett lagringsmål för din Azure HPC-cache. I den här artikeln beskrivs hur de två tjänsterna kan arbeta tillsammans och du kan ge tips om hur du konfigurerar dem.

Azure NetApp Files kombinerar sitt ONTAP-operativsystem med skalbarheten och hastigheten för Microsoft Azure. Med den här kombinationen kan användare flytta etablerade arbetsflöden till molnet utan att skriva om koden.

Genom att lägga till en Azure HPC-cachekomponent kan du förbättra filåtkomsten genom att presentera flera Azure NetApp-filvolymer i ett aggregerat namnområde. Det kan ge kantcache för volymer som finns i en annan tjänstregion. Det kan också förbättra prestanda på efterfrågan på volymer som har skapats på lägre servicenivåer för att spara kostnader.

## <a name="overview"></a>Översikt

Om du vill använda ett Azure NetApp-filsystem som backend-lagring med Azure HPC-cache följer du den här processen.

1. Skapa Azure NetApp Files-systemet och volymerna enligt riktlinjerna i [Planera ditt system nedan](#plan-your-azure-netapp-files-system).
1. Skapa Azure HPC-cachen i den region där du behöver filåtkomst. (Använd instruktionerna i [Skapa en Azure HPC-cache](hpc-cache-create.md).)
1. [Definiera lagringsmål](#create-storage-targets-in-the-cache) i cacheminnet som pekar på Azure NetApp-filvolymerna. Skapa ett cachelagringsmål för varje unik IP-adress som används för att komma åt volymerna.
1. Låt klienter [montera Azure HPC-cachen](#mount-storage-targets) i stället för att montera Azure NetApp Files-volymer direkt.

## <a name="plan-your-azure-netapp-files-system"></a>Planera ditt Azure NetApp-filsystem

När du planerar ditt Azure NetApp Files-system bör du vara uppmärksam på objekten i det här avsnittet för att se till att du kan integrera det smidigt med Azure HPC-cache.

Läs även [Azure NetApp Files-dokumentationen](../azure-netapp-files/index.yml) innan du skapar volymer för användning med Azure HPC-cache.

### <a name="nfs-client-access-only"></a>Endast NFS-klientåtkomst

Azure HPC Cache stöder för närvarande endast NFS-åtkomst. Den kan inte användas med SMB ACL eller POSIX-lägesbitvolymer.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Exklusivt undernät för Azure NetApp-filer

Azure NetApp Files använder ett enda delegerat undernät för sina volymer. Inga andra resurser kan använda det undernätet. Dessutom kan bara ett undernät i ett virtuellt nätverk användas för Azure NetApp-filer. Läs mer i [Riktlinjer för azure netapp-filer nätverksplanering](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Delegerad undernätsstorlek

Använd den minsta storleken för det delegerade undernätet när du skapar ett Azure NetApp-filsystem som ska användas med Azure HPC-cache.

Den minsta storleken, som anges med netmask /28, ger 16 IP-adresser. I praktiken använder Azure NetApp-filer endast tre av de tillgängliga IP-adresserna för volymåtkomst. Det innebär att du bara behöver skapa tre lagringsmål i Azure HPC-cachen för att täcka alla volymer.

Om det delegerade undernätet är för stort är det möjligt för Azure NetApp Files-volymerna att använda fler IP-adresser än en enda Azure HPC-cacheinstans kan hantera. En enda cache kan ha högst tio lagringsmål.

Snabbstartsexempelet i Azure NetApp Files-dokumentationen använder 10.7.0.0/16 för det delegerade undernätet, vilket ger ett undernät som är för stort.

### <a name="capacity-pool-service-level"></a>Servicenivå för kapacitetspool

När du väljer servicenivå för din kapacitetspool bör du tänka på arbetsflödet. Om du ofta skriver tillbaka data till Azure NetApp Files-volymen kan cachens prestanda begränsas om tillbakaskrivningstiden är långsam. Välj en hög servicenivå för volymer som ofta kommer att skrivas.

Volymer med låga servicenivåer kan också visa viss fördröjning i början av en aktivitet medan cachen fyller innehåll i förfyllande. När cacheminnet är igång med en bra fungerande uppsättning filer, bör fördröjningen bli omärbar.

Det är viktigt att planera servicenivån för kapacitetspoolen i förväg, eftersom den inte kan ändras när den har skapats. En ny volym måste skapas i en annan kapacitetspool och data kopieras över.

Observera att du kan ändra lagringskvoten för en volym och storleken på kapacitetspoolen utan att störa åtkomsten.

## <a name="create-storage-targets-in-the-cache"></a>Skapa lagringsmål i cacheminnet

När ditt Azure NetApp Files-system har konfigurerats och Azure HPC-cachen har skapats definierar du lagringsmål i cacheminnet som pekar på filsystemvolymerna.

Skapa ett lagringsmål för varje IP-adress som används av dina Azure NetApp-filvolymer. IP-adressen visas på volymens monteringsinstruktionssida.

Om flera volymer delar samma IP-adress kan du använda ett lagringsmål för dem alla.  

Följ [monteringsinstruktionerna i Dokumentationen](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) för Azure NetApp Files för att hitta de IP-adresser som ska användas.

Du kan också hitta IP-adresser med Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Exportnamn på Azure NetApp Files-systemet har en enda sökvägskomponent. Försök inte skapa ett lagringsmål för ``/`` rotexporten i Azure NetApp-filer, eftersom exporten inte ger filåtkomst.

Det finns inga särskilda begränsningar för virtuella namnområdessökvägar för dessa lagringsmål.

## <a name="mount-storage-targets"></a>Montera lagringsmål

Klientdatorer bör montera cachen i stället för att montera Azure NetApp Files-volymerna direkt. Följ instruktionerna i [Montera Azure HPC-cachen](hpc-cache-mount.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du konfigurerar och använder [Azure NetApp-filer](../azure-netapp-files/index.yml)
* Kontakta [supporten](hpc-cache-support-ticket.md)om du vill ha hjälp med att planera och konfigurera Azure HPC-cachesystemet för att använda Azure NetApp-filer .
