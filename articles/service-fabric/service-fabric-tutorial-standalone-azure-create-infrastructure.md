---
title: Självstudiekursen skapar infrastrukturen för ett Service Fabric-kluster på Azure Virtual Machines – Azure Service Fabric | Microsoft Docs
description: I den här självstudien får du lära dig hur du ställer in Azure VM-infrastrukturen att köra ett Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/25/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: b5f2f77b3caed483aed1736bd510096d44329284
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276008"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Självstudier: Skapa Virtuella Azure-infrastrukturen för att vara värd för Service Fabric-kluster

Med fristående Service Fabric-kluster kan du välja miljö och skapa kluster enligt metoden ”valfritt operativsystem, valfritt moln” som präglar Service Fabric. I den här självstudiekursen, skapa ett fristående kluster på virtuella Azure-datorer och installera ett program på den.

Den här självstudien ingår i en serie. I den här artikeln får skapa du Virtuella Azure-resurser som krävs för att vara värd för ditt fristående kluster med Service Fabric. I kommande artiklar måste du installera det fristående Service Fabric-paketet, installera ett exempelprogram i klustret och slutligen rensa klustret.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa en uppsättning AzureVM instanser
> * Ändra säkerhetsgruppen
> * Logga in på någon av instanserna
> * Förbereda instansen för Service Fabric

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.  Om du inte redan har ett konto, går du till den [Azure-portalen](https://portal.azure.com) att skapa en.

## <a name="create-azure-virtual-machine-instances"></a>Skapa Azure VM-instanser

1. Logga in på Azure portal och väljer **virtuella datorer** (inte virtuella datorer (klassisk)).

   ![Azure-portalen VM][az-console]

2. Välj den **Lägg till** knapp, vilket öppnar upp den **skapa en virtuell dator** formuläret.

3. I den **grunderna** fliken, måste du välja den prenumeration och resursgrupp grupp som du vill (med en ny resursgrupp rekommenderas).

4. Ändra den **bild** skriver till **Windows Server 2016 Datacenter**. 
 
5. Ändra instansen **storlek** till **Standard DS2 v2**. Ange ett **användarnamn** och **lösenord**, loggas de är.

6. Lämna den **regler för inkommande portar** blockeras just nu; vi kommer att konfigurera dem i nästa avsnitt.

7. I den **nätverk** fliken, skapa en ny **virtuellt nätverk** och anteckna dess namn.

8. Nu ska vi konfigurera den **NIC nätverkssäkerhetsgrupp** till **Avancerat**. Skapa en ny säkerhetsgrupp, ange dess namn och skapa följande regler för att tillåta TCP-trafik från valfri källa:

   ![sf-inbound][sf-inbound]

   * Port `3389`för RDP och ICMP (grundläggande connectivity).
   * Portar `19000-19003`, för Service Fabric.
   * Portar `19080-19081`, för Service Fabric.
   * Port `8080`, för begäran från webbläsaren.

   > [!TIP]
   > För att du ska kunna ansluta dina virtuella datorer tillsammans i Service Fabric, måste de virtuella datorer som är värd för din infrastruktur ha samma autentiseringsuppgifter.  Det finns två vanliga metoder för att få likadana autentiseringsuppgifter: Anslut dem till samma domän, eller ange samma administratörslösenord på varje virtuell dator. Som tur är kan Azure kan alla virtuella datorer på samma **virtuellt nätverk** att enkelt ansluta, så vi kommer att se till att ha alla våra instanser i samma nätverk.

9. Lägg till en annan regel. Ange källan för att vara **Tjänsttagg** och ange källtjänsttagg till **VirtualNetwork**. Service Fabric kräver följande portar vara öppna för kommunikation inom klustret: 135,137-139,445,20001-20031,20606-20861.

   ![inkommande vnet][vnet-inbound]

10. Resten av alternativen accepteras i sitt ursprungsläge. Om du vill granska dem och sedan starta den virtuella datorn.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Skapa flera instanser för Service Fabric-kluster

Starta ytterligare två **virtuella datorer**, var noga med att upprätthålla samma inställningar som beskrivs i föregående avsnitt. Särskilt, upprätthålla samma administratörsanvändarnamn och lösenord. Den **virtuellt nätverk** och **NIC nätverkssäkerhetsgrupp** bör inte återskapas; Välj dem som du redan har skapat från den nedrullningsbara menyn. Det kan ta några minuter för var och en av dina instanser som ska distribueras.

## <a name="connect-to-your-instances"></a>Ansluta till dina instanser

1. Välj en av dina instanser från den **VM** avsnittet.

2. I den **översikt** och anteckna den *privata* IP-adress. Klicka sedan på **Connect**.

3. I den **RDP** fliken måste du komma ihåg att vi använder den offentliga IP-adress och port 3389, som vi särskilt öppnas tidigare. Ladda ned RDP-filen.
 
4. Öppna RDP-filen och när du uppmanas ange användarnamnet och lösenordet du angav i VM-installationsprogrammet.

5. När du är ansluten till en instans, måste du kontrollera att remote registry kördes, aktivera SMB och öppna portarna som krävs för SMB och fjärregistret.

   Om du vill aktivera SMB måste är det här PowerShell-kommando:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Om du vill öppna portarna i brandväggen använder du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Upprepa processen för dina andra instanser och igen noteras privata IP-adresser.

## <a name="verify-your-settings"></a>Verifiera dina inställningar

1. Verifiera grundläggande anslutning genom att ansluta till en av de virtuella datorerna med RDP.

2. Öppna den **kommandotolk** från i den virtuella datorn och sedan använda kommandot ping för att ansluta från en virtuell dator till en annan, ersätter den nedan IP-adress med någon av privata IP-adresser du antecknade tidigare (inte IP-Adressen för den virtuella datorn som du är ansluten till redan).

   ```
   ping 172.31.20.163
   ```

   Om dina utdata ser ut så här `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` upprepade fyra gånger, fungerar anslutningen mellan instanserna.

3. Nu ska du kontrollera att din SMB-delning fungerar med följande kommando:

   ```
   net use * \\172.31.20.163\c$
   ```

   `Drive Z: is now connected to \\172.31.20.163\c$.` ska returneras som utdata.


   Dina instanser är nu korrekt förberedda för Service Fabric.

## <a name="next-steps"></a>Nästa steg

I del ett av serien lärde du dig att starta tre Azure VM-instanser och förbereda dem konfigurerad för Service Fabric-installationen:

> [!div class="checklist"]
> * Skapa en uppsättning Azure VM-instanser
> * Ändra säkerhetsgruppen
> * Logga in på någon av instanserna
> * Förbereda instansen för Service Fabric

Gå vidare till avsnitt två i serien för att konfigurera Service Fabric på klustret.

> [!div class="nextstepaction"]
> [Installera Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
