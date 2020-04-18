---
title: Aktivera kapslad virtualisering i Virtuella Azure-datorer
description: Aktivera kapslad virtualisering i Virtuella Azure-datorer
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 7858bd467c6e3fd82fcb3803a98e96b265f17d23
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605259"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Aktivera kapslad virtualisering i en virtuell Azure-dator

Kapslad virtualisering stöds i flera azure-virtuella datorfamiljer. Den här funktionen ger stor flexibilitet när det gäller att stödja scenarier som utvecklings-, testnings-, utbildnings- och demonstrationsmiljöer.   

Den här artikeln går igenom att aktivera Hyper-V på en virtuell Azure-dator och konfigurera Internet-anslutning till den virtuella gästdatorn.

## <a name="create-a-nesting-capable-azure-vm"></a>Skapa en kapslingskompatibel Azure VM

Skapa en ny Virtuell Windows Server 2016 Azure-dator. En fullständig lista över storlekar på virtuella datorer som stöder kapsling finns i [artikeln Azure Compute Unit](acu.md).

Kom ihåg att välja en vm-storlek som är tillräckligt stor för att stödja kraven från en virtuell gästdator. I det här exemplet använder vi en azure-dator i D3_v3 storlek. 

Du kan se den regionala tillgängligheten för virtuella datorer i Dv3- eller Ev3-serien [här](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Detaljerade instruktioner om hur du skapar en ny virtuell dator finns i [Skapa och hantera virtuella windows-datorer med Azure PowerShell-modulen](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Anslut till din virtuella Azure-dator

Skapa en fjärrskrivbordsanslutning till den virtuella datorn.

1. Klicka på knappen **Anslut** på den virtuella datorns egenskaper. En protokollfil för fjärrskrivbord (.rdp-fil) skapas och hämtas.

2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) från Mac App Store.

3. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn och klicka sedan på **Ok**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Aktivera Hyper-V-funktionen på den virtuella Azure-datorn
Du kan konfigurera dessa inställningar manuellt eller så har vi tillhandahållit ett PowerShell-skript för att automatisera konfigurationen.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Alternativ 1: Använda ett PowerShell-skript för att konfigurera kapslad virtualisering
Ett PowerShell-skript för att aktivera kapslad virtualisering på en Windows Server 2016-värd finns på [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skriptet kontrollerar för förutsättningar och konfigurerar sedan kapslad virtualisering på den virtuella Azure-datorn. En omstart av den virtuella Azure-datorn är nödvändig för att slutföra konfigurationen. Det här skriptet kan fungera i andra miljöer men är inte garanterat. Kolla in Azure-blogginlägget med en livevideodemonstration om kapslad virtualisering som körs på Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Alternativ 2: Konfigurera kapslad virtualisering manuellt

1. Öppna PowerShell som administratör på den virtuella Azure-datorn. 

2. Aktivera Hyper-V-funktionen och hanteringsverktygen.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Det här kommandot startar om Den virtuella azure-datorn. Du kommer att förlora rdp-anslutningen under omstartsprocessen.
    
3. När Azure VM startas om återansluter du till den virtuella datorn med RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Konfigurera internetanslutning för den virtuella gästdatorn
Skapa ett nytt virtuellt nätverkskort för gästdatorn och konfigurera en NAT-gateway för att aktivera Internet-anslutning.

### <a name="create-a-nat-virtual-network-switch"></a>Skapa en virtuell NAT-nätverksväxel

1. Öppna PowerShell som administratör på den virtuella Azure-datorn.
   
2. Skapa en intern växel.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Visa egenskaperna för växeln och notera ifIndex för det nya kortet.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter (nettoadapter)](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Notera "ifIndex" för den virtuella växeln som du just skapade.
    
4. Skapa en IP-adress för NAT Gateway.
    
För att konfigurera gatewayen behöver du lite information om nätverket:    
  * IPAddress - NAT Gateway IP anger IPv4- eller IPv6-adressen som ska användas som standardgatewayadress för det virtuella nätverksundernätet. Den generiska formen är a.b.c.1 (till exempel "192.168.0.1"). Även om den slutliga positionen inte behöver vara 0,1, är den vanligtvis (baserat på prefixlängd). Vanligtvis bör du använda ett privat nätverksadressutrymme för RFC 1918. 
  * PrefixLength - Prefixets prefixlängd definierar den lokala undernätsstorleken (nätmask). Prefixets prefixlängd är ett heltalsvärde mellan 0 och 32. 0 skulle kartlägga hela Internet, skulle 32 bara tillåta en mappad IP. Gemensamma värden sträcker sig från 24 till 12 beroende på hur många IPs måste kopplas till NAT. Ett vanligt PrefixLängdgth är 24 – det här är en nätmask på 255.255.255.0.
  * InterfaceIndex - **ifIndex** är gränssnittsindex för den virtuella växel som skapades i föregående steg. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Skapa NAT-nätverket

För att konfigurera gatewayen måste du ange information om nätverket och NAT Gateway:
  * Namn - Det här är namnet på NAT-nätverket. 
  * Internt IPInterfaceAddressPrefix - NAT-undernätsprefixet beskriver både IP-prefixet för NAT Gateway ovanifrån och NAT-undernätsprefixets längd ovanifrån. Det allmänna formuläret kommer att vara a.b.c.0/NAT Subnet Prefix Length. 

Skapa ett nytt NAT-nätverk i PowerShell.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Skapa den virtuella gästdatorn

>[!IMPORTANT] 
>
>Azure-gästagenten stöds inte på kapslade virtuella datorer och kan orsaka problem på både värden och kapslade virtuella datorer. Installera inte Azure-agenten på kapslade virtuella datorer och använd inte en avbildning för att skapa de kapslade virtuella datorerna som redan har Azure-gästagenten installerad.

1. Öppna Hyper-V Manager och skapa en ny virtuell dator. Konfigurera den virtuella datorn så att det nya interna nätverket som du har skapat används.
    
    ![NätverkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Installera ett operativsystem på den virtuella gästdatorn.
    
    >[!NOTE] 
    >
    >Du behöver installationsmedia för att ett operativsystem ska kunna installeras på den virtuella datorn. I det här fallet använder vi Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Tilldela en IP-adress till den virtuella gästdatorn

Du kan tilldela en IP-adress till den virtuella gästdatorn antingen genom att manuellt ange en statisk IP-adress på den virtuella gästdatorn eller konfigurera DHCP på den virtuella Azure-datorn för att tilldela IP-adressen dynamiskt.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Alternativ 1: Konfigurera DHCP för att dynamiskt tilldela en IP-adress till den virtuella gästdatorn
Följ stegen nedan för att konfigurera DHCP på den virtuella värddatorn för dynamisk adresstilldelning.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Installera DCHP Server på den virtuella Azure-datorn

1. Öppna Serverhanteraren. Klicka på **Lägg till roller och funktioner**på instrumentpanelen . Guiden Lägg till roller och funktioner visas.
  
2. Klicka på **Nästa** tills sidan Serverroller i guiden.
  
3. Markera kryssrutan **DHCP Server** genom att klicka på **Lägg till funktioner**och klicka sedan på **Nästa** tills du har slutfört guiden.
  
4. Klicka på **Installera**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurera ett nytt DHCP-scope

1. Öppna DHCP-hanteraren.
  
2. Expandera servernamnet i navigeringsfönstret, högerklicka på **IPv4**och klicka på **Nytt scope**. Guiden Nytt scope visas, klicka på **Nästa**.
  
3. Ange ett namn och en beskrivning för scopet och klicka på **Nästa**.
  
4. Definiera ett IP-intervall för DIN DCHP-server (till exempel 192.168.0.100 till 192.168.0.200).
  
5. Klicka på **Nästa** tills sidan Standardgateway. Ange den IP-adress som du skapade tidigare (till exempel 192.168.0.1) som standardgateway och klicka sedan på **Lägg till**.
  
6. Klicka på **Nästa** tills guiden är klar och lämna alla standardvärden och klicka sedan på **Slutför**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Alternativ 2: Ange manuellt en statisk IP-adress på gästens virtuella dator
Om du inte har konfigurerat DHCP för att dynamiskt tilldela en IP-adress till gästdatorn följer du dessa steg för att ange en statisk IP-adress.

1. Öppna PowerShell som administratör på den virtuella Azure-datorn.

2. Högerklicka på den virtuella gästdatorn och klicka på Anslut.

3. Logga in på gästens virtuella dator.

4. Öppna Nätverks- och delningscenter på den virtuella gästen.

5. Konfigurera nätverkskortet för en adress inom intervallet för DET NAT-nätverk som du skapade i föregående avsnitt.

I det här exemplet ska du använda en adress i intervallet 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testa anslutning i gästvirtual dator

Öppna webbläsaren i den virtuella gästen och navigera till en webbsida.
    ![GästVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Konfigurera intranätanslutning för den virtuella gästdatorn

Instruktioner om hur du aktiverar transparent anslutning mellan virtuella gästdpm och virtuella Azure-datorer finns i [det här dokumentet](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
