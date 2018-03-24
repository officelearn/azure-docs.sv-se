---
title: Begränsa nätverksåtkomst till PaaS - resurser i Azure-portalen | Microsoft Docs
description: Lär dig mer om att begränsa och begränsa nätverksåtkomst till Azure-resurser, till exempel Azure Storage och Azure SQL Database med virtuella nätverksslutpunkter med Azure-portalen.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 9a64a5c1f63dc05cba6fdfa310b694e34bdba7d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Begränsa nätverksåtkomst till PaaS-resurser med virtuella nätverksslutpunkter med hjälp av Azure portal

Slutpunkter för virtuellt nätverk kan du begränsa nätverksåtkomsten till resurser i Azure-tjänsten som ett undernät för virtuellt nätverk. Du kan också ta bort internet-åtkomst till resurserna. Tjänsteslutpunkter ger direkt anslutning från det virtuella nätverket till stöds Azure-tjänster, så att du kan använda privata adressutrymmet för det virtuella nätverket för att komma åt Azure-tjänster. Trafik till Azure-resurser via Tjänsteslutpunkter alltid kvar på stamnät Microsoft Azure-nätverk. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk med ett undernät
> * Lägg till ett undernät och aktivera en tjänstslutpunkt
> * Skapa en Azure-resurs och ge åtkomst till nätverket till den från bara ett undernät
> * Distribuera en virtuell dator (VM) till varje undernät
> * Bekräfta åtkomst till en resurs från ett undernät
> * Bekräfta att åtkomst nekas till en resurs från ett undernät och internet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-portalen på http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. Anger, eller Välj följande information och välj sedan **skapa**:

    |Inställning|Värde|
    |----|----|
    |Namn| myVirtualNetwork |
    |Adressutrymme| 10.0.0.0/16|
    |Prenumeration| Välj din prenumeration|
    |Resursgrupp | Välj **Skapa nytt** och ange *myResourceGroup*.|
    |Plats| Välj **östra USA** |
    |Undernätsnamn| Offentligt|
    |Adressintervall för gatewayundernät| 10.0.0.0/24|
    |slutpunkter| Disabled|

    ![Ange grundläggande information om ditt virtuella nätverk](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)


## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt

1. I den **söka efter resurser, tjänster och dokumenten** överst i portalen och ange *myVirtualNetwork.* När **myVirtualNetwork** visas i sökresultaten väljer den.
2. Lägg till ett undernät i det virtuella nätverket. Under **inställningar**väljer **undernät**, och välj sedan **+ undernät**som visas i följande bild:

    ![Lägga till undernät](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Under **Lägg till undernät**, Välj eller ange följande information och välj sedan **OK**:

    |Inställning|Värde|
    |----|----|
    |Namn| Privat |
    |Adressintervall| 10.0.1.0/24|
    |slutpunkter| Välj **Microsoft.Storage** under **tjänster**|

## <a name="restrict-network-access-to-and-from-a-subnet"></a>Begränsa nätverksåtkomst till och från ett undernät

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **nätverkssäkerhetsgruppen**.
Under **skapar en nätverkssäkerhetsgrupp**anger, eller Välj följande information och välj sedan **skapa**:

    |Inställning|Värde|
    |----|----|
    |Namn| myNsgPrivate |
    |Prenumeration| Välj din prenumeration|
    |Resursgrupp | Välj **använda befintliga** och välj *myResourceGroup*.|
    |Plats| Välj **östra USA** |

4. När nätverkssäkerhetsgruppen har skapats kan du ange *myNsgPrivate*i den **söka efter resurser, tjänster och dokumenten** överst i portalen. När **myNsgPrivate** visas i sökresultaten väljer den.
5. Under **inställningar**väljer **utgående säkerhetsregler**.
6. Välj **+ Lägg till**.
7. Skapa en regel som tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats till tjänsten Azure Storage. Anger, eller Välj följande information och välj sedan **OK**:

    |Inställning|Värde|
    |----|----|
    |Källa| Välj **VirtualNetwork** |
    |Portintervall för källa| * |
    |Mål | Välj **tjänsten tagg**|
    |Mål-tjänsten-tagg | Välj **lagring**|
    |Portintervall för mål| * |
    |Protokoll|Alla|
    |Åtgärd|Tillåt|
    |Prioritet|100|
    |Namn|Allow-Storage-All|
8. Skapa en regel som åsidosätter en standardsäkerhetsregel som tillåter utgående åtkomst till offentliga IP-adresser. Slutför steg 6 och 7 igen med hjälp av följande värden:

    |Inställning|Värde|
    |----|----|
    |Källa| Välj **VirtualNetwork** |
    |Portintervall för källa| * |
    |Mål | Välj **tjänsten tagg**|
    |Mål-tjänsten-tagg| Välj **Internet**|
    |Portintervall för mål| * |
    |Protokoll|Alla|
    |Åtgärd|Neka|
    |Prioritet|110|
    |Namn|Deny-Internet-All|

9. Under **inställningar**väljer **inkommande säkerhetsregler**.
10. Välj **+ Lägg till**.
11. Skapa en regel som tillåter trafik för Remote Desktop Protocol (RDP) till undernätet för inkommande från var som helst. Regeln åsidosätter en standardsäkerhetsregel som nekar all inkommande trafik från internet. Anslutning till fjärrskrivbord är tillåtna till undernätet så att anslutningen kan testas i ett senare steg. Slutför steg 6 och 7 igen med hjälp av följande värden:

    |Inställning|Värde|
    |----|----|
    |Källa| Alla |
    |Portintervall för källa| * |
    |Mål | Välj **tjänsten tagg**|
    |Mål-tjänsten-tagg| Välj **VirtualNetwork**|
    |Portintervall för mål| 3389 |
    |Protokoll|Alla|
    |Åtgärd|Tillåt|
    |Prioritet|120|
    |Namn|Allow-RDP-All|

12. Under **inställningar**väljer **undernät**.
13. Välj **+ associera**
14. Under **Associera undernät**väljer **för virtuella nätverk** och välj sedan **myVirtualNetwork** under **Välj ett virtuellt nätverk**.
15. Under **Välj undernät**väljer **privata**, och välj sedan **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Begränsa nätverksåtkomsten till en resurs

Stegen för att begränsa nätverksåtkomsten till resurser som skapats via Azure-tjänster aktiverad för slutpunkter varierar tjänster. Finns i dokumentationen för enskilda tjänster för specifika steg för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverksåtkomsten för ett Azure Storage-konto, som exempel.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **lagring**, och välj sedan **lagringskonto - blob, fil, tabell, kö**.
3. Anger, eller Välj följande information återstående standardvärdena och välj sedan **skapa**:

    |Inställning|Värde|
    |----|----|
    |Namn| Ange ett namn som är unikt för alla Azure platser mellan 3 till 24 tecken, med endast siffror och gemener.|
    |Typ av konto|StorageV2 (generella v2)|
    |Replikering| Lokalt redundant lagring (LRS)|
    |Prenumeration| Välj din prenumeration|
    |Resursgrupp | Välj **använda befintliga** och välj *myResourceGroup*.|
    |Plats| Välj **östra USA** |

### <a name="create-a-file-share-in-the-storage-account"></a>Skapa en filresurs i storage-konto

1. När lagringskontot har skapats kan du ange namnet på storage-konto i den **söka efter resurser, tjänster och dokumenten** rutan överst i portalen. När namnet på ditt lagringskonto visas i sökresultaten väljer du den.
2. Välj **filer**som visas i följande bild:

    ![Lagringskonto](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 
3. Välj **+ filresurs**under **Filtjänst**.
4. Ange *Mina filresurs* under **namn**, och välj sedan **OK**.
5. Stäng den **Filtjänst** rutan.

### <a name="enable-network-access-from-a-subnet"></a>Aktivera nätverksåtkomst från ett undernät

Storage-konton godkänna anslutningar från klienter i ett nätverk som standard. Om du vill tillåta åtkomst från endast ett specifikt undernät, och neka åtkomst till nätverket från andra nätverk, gör du följande:

1. Under **inställningar** lagringskontot, Välj **brandväggar och virtuella nätverk**.
2. Under **virtuella nätverken**väljer **markerat nätverk**.
3. Välj **Lägg till befintligt virtuellt nätverk**.
4. Under **Lägg till nätverk**, väljer du följande värden och välj sedan **Lägg till**:

    |Inställning|Värde|
    |----|----|
    |Prenumeration| Välj din prenumeration.|
    |Virtuella nätverk|Välj **myVirtualNetwork**under **virtuella nätverk**|
    |Undernät| Välj **privata**under **undernät**|

    ![Brandväggar och virtuella nätverk](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png) 

5. Välj **Spara**.
6. Stäng den **brandväggar och virtuella nätverk** rutan.
7. Under **inställningar** lagringskontot, Välj **åtkomstnycklar**, enligt följande bild:

      ![Brandväggar och virtuella nätverk](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Observera den **nyckeln** värdet som du måste ange den manuellt i ett senare steg när du kartlägger filresursen till en enhetsbeteckning på en virtuell dator.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Distribuera en virtuell dator till varje undernät för att testa nätverksåtkomst till ett lagringskonto.

### <a name="create-the-first-virtual-machine"></a>Skapa den första virtuella datorn

1. Välj **+ skapa en resurs för** hittades i det övre, vänstra hörnet i Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**.
3. Anger, eller Välj följande information och välj sedan **OK**:

    |Inställning|Värde|
    |----|----|
    |Namn| myVmPublic|
    |Användarnamn|Ange ett användarnamn som du väljer.|
    |Lösenord| Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **använda befintliga** och välj **myResourceGroup**.|
    |Plats| Välj **östra USA**.|

    ![Ange grundläggande information om en virtuell dator](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Välj en storlek för den virtuella datorn och välj sedan **Välj**.
5. Under **inställningar**väljer **nätverk** och välj sedan **myVirtualNetwork**. Välj sedan **undernät**, och välj **offentliga**som visas i följande bild:

    ![Välj ett virtuellt nätverk](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
6. På den **sammanfattning** väljer **skapa** att starta distributionen av virtuella datorer. Den virtuella datorn tar några minuter att distribuera, men du kan fortsätta till nästa steg medan den virtuella datorn skapas.

### <a name="create-the-second-virtual-machine"></a>Skapa den andra virtuella datorn

Slutför steg 1 – 6 igen, men i steg 3, namnge den virtuella datorn *myVmPrivate* och i steg 5, Välj den **privata** undernät.

Den virtuella datorn tar några minuter att distribuera. Fortsätt inte till nästa steg förrän den är klar att skapa och dess inställningar öppna i portalen.

## <a name="confirm-access-to-storage-account"></a>Bekräfta åtkomst till lagringskontot

1. En gång i *myVmPrivate* VM har skapat, Azure öppnar inställningarna för den. Anslut till den virtuella datorn genom att välja den **Anslut** knappen som visas i följande bild:

    ![Ansluta till en virtuell dator](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. När du har valt den **Anslut** knapp och en Remote Desktop Protocol (RDP)-fil skapas och hämtas till datorn.  
3. Öppna hämtade rdp-filen. Välj **Anslut**. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn. Du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn. 
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om varningen, väljer **Ja** eller **Fortsätt**, för att fortsätta med anslutningen.
6. På den *myVmPrivate* VM, mappa Azure-filresursen enhet Z med hjälp av PowerShell. Innan du kör kommandona som följer ersätta `<storage-account-key>` och `<storage-account-name>` med värden som du angett och hämtas i [skapa ett lagringskonto](#create-a-storage-account).

    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
    ```
    
    PowerShell returnerar utdata som liknar följande exempel utdata:

    ```powershell
    Name           Used (GB)     Free (GB) Provider      Root
    ----           ---------     --------- --------      ----
    Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
    ```

    Azure-filresursen som mappats till Z-enheten.

7. Bekräfta att den virtuella datorn har ingen utgående anslutning till alla andra offentliga IP-adresser från en kommandotolk:

    ```
    ping bing.com
    ```
    
    Du får inga svar eftersom nätverkssäkerhetsgruppen som är kopplat till den *privata* undernät inte tillåter utgående åtkomst till den offentliga IP-adresser än de adresser som tilldelats till tjänsten Azure Storage.

8. Stäng fjärrskrivbordssession till den *myVmPrivate* VM.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bekräfta att åtkomst nekas till storage-konto

1. Ange *myVmPublic* i den **söka efter resurser, tjänster och dokumenten** överst i portalen.
2. När **myVmPublic** visas i sökresultaten väljer den.
3. Slutför steg 1 – 6 i [Bekräfta åtkomst till lagringskontot](#confirm-access-to-storage-account) för den *myVmPublic* VM.

    Åtkomst nekas och du får en `New-PSDrive : Access is denied` fel. Åtkomst nekas eftersom den *myVmPublic* VM distribueras i det *offentliga* undernät. Den *offentliga* undernätet har inte en tjänstslutpunkt aktiverat för Azure Storage och lagringskontot kan bara åtkomst till nätverket från den *privata* undernät, inte den *offentliga*undernät.

4. Stäng fjärrskrivbordssession till den *myVmPublic* VM.

5. Bläddra till Azure från datorn, [portal](https://portal.azure.com).
6. Ange namnet på lagringskontot som du skapade i den **söka efter resurser, tjänster och dokumenten** rutan. När namnet på ditt lagringskonto visas i sökresultaten väljer du den.
7. Välj **Filer**.
8. Du får felmeddelandet visas i följande bild:

    ![Meddelande om nekad åtkomst](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

    Åtkomst nekas eftersom datorn inte är i den *privata* undernätet för den *MyVirtualNetwork* virtuellt nätverk.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller:

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

Du har aktiverat en tjänstslutpunkt för ett undernät för virtuellt nätverk i den här självstudiekursen. Du har lärt dig att slutpunkter kan aktiveras för resurser som har distribuerats med Azure-tjänster. Du har skapat ett Azure Storage-konto och begränsad nätverksåtkomst till lagringskontot endast resurser inom ett undernät för virtuellt nätverk. Innan du skapar slutpunkter i produktion virtuella nätverk, rekommenderas att du noggrant bekanta dig med [tjänstens slutpunkter](virtual-network-service-endpoints-overview.md).

Om du har flera virtuella nätverk i ditt konto kan du vill ansluta två virtuella nätverk tillsammans så resurser inom varje virtuellt nätverk kan kommunicera med varandra. Gå vidare till nästa kurs att lära dig att ansluta virtuella nätverk.

> [!div class="nextstepaction"]
> [Ansluta virtuella nätverk](./tutorial-connect-virtual-networks-portal.md)
