---
title: Filtrera nätverkstrafik – självstudie – Azure-portalen | Microsoft Docs
description: I den här självstudien får du lära dig hur du filtrerar nätverkstrafik till ett undernät, med en nätverkssäkerhetsgrupp, med hjälp av Azure-portalen.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 06/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: a731c1e0617fe0ccf9d571dd2b7d0c2ad107bc9e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901406"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Självstudie: Filtrera nätverkstrafik med en nätverkssäkerhetsgrupp med hjälp av Azure-portalen

Du kan filtrera inkommande och utgående nätverkstrafik till och från ett undernät i ett virtuellt nätverk med en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehåller säkerhetsregler som filtrerar nätverkstrafik efter IP-adress, port och protokoll. Säkerhetsregler tillämpas på resurser som har distribuerats i ett undernät. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en nätverkssäkerhetsgrupp och säkerhetsregler
> * Skapa ett virtuellt nätverk och associera en nätverkssäkerhetsgrupp med ett undernät
> * Distribuera virtuella datorer (VM) i ett undernät
> * Testa trafikfilter

Om du vill kan du slutföra den här självstudien med [Azure CLI](tutorial-filter-network-traffic-cli.md) eller [PowerShell](tutorial-filter-network-traffic-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                    | myVirtualNetwork                                   |
    | Adressutrymme           | 10.0.0.0/16                                        |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **Skapa ny** och ange *myResourceGroup*. |
    | Plats                | Välj **USA, östra**.                                |
    | Undernät – namn            | mySubnet                                           |
    | Undernät – adressintervall  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Skapa programsäkerhetsgrupper

En programsäkerhetsgrupp gör att du kan gruppera ihop servrar med liknande funktioner, till exempel webbservrar.

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. I rutan **Sök på Marketplace** anger du *Programsäkerhetsgrupp*. När **programsäkerhetsgruppen** visas i sökresultatet väljer du den. Välj sedan **Programsäkerhetsgrupp** igen under **Allt** och välj sedan **Skapa**.
3. Välj eller ange följande information och välj **Skapa**:

    | Inställning        | Värde                                                         |
    | ---            | ---                                                           |
    | Namn           | myAsgWebServers                                               |
    | Prenumeration   | Välj din prenumeration.                                     |
    | Resursgrupp | Välj **Använd befintlig** och sedan **myResourceGroup**. |
    | Plats       | Östra USA                                                       |

4. Slutför steg 3 igen och ange följande värden:

    | Inställning        | Värde                                                         |
    | ---            | ---                                                           |
    | Namn           | myAsgMgmtServers                                              |
    | Prenumeration   | Välj din prenumeration.                                     |
    | Resursgrupp | Välj **Använd befintlig** och sedan **myResourceGroup**. |
    | Plats       | Östra USA                                                       |

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Nätverk** och sedan **Nätverkssäkerhetsgrupp**.
3. Välj eller ange följande information och välj **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myNsg|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp | Välj **Använd befintlig** och sedan *myResourceGroup*.|
    |Plats|Östra USA|

## <a name="associate-network-security-group-to-subnet"></a>Associera nätverkssäkerhetsgrupp till undernät

1. I rutan *Sök efter resurser, tjänster och dokument* högst upp i portalen börjar du skriva *myNsg*. Välj **myNsg** när det visas bland sökresultaten.
2. Under **INSTÄLLNINGAR** väljer du **Undernät** och väljer sedan **+ Associera** som du ser på följande bild:

    ![Associera NSG till undernät](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. Under **Koppla undernätverk** väljer du **Virtuellt nätverk** och sedan **myVirtualNetwork**. Välj **Undernät**, välj **mySubnet** och välj sedan **OK**.

## <a name="create-security-rules"></a>Skapa säkerhetsregler

1. Under **INSTÄLLNINGAR** väljer du **Ingående säkerhetsregler** och sedan **+ Lägg till**, så som visas i följande bild:

    ![Lägga till en ingående säkerhetsregel](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Skapa en säkerhetsregel som tillåter portarna 80 och 443 till programsäkerhetsgruppen **myAsgWebServers**. Under **Lägg till ingående säkerhetsregel** anger eller väljer du följande värden, accepterar standardvärdena för resten av inställningarna och väljer sedan **Lägg till**:

    | Inställning                 | Värde                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Mål             | Välj **Programsäkerhetsgrupp** och välj sedan **myAsgWebServers** för **Programsäkerhetsgrupp**.  |
    | Målportintervall | Ange 80,443                                                                                                    |
    | Protokoll                | Välj TCP                                                                                                      |
    | Namn                    | Allow-Web-All                                                                                                   |

3. Slutför steg 2 igen med följande värden:

    | Inställning                 | Värde                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Mål             | Välj **Programsäkerhetsgrupp** och välj sedan **myAsgMgmtServers** för **Programsäkerhetsgrupp**. |
    | Målportintervall | Ange 3389                                                                                                      |
    | Protokoll                | Välj TCP                                                                                                      |
    | Prioritet                | Ange 110                                                                                                       |
    | Namn                    | Allow-RDP-All                                                                                                   |

    I den här självstudien exponeras RDP (port 3389) till internet för den virtuella dator som är tilldelad till programsäkerhetsgruppen *myAsgMgmtServers*. För produktionsmiljöer rekommenderas att du i stället för att exponera port 3389 mot Internet ansluter till Azure-resurser som du vill hantera med hjälp av en VPN-anslutning eller privat nätverksanslutning.

När du har slutfört steg 1–3 granskar du de regler som du skapat. Din lista bör se ut som listan i följande bild:

![Säkerhetsregler](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVmWeb|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Använd befintlig** och sedan **myResourceGroup**.|
    |Plats| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Under **Inställningar** anger eller väljer du följande värden, accepterar standardvärdena för resten av inställningarna och väljer sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk |Välj **myVirtualNetwork**|
    |Nätverkssäkerhetsgrupp | Välj **Avancerat**.|
    |Nätverkssäkerhetsgrupp (brandvägg)| Välj **(ny) myVmWeb-nsg** och sedan under **Välj nätverkssäkerhetsgrupp** väljer du **Ingen**. |

6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Slutför steg 1–6 igen, men i steg 3 ger du den virtuella datorn namnet *myVmMgmt*. Det tar några minuter att distribuera den virtuella datorn. Fortsätt inte till nästa steg förrän den virtuella datorn har distribuerats.

## <a name="associate-network-interfaces-to-an-asg"></a>Koppla nätverksgränssnitt till en ASG

När portalen skapade de virtuella datorerna skapade den ett nätverksgränssnitt för varje virtuell dator och anslöt nätverksgränssnittet till den virtuella datorn. Lägg till nätverksgränssnittet för varje virtuell dator till en av de programsäkerhetsgrupper som du skapade tidigare:

1. I rutan *Sök efter resurser, tjänster och dokument* högst upp i portalen börjar du skriva *myVmWeb*. När den virtuella datorn **myVmWeb** visas i sökrutan väljer du det.
2. Under **INSTÄLLNINGAR** väljer du **Nätverk**.  Välj **Konfigurera programsäkerhetsgrupperna**, välj **myAsgWebServers** för **Programsäkerhetsgrupper** och välj sedan **Spara** enligt följande bild:

    ![Associera till ASG](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Slutför steg 1 och 2 igen. Den här gången söker du efter den virtuella datorn **myVmMgmt** och väljer ASG:n **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Testa trafikfilter

1. Anslut till den virtuella datorn *myVmMgmt*. Ange *myVmMgmt* i sökrutan längst upp i portalen. Välj **myVmMgmt** när det visas bland sökresultaten. Välj knappen **Anslut**.
2. Välj **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen och välj **Anslut**. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

    Anslutningen lyckas, eftersom port 3389 tillåts inkommande från Internet till programsäkerhetsgruppen *myAsgMgmtServers* där nätverksgränssnittet som är kopplat till den virtuella datorn *myVmMgmt* ingår.

6. Anslut till den virtuella datorn *myVmWeb* från den virtuella datorn *myVmMgmt* genom att ange följande kommando i en PowerShell-session:

    ``` 
    mstsc /v:myVmWeb
    ```

    Du kan ansluta till den virtuella datorn myVmWeb från den virtuella datorn myVmMgmt eftersom virtuella datorer i samma virtuella nätverk kan kommunicera med varandra via alla portar som standard. Det går dock inte att skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVmWeb* från Internet eftersom säkerhetsregeln för *myAsgWebServers* inte tillåter port 3389 inkommande från Internet och inkommande trafik från internet nekas för alla resurser som standard.

7. För att installera Microsoft IIS på den virtuella datorn *myVmWeb* anger du följande kommando från en PowerShell-session på den virtuella datorn *myVmWeb*:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. När IIS-installationen är klar kopplar du från den virtuella datorn *myVmWeb*, vilket lämnar dig i fjärrskrivbordsanslutningen med den virtuella datorn *myVmMgmt*.
9. Koppla från den virtuella datorn *myVmMgmt*.
10. I rutan *Sök efter resurser, tjänster och dokument* högst upp i Azure-portalen börjar du skriva *myVmWeb* på datorn. Välj **myVmWeb** när det visas bland sökresultaten. Notera den **offentliga IP-adressen** för den virtuella datorn. Den adress som visas i följande bild är 137.135.84.74, men din adress är annorlunda:

    ![Offentlig IP-adress](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Bekräfta att du har åtkomst till webbservern *myVmWeb* via internet genom att öppna en webbläsare på datorn och gå till `http://<public-ip-address-from-previous-step>`. ISS-välkomstskärmen visas eftersom port 80 tillåts inkommande från Internet till programsäkerhetsgruppen *myAsgWebServers* där nätverksgränssnittet som är kopplat till den virtuella datorn *myVmWeb* ingår.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en nätverkssäkerhetsgrupp och associerat den med ett undernät i ett virtuellt nätverk. Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper](security-overview.md) och [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md).

Azure dirigerar som standard trafik mellan undernät. Du kan i stället välja att exempelvis dirigera trafik mellan undernät via en virtuell dator, som fungerar som en brandvägg. Gå vidare till nästa självstudie om du vill veta hur du skapar en routningstabell.

> [!div class="nextstepaction"]
> [Skapa en routningstabell](./tutorial-create-route-table-portal.md)