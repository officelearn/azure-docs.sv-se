---
title: "Så här aktiverar du kapslade virtualisering i Azure Virtual Machines | Microsoft Docs"
description: "Så här aktiverar du kapslade virtualisering i Azure Virtual Machines"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: philmea
manager: timlt
ms.author: philmea
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: c2f511cd024accc099423f2ed5bbb15d2dd23414
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Så här aktiverar du kapslade virtualisering i en Azure VM

Kapslade virtualisering stöds i Dv3 och Ev3 serien av virtuella Azure-datorer. Den här funktionen ger stor flexibilitet i stöd för scenarier, till exempel utveckling, testning, utbildning och demonstration miljöer. 

Den här artikeln innehåller steg genom att aktivera kapslade virtualisering på en virtuell dator i Azure och konfigurera Internet-anslutning till den virtuella gästdatorn.

## <a name="create-a-dv3-or-ev3-series-azure-vm"></a>Skapa en Dv3 eller Ev3 serie Azure VM

Skapa en ny Windows Server 2016 Azure VM och välja en storlek från Dv3 eller Ev3 serier. Se till att du väljer en storlek som är tillräckligt stor för att stödja kraven för en virtuell gästdator. I det här exemplet använder vi en D3_v3 storlek Azure VM. 

Du kan visa regionala tillgängligheten för Dv3 eller Ev3 serie virtuella datorer [här](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Detaljerade instruktioner om hur du skapar en ny virtuell dator finns [skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Ansluta till din Azure VM

Skapa en fjärrskrivbordsanslutning till den virtuella datorn.

1. Klicka på knappen **Anslut** på den virtuella datorns egenskaper. En protokollfil för fjärrskrivbord (.rdp-fil) skapas och hämtas.

2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

3. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn och klicka sedan på **Ok**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Aktivera Hyper-V-funktionen på Azure VM
Du kan konfigurera dessa inställningar manuellt eller så har vi angett ett PowerShell-skript för att automatisera konfigurationen.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Alternativ 1: Använd ett PowerShell-skript för att konfigurera kapslade virtualisering
Det finns ett PowerShell-skript för att aktivera kapslade virtualisering på en Windows Server 2016-värd på [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). Skriptet kontrollerar kraven och konfigurerar sedan kapslade virtualisering på Azure VM. En omstart av Azure VM är nödvändigt för att slutföra konfigurationen. Det här skriptet fungerar i andra miljöer, men är inte säkert. Ta en titt på Azure blogginlägget med en levande videodemonstration kapslade virtualisering som körs på Azure! https://aka.MS/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Alternativ 2: Konfigurera kapslade virtualisering manuellt

1. Öppna PowerShell som administratör på Azure-VM. 

2. Aktivera Hyper-V-funktion och verktyg för hantering.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Detta kommando startar om den virtuella Azure-datorn. Du förlorar din RDP-anslutning under omstarten.
    
3. Återanslut till den virtuella datorn med RDP när den virtuella Azure-datorn har startats om.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Konfigurera internet-anslutning för den virtuella gästdatorn
Skapa ett nytt virtuellt nätverkskort för den virtuella gästdatorn och konfigurera en NAT-Gateway om du vill aktivera Internet-anslutning.

### <a name="create-a-nat-virtual-network-switch"></a>Skapa en virtuell nätverksswitch NAT

1. Öppna PowerShell som administratör på Azure-VM.
   
2. Skapa en intern växel.

    ```powershell
    New-VMSwitch -SwitchName "InternalNATSwitch" -SwitchType Internal
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
    
För att konfigurera gatewayen måste viss information om nätverket:    
  * IP-adress - IP-Adressen för NAT-Gateway anger IPv4 eller IPv6-adress som används som standard gateway-adress för det virtuella nätverket. Formuläret är a.b.c.1 (till exempel ”192.168.0.1”). När den sista positionen behöver inte.1 det vanligtvis (baseras på prefixlängd). Normalt bör du använda en RFC 1918 privat nätverks-adressutrymme. 
  * PrefixLength - undernätsprefixets längd definierar lokala undernätets storlek (nätmask). Undernätsprefixets längd ska vara ett heltal mellan 0 och 32. 0 skulle mappa hela internet, 32 skulle bara tillåter en mappad IP. Vanliga värden mellan 24 och 12 beroende på hur många IP-adresser måste vara kopplad till NAT-enheten. En gemensam PrefixLength är 24 – Detta är en nätmask 255.255.255.0.
  * InterfaceIndex - **ifIndex** är gränssnittsindex för den virtuella växeln som skapades i föregående steg. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Skapa NAT-nätverk

För att konfigurera gatewayen måste du ange information om nätverk och NAT-Gateway:
  * Namn – det här är namnet på NAT-nätverk. 
  * InternalIPInterfaceAddressPrefix - undernätsprefixet NAT beskriver både NAT Gateway IP-adressprefixet ovan samt NAT undernätsprefixets längd från ovan. Formuläret blir a.b.c.0/NAT undernät prefixlängden. 

Skapa ett nytt NAT-nätverk i PowerShell.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Skapa den virtuella gästdatorn

1. Öppna Hyper-V Manager och skapa en ny virtuell dator. Konfigurera den virtuella datorn om du vill använda det nya interna nätverket som du skapade.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Installera ett operativsystem på den virtuella gästdatorn.
    
    >[!NOTE] 
    >
    >Du behöver installationsmediet för ett operativsystem att installera på den virtuella datorn. I det här fallet använder du Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Tilldela en IP-adress till den virtuella gästdatorn

Du kan tilldela en IP-adress till den virtuella gästdatorn antingen genom att manuellt ange en statisk IP-adress på den virtuella gästdatorn eller konfigurera DHCP på Azure-VM för att dynamiskt tilldela IP-adressen.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Alternativ 1: Konfigurera DHCP för att dynamiskt tilldela en IP-adress till den virtuella gästdatorn
Följ stegen nedan för att konfigurera DHCP på den virtuella datorn i värden för dynamisk adressallokering.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Installera DHCP-Server på Azure VM

1. Öppna Serverhanteraren. Klicka på instrumentpanelen, **Lägg till roller och funktioner**. Guiden Lägg till roller och funktioner visas.
  
2. I guiden klickar du på **nästa** förrän sidan serverroller.
  
3. Markerar du den **DHCP-Server** kryssrutan, klicka på **Lägg till funktioner**, och klicka sedan på **nästa** förrän du har slutfört guiden.
  
4. Klicka på **Installera**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurera ett nytt DHCP-scope

1. Öppna DHCP-hanteraren.
  
2. I navigeringsfönstret expanderar du servernamnet, högerklicka på **IPv4**, och klicka på **nytt Scope**. Guiden Nytt Scope visas klickar du på **nästa**.
  
3. Ange ett namn och beskrivning för detta scope och klicka på **nästa**.
  
4. Definiera ett IP-intervall för DHCP-servern (till exempel 192.168.0.100 till 192.168.0.200).
  
5. Klicka på **nästa** förrän sidan standardgateway. Ange IP-adressen som du skapade tidigare (till exempel 192.168.0.1) som standard-Gateway.
  
6. Klicka på **nästa** tills guiden har slutförts, lämnar du alla standardvärden klicka **Slutför**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Alternativ 2: Ange en statisk IP-adress manuellt på den virtuella gästdatorn
Om du inte har konfigurerat DHCP för att dynamiskt tilldela en IP-adress till den virtuella gästdatorn, följer du dessa steg om du vill ange en statisk IP-adress.

1. Öppna PowerShell som administratör på Azure-VM.

2. Högerklicka på den virtuella gästdatorn och klicka på Anslut.

3. Logga in på den virtuella gästdatorn.

4. Öppna nätverks- och delningscenter på virtuella gästdatorn.

5. Konfigurera nätverkskort i en adress inom intervallet för NAT-nätverket som du skapade i föregående avsnitt.

I det här exemplet använder du en adress i intervallet 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testa anslutningen i den virtuella gästdatorn

I den virtuella gästdatorn, öppna webbläsaren och gå till en webbsida.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)
