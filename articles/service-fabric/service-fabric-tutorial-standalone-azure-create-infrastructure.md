---
title: Skapa infrastruktur för ett kluster på virtuella Azure-datorer
description: I den här självstudien får du lära dig hur du konfigurerar Azure VM-infrastrukturen för att köra ett Service Fabric-kluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 93a7e2507ab3a467ef83924479872694cae2dd5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614017"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Självstudiekurs: Skapa Azure VM-infrastruktur för att vara värd för ett Service Fabric-kluster

Med fristående Service Fabric-kluster kan du välja en egen miljö och skapa ett kluster som en del av metoden ”valfritt operativsystem, valfritt moln” som används i Service Fabric. I den här självstudieserien skapar du ett fristående kluster som finns på virtuella Azure-datorer och installerar ett program på den.

Den här självstudien ingår i en serie. I den här artikeln genererar du azure VM-resurser som krävs för att vara värd för ditt fristående kluster av Service Fabric. I kommande artiklar måste du installera det fristående Service Fabric-paketet, installera ett exempelprogram i klustret och slutligen rensa klustret.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa en uppsättning AzureVM-instanser
> * Ändra säkerhetsgruppen
> * Logga in på någon av instanserna
> * Förbereda instansen för Service Fabric

## <a name="prerequisites"></a>Krav

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.  Om du inte redan har ett konto går du till [Azure-portalen](https://portal.azure.com) för att skapa ett.

## <a name="create-azure-virtual-machine-instances"></a>Skapa Azure-instanser för virtuella datorer

1. Logga in på Azure-portalen och välj **Virtuella datorer** (inte virtuella datorer (klassisk)).

   ![Virtuell Azure-portal][az-console]

2. Välj knappen **Lägg till,** som öppnar formuläret **Skapa en virtuell dator.**

3. På fliken **Grunderna** måste du välja den prenumerations- och resursgrupp du vill använda (med hjälp av en ny resursgrupp rekommenderas).

4. Ändra **avbildningstypen** till **Windows Server 2016 Datacenter**. 
 
5. Ändra **instansstorleken** till **Standard DS2 v2**. Ange ett **användarnamn** och **lösenord för**administratörer och notera vad de är.

6. Lämna **reglerna** för inkommande port blockerade för tillfället. vi kommer att konfigurera dem i nästa avsnitt.

7. På fliken **Nätverk** skapar du ett nytt **virtuellt nätverk** och tar del av namnet.

8. Ange sedan **säkerhetsgruppen** för nätverkskort till **Avancerat**. Skapa en ny säkerhetsgrupp, notera dess namn och skapa följande regler för att tillåta TCP-trafik från valfri källa:

   ![sf-inkommande][sf-inbound]

   * port `3389`för RDP och ICMP (grundläggande anslutning).
   * Portar `19000-19003`, för serviceinfrastruktur.
   * Portar `19080-19081`, för serviceinfrastruktur.
   * Port `8080`, för webbläsarbegäranden.

   > [!TIP]
   > För att du ska kunna ansluta dina virtuella datorer tillsammans i Service Fabric, måste de virtuella datorer som är värd för din infrastruktur ha samma autentiseringsuppgifter.  Det finns två vanliga metoder för att få likadana autentiseringsuppgifter: Anslut dem till samma domän, eller ange samma administratörslösenord på varje virtuell dator. Lyckligtvis tillåter Azure alla virtuella datorer i samma **virtuella nätverk** att enkelt ansluta, så vi kommer att se till att ha alla våra instanser i samma nätverk.

9. Lägg till en annan regel. Ange att källan ska vara **Service Tag** och ange källtjänsttaggen till **VirtualNetwork**. Service Fabric kräver att följande portar är öppna för kommunikation i klustret: 135 137-139 445 20001-20031 20606-20861.

   ![vnet-inkommande][vnet-inbound]

10. Resten av alternativen är godtagbara i standardtillståndet. Granska dem om du vill och starta sedan den virtuella datorn.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Skapa fler instanser för service fabric-klustret

Starta ytterligare två **virtuella datorer**, se till att behålla samma inställningar som beskrivs i föregående avsnitt. Särskilt underhålla samma administratörsanvändarnamn och lösenord. Säkerhetsgruppen **för virtuellt nätverk** och **nätverkskort** bör inte återskapas. välj de som du redan har skapat på rullgardinsmenyn. Det kan ta några minuter innan var och en av dina instanser distribueras.

## <a name="connect-to-your-instances"></a>Anslut till dina instanser

1. Välj en av dina instanser i avsnittet **Virtuell dator.**

2. På fliken **Översikt** bör du ta del av den *privata* IP-adressen. Klicka sedan på **Anslut**.

3. På fliken **RDP** bör du tänka på att vi använder den offentliga IP-adressen och port 3389, som vi specifikt öppnade tidigare. Hämta RDP-filen.
 
4. Öppna RDP-filen och ange det användarnamn och lösenord som du angav i vm-installationen när du uppmanas att ange det användarnamn och lösenord som du angav.

5. När du är ansluten till en instans måste du verifiera att fjärrregistret kördes, aktivera SMB och öppna nödvändiga portar för SMB och fjärrregister.

   Så här aktiverar du SMB:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Om du vill öppna portarna i brandväggen använder du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Upprepa den här processen för dina andra instanser och notera de privata IP-adresserna igen.

## <a name="verify-your-settings"></a>Verifiera dina inställningar

1. Om du vill validera den grundläggande anslutningen ansluter du till en av de virtuella datorerna med RDP.

2. Öppna **kommandotolken** inifrån den virtuella datorn och använd sedan ping-kommandot för att ansluta från en virtuell dator till en annan och ersätta under IP-adressen med en av de privata IP-adresser som du noterade tidigare (inte IP-adressen för den virtuella datorn som du redan är ansluten till).

   ```
   ping 172.31.20.163
   ```

   Om dina utdata ser ut så här `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` upprepade fyra gånger, fungerar anslutningen mellan instanserna.

3. Nu ska du kontrollera att din SMB-delning fungerar med följande kommando:

   ```
   net use * \\172.31.20.163\c$
   ```

   `Drive Z: is now connected to \\172.31.20.163\c$.` ska returneras som utdata.


   Nu är dina instanser ordentligt förberedda för Service Fabric.

## <a name="next-steps"></a>Nästa steg

I del ett av serien har du lärt dig hur du startar tre Azure VM-instanser och konfigurerade dem för Installationen av Service Fabric:

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
