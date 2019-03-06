---
title: Hur du aktiverar kapslad virtualisering i Azure Virtual Machines | Microsoft Docs
description: Hur du aktiverar kapslad virtualisering i Azure Virtual Machines
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 096c7d54d0d6a166f62456e951b4c739074a8011
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444420"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Hur du aktiverar kapslad virtualisering i en Azure VM

Kapslad virtualisering stöds i flera Azure VM-familjer. Den här funktionen ger stor flexibilitet i stöd för scenarier, till exempel miljöer för utveckling, testning, utbildning och demonstration.   

Den här artikeln visar aktivera Hyper-V på en Azure virtuell dator och konfigurera Internet-anslutning till den virtuella gästdatorn.

## <a name="create-a-nesting-capable-azure-vm"></a>Skapa en kapslade kompatibla Azure VM

Skapa en ny Windows Server 2016 Azure VM.  Snabbreferens stöder alla v3 virtuella datorer kapslad virtualisering. En fullständig lista över virtuella datorer storlekar som stöd för kapsling, kan du ta en titt på [Azure-beräkningsenhet artikeln](acu.md).

Kom ihåg att välja en VM-storlek som är tillräckligt stor för att stödja kraven för en virtuell gästdator. I det här exemplet använder vi en D3_v3-storlek virtuella Azure-datorer. 

Du kan visa regional tillgänglighet för Dv3 och Ev3-serien virtuella datorer [här](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Detaljerade anvisningar om hur du skapar en ny virtuell dator finns i [skapa och hantera Windows virtuella datorer med Azure PowerShell-modulen](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Ansluta till din Azure VM

Skapa en fjärrskrivbordsanslutning till den virtuella datorn.

1. Klicka på knappen **Anslut** på den virtuella datorns egenskaper. En protokollfil för fjärrskrivbord (.rdp-fil) skapas och hämtas.

2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

3. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn och klicka sedan på **Ok**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Aktivera Hyper-V-funktionen på Azure VM
Du kan konfigurera dessa inställningar manuellt eller har vi lagt till ett PowerShell-skript för att automatisera konfigurationen.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Alternativ 1: Använda ett PowerShell-skript för att konfigurera kapslad virtualisering
Det finns ett PowerShell.skript för att aktivera kapslad virtualisering på en Windows Server 2016-värd på [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skriptet kontrollerar kraven och konfigurerar sedan kapslad virtualisering på Azure VM. En omstart av Azure VM är nödvändigt att slutföra konfigurationen. Det här skriptet kan fungera i andra miljöer, men är inte säkert. Kolla in Azure-blogginlägget med en video livedemonstration på kapslad virtualisering som körs på Azure! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Alternativ 2: Konfigurera kapslad virtualisering manuellt

1. Öppna PowerShell som administratör på Azure-VM. 

2. Aktivera Hyper-V-funktion och verktyg.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Det här kommandot startar om den virtuella Azure-datorn. Du kommer att förlora RDP-anslutningen under omstarten.
    
3. När den virtuella Azure-datorn har startats om återansluta till den virtuella datorn med RDP.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Konfigurera anslutning till internet för den virtuella gästdatorn
Skapa ett nytt virtuellt nätverkskort för den virtuella gästdatorn och konfigurera en NAT-Gateway om du vill aktivera Internet-anslutning.

### <a name="create-a-nat-virtual-network-switch"></a>Skapa en virtuell nätverksswitch för NAT

1. Öppna PowerShell som administratör på Azure-VM.
   
2. Skapa en intern växel.

    ```powershell
    New-VMSwitch -Name "InternalNATSwitch" -SwitchType Internal
    ```

3. Visa egenskaperna för växeln och notera ifIndex för det nya kortet.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Anteckna ”ifIndex” för den virtuella växeln som du nyss skapade.
    
4. Skapa en IP-adress för NAT-Gateway.
    
För att konfigurera gatewayen, behöver du lite information om nätverket:    
  * IP-adress – IP-Adressen för NAT Gateway anger IPv4 eller IPv6-adress som används som standardgatewayadressen för virtuella nätverkets undernät. Formuläret är a.b.c.1 (till exempel ”192.168.0.1”). När den sista positionen inte behöver vara.1 det vanligtvis är (baserat på undernätsprefixets längd). Normalt bör du använda ett adressutrymme för RFC 1918 privat nätverk. 
  * PrefixLength - undernätsprefixets längd definierar undernätet storleken (nätmask). Undernätsprefixets längd ska vara ett heltal mellan 0 och 32. 0 skulle mappa hela internet, 32 skulle bara tillåter en mappade IP-adress. Vanliga värden mellan 24 och 12 beroende på hur många IP-adresser måste vara kopplad till NAT-enheten. En gemensam PrefixLength är 24 – det här är en nätmask 255.255.255.0.
  * InterfaceIndex - **ifIndex** är gränssnittsindex för den virtuella växeln som skapades i föregående steg. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Skapa NAT-nätverket

För att konfigurera gatewayen, måste du ange information om Nätverks- och NAT-Gateway:
  * Namn – detta är namnet på NAT-nätverket. 
  * InternalIPInterfaceAddressPrefix - NAT-undernätsprefixet beskriver både NAT Gateway IP-adressprefix ovan samt NAT undernätsprefixets längd ovan. Formuläret blir a.b.c.0/NAT undernät prefixlängden. 

Skapa en ny NAT-nätverket i PowerShell.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Skapa den virtuella gästdatorn

1. Öppna Hyper-V Manager och skapa en ny virtuell dator. Konfigurera den virtuella datorn om du vill använda det nya interna nätverket som du skapade.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Installera ett operativsystem på den virtuella gästdatorn.
    
    >[!NOTE] 
    >
    >Du behöver installationsmediet för ett operativsystem att installera på den virtuella datorn. I det här fallet använder vi Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Tilldela en IP-adress till den virtuella gästdatorn

Du kan tilldela en IP-adress till den virtuella gästdatorn antingen genom att manuellt ange en statisk IP-adress på den virtuella gästdatorn eller konfigurera DHCP på Azure-VM för att dynamiskt tilldela IP-adressen.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Alternativ 1: Konfigurera DHCP för att dynamiskt tilldela en IP-adress till den virtuella gästdatorn
Följ stegen nedan för att konfigurera DHCP på den virtuella värddatorn för dynamisk adressallokering.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Installera DHCP-Server på Azure VM

1. Öppna Serverhanteraren. På instrumentpanelen, klickar du på **Lägg till roller och funktioner**. Lägg till roller och funktioner som guiden visas.
  
2. I guiden klickar du på **nästa** fram till sidan serverroller.
  
3. Klickar du på den **DHCP-Server** markerar du kryssrutan klickar du på **Lägg till funktioner**, och klicka sedan på **nästa** förrän du har slutfört guiden.
  
4. Klicka på **Installera**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurera ett nytt DHCP-scope

1. Öppna DHCP-hanteraren.
  
2. I navigeringsfönstret expanderar du servernamnet, högerklicka på **IPv4**, och klicka på **nytt Scope**. Guiden Nytt Scope visas klickar du på **nästa**.
  
3. Ange ett namn och beskrivning för detta scope och klicka på **nästa**.
  
4. Definiera en IP-adressintervall för DHCP-servern (till exempel 192.168.0.100 till 192.168.0.200).
  
5. Klicka på **nästa** fram till sidan för standard-Gateway. Ange IP-adressen som du skapade tidigare (t.ex, 192.168.0.1) som standard-Gateway och klicka sedan på **Lägg till**.
  
6. Klicka på **nästa** tills guiden har slutförts, lämnar du alla standardvärden klickar **Slutför**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Alternativ 2: Ange en statisk IP-adress manuellt på den virtuella gästdatorn
Om du inte konfigurerade DHCP för att dynamiskt tilldela en IP-adress till den virtuella gästdatorn, följer du stegen nedan för att ange en statisk IP-adress.

1. Öppna PowerShell som administratör på Azure-VM.

2. Högerklicka på den virtuella gästdatorn och klicka på Anslut.

3. Logga in på den virtuella gästdatorn.

4. Öppna nätverks- och delningscenter på den virtuella gästdatorn.

5. Konfigurera nätverkskort för en adress inom intervallet för NAT-nätverket som du skapade i föregående avsnitt.

I det här exemplet ska du använda en adress i intervallet 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testa anslutningen i den virtuella gästdatorn

I den virtuella gästdatorn, öppna webbläsaren och navigera till en webbsida.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Konfigurera intranätanslutning för den virtuella gästdatorn

Anvisningar om hur du aktiverar transparent anslutning mellan virtuella gästdatorer och virtuella Azure-datorer hittar [det här dokumentet](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
