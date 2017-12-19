---
title: "Felsöka Nätverkssäkerhetsgrupper - Portal | Microsoft Docs"
description: "Lär dig hur du felsöker Nätverkssäkerhetsgrupper i Azure Resource Manager-distributionsmodellen med hjälp av Azure-portalen."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 92816a5b1a74be5b64d974ad9ade47374db5db10
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Felsöka Nätverkssäkerhetsgrupper i Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Om du konfigurerade Nätverkssäkerhetsgrupper (NSG: er) på den virtuella datorn (VM) och har problem med anslutningen VM, innehåller den här artikeln en översikt över diagnostikfunktionerna för NSG: er för att fortsätta felsökningen.

NSG: er kan du styra vilka typer av trafik som flöde till och från virtuella datorer (VM). NSG: er kan tillämpas på undernät i ett Azure Virtual Network (VNet), nätverksgränssnitt (NIC) eller båda. Effektiva reglerna som gäller för ett nätverkskort är en sammanställning av regler som finns i NSG: er som tillämpas på ett nätverkskort och undernät som den är ansluten till. Regler för dessa NSG: er kan ibland står i konflikt med varandra och påverka nätverksanslutning för en virtuell dator.  

Du kan visa alla effektiva säkerhetsregler från dina NSG: er som tillämpas på den Virtuella datorns nätverkskort. Den här artikeln visar hur du felsöker problem med nätverksanslutningen VM med hjälp av reglerna i Azure Resource Manager-distributionsmodellen. Om du inte är bekant med principerna för VNet och NSG läsa den [för virtuella nätverk](virtual-networks-overview.md) och [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md) översikt artiklar.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Använda effektiva säkerhetsregler för att felsöka VM trafikflöde
Det scenario som följer är ett exempel på ett vanligt anslutningsproblem:

En virtuell dator med namnet *VM1* är en del av ett undernät med namnet *Undernät1* inom ett VNet med namnet *WestUS VNet1*. Ett försök att ansluta till den virtuella datorn med RDP över TCP-port 3389 misslyckas. NSG: er som tillämpas på båda NIC *VM1 NIC1* och undernätet *Undernät1*. Trafik till TCP-port 3389 tillåts i NSG: N som är kopplad till nätverksgränssnittet *VM1 NIC1*, men TCP ping till VM1 är port 3389 misslyckas.

Följande steg kan användas för att fastställa inkommande och utgående anslutningsfel över alla portar när det här exemplet används TCP-port 3389.

### <a name="vm"></a>Visa effektiva säkerhetsregler för en virtuell dator
Utför följande steg för att felsöka NSG: er för en virtuell dator:

Du kan visa en fullständig lista över giltiga säkerhetsregler på ett nätverkskort från Virtuellt datorn. Du kan också lägga till, ändra och ta bort både nätverkskort och undernät NSG-regler från bladet effektiva regler om du har behörighet att utföra dessa åtgärder.

1. Logga in på Azure-portalen på https://portal.azure.com med ett Azure-konto. Ditt konto ha tilldelats den *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* åtgärden för nätverksgränssnittet. Information om hur du tilldelar åtgärder till konton finns [skapa anpassade roller för rollbaserad åtkomstkontroll i](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Klicka på **fler tjänster**, klicka på **virtuella datorer** som visas i listan.
3. Välj en virtuell dator för att felsöka i listan som visas och en VM-bladet med alternativ visas.
4. Klicka på **diagnostisera & lösa problem** och välj sedan ett vanligt problem. I det här exemplet **jag kan inte ansluta till en virtuell dator Windows** är markerad. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. Stegen visas under problem, som visas i följande bild: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Klicka på *effektiva regler för nätverkssäkerhetsgrupper* i listan över rekommenderade steg.
6. Den **hämta effektiva säkerhetsregler** bladet visas som i följande bild:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Observera följande delar av bilden:
   
   * **Omfattning:** inställd på *VM1*, den virtuella datorn valde i steg 3.
   * **Nätverksgränssnitt:** *VM1 NIC1* är markerad. En virtuell dator kan ha flera nätverksgränssnitt (NIC). Varje nätverkskort kan ha unika effektiva säkerhetsregler. När du felsöker kan behöva du visa effektiva säkerhetsregler för varje nätverkskort.
   * **Associerade NSG: er:** NSG: er kan tillämpas på både NIC och nätverkskortet är anslutet till undernätet. I bilden är har en NSG tillämpats både nätverkskortet och den är ansluten till undernätet. Du kan klicka på NSG namn att direkt ändra regler i de NSG: er.
   * **Fliken VM1 nsg:** listan över regler som visas i bilden är för NSG tillämpas på nätverkskortet. Flera standardregler skapas av Azure när en NSG skapas. Du kan inte ta bort standardreglerna, men du kan åsidosätta dem med regler med högre prioritet. Mer information om standardregler den [NSG översikt](virtual-networks-nsg.md#default-rules) artikel.
   * **Målkolumnen:** några av reglerna som har texten i kolumnen, medan andra har adressprefix. Texten är namnet på standardtaggar som tillämpas på säkerhetsregeln när den skapades. Taggarna är systemdefinierade identifierare som representerar flera prefix. Att markera en regel med en tagg som *AllowInternetOutBound*, visar en lista över prefix i den **-adressprefix** bladet.
   * **Hämta:** listan över regler som kan vara lång. Du kan hämta en CSV-fil av reglerna för offlineanalys genom att klicka på **hämta** och spara filen.
   * **AllowRDP** inkommande regel: den här regeln kan RDP-anslutningar till den virtuella datorn.
7. Klicka på den **Undernät1 NSG** att visa effektiva regler från NSG tillämpad på undernätet, enligt följande bild: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Observera den *denyRDP* **inkommande** regeln. Regler för inkommande trafik tillämpas på undernätet utvärderas före regler som används vid nätverksgränssnittet. Eftersom neka-regeln har tillämpats på undernätet misslyckas begäran om att ansluta till TCP-3389 eftersom utvärderas aldrig Tillåt-regel på nätverkskortet. 
   
    Den *denyRDP* regeln är anledningen till varför RDP-anslutning inte. Ta bort den bör lösa problemet.
   
   > [!NOTE]
   > Om den virtuella datorn som är kopplade till nätverkskortet inte är i körningstillstånd eller NSG: er som inte har använts till nätverkskort eller undernät, visas inga regler.
   > 
   > 
8. Redigera NSG-regler, klicka på *Undernät1 NSG* i den **associerade NSG: er** avsnitt.
   Då öppnas den **Undernät1 NSG** bladet. Du kan redigera regler direkt genom att klicka på **inkommande säkerhetsregler**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. När du tar bort den *denyRDP* regel för inkommande trafik i den **Undernät1 NSG** och lägga till en *allowRDP* regeln för faktiska regler lista ser ut som på bilden nedan:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Kontrollera att TCP-port 3389 är öppen genom att öppna en RDP-anslutning till den virtuella datorn eller med hjälp av verktyget PsPing. Du kan lära dig mer om PsPing genom att läsa den [PsPing hämtningssidan](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Visa effektiva säkerhetsregler för ett nätverksgränssnitt
Om din VM trafikflöde påverkas för ett specifikt nätverkskort visa du en fullständig lista över gällande regler till nätverkskortet i nätverket gränssnitt kontexten genom att utföra följande steg:

1. Logga in på Azure-portalen på https://portal.azure.com.
2. Klicka på **fler tjänster**, klicka på **nätverksgränssnitt** som visas i listan.
3. Välj ett nätverksgränssnitt. I följande bild, ett nätverkskort med namnet *VM1 NIC1* är markerad.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Observera att den **omfång** har angetts till nätverksgränssnittet som valts. Mer information om ytterligare information visas läsa steg 6 i den **felsöka NSG: er för en virtuell** i den här artikeln.
   
   > [!NOTE]
   > Om en NSG tas bort från ett nätverksgränssnitt undernätet NSG är fortfarande giltiga på det angivna nätverkskortet. I så fall skulle utdata endast visa regler från undernätet NSG. Reglerna visas bara om nätverkskortet som är kopplad till en virtuell dator.
   > 
   > 
4. Du kan redigera regler direkt för NSG: er som är associerad med ett nätverkskort och ett undernät. Mer information hur, läsa steg 8 i den **visa effektiva säkerhetsregler för en virtuell dator** i den här artikeln.

## <a name="nsg"></a>Visa effektiva säkerhetsregler för nätverkssäkerhetsgruppen (NSG)
När du ändrar NSG-regler kan du vill granska effekten av de regler som läggs till på en viss virtuell dator. Du kan visa en fullständig lista över giltiga säkerhetsregler för alla nätverkskort som en given NSG tillämpas på, utan att växla kontext än det angivna NSG-bladet. Utför följande steg för att felsöka effektiva regler inom en NSG:

1. Logga in på Azure-portalen på https://portal.azure.com.
2. Klicka på **fler tjänster**, klicka på **Nätverkssäkerhetsgrupper** som visas i listan.
3. Välj en NSG. I följande bild, har en NSG som heter VM1 nsg valts.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Observera följande avsnitt i föregående bild:
   
   * **Omfattning:** inställd på NSG: N som valts.
   * **Virtuell dator:** när en NSG som kopplats till ett undernät, tillämpas den på alla nätverksgränssnitt som är kopplade till alla virtuella datorer som är anslutna till undernätet. I listan visas alla virtuella datorer som den här NSG tillämpas på. Du kan välja någon virtuell dator i listan.
     
     > [!NOTE]
     > Om en NSG som kopplats till endast en tom undernät, visas inte virtuella datorer. Om en NSG som kopplats till ett nätverkskort som inte är associerad med en virtuell dator, visas dessa nätverkskort också inte. 
     > 
     > 
   * **Nätverksgränssnitt:** en virtuell dator kan ha flera nätverksgränssnitt. Du kan välja ett nätverksgränssnitt som är kopplade till den valda virtuella datorn.
   * **AssociatedNSGs:** när som helst ett nätverkskort kan ha upp till två effektiva NSG: er, en tillämpas på nätverkskortet och den andra undernät. Även om omfånget är valt som VM1 nsg om nätverkskortet har en effektiv undernät NSG, visar utdata både NSG: er.
4. Du kan redigera regler direkt för NSG: er som är associerad med ett nätverkskort eller ett undernät. Mer information hur, läsa steg 8 i den **visa effektiva säkerhetsregler för en virtuell dator** i den här artikeln.

Mer information om ytterligare information visas läsa steg 6 i den **visa effektiva säkerhetsregler för en virtuell dator** i den här artikeln.

> [!NOTE]
> Även om ett undernät och ett nätverkskort kan ha endast en NSG tillämpas på dem, kan en NSG vara kopplad till flera nätverkskort och flera undernät.
> 
> 

## <a name="considerations"></a>Överväganden
Tänk på följande när du felsöker problem med nätverksanslutningen:

* Standard NSG-regler ska blockera inkommande åtkomst från internet och tillåter bara VNet inkommande trafik. Regler bör läggas uttryckligen vill tillåta inkommande åtkomst från Internet, som krävs.
* Om det finns inga NSG säkerhetsregler som orsakar en virtuell dator är ansluten till nätverket misslyckas, kan det bero på följande:
  * Brandväggsprogram som körs i den Virtuella datorns operativsystem
  * Vägar som konfigurerats för virtuella installationer eller lokal trafik. Internet-trafik kan omdirigeras till lokalt via Tvingad tunneltrafik. En RDP/SSH-anslutning från Internet till den virtuella datorn kanske inte fungerar med den här inställningen, beroende på hur trafiken hanteras i nätverksmaskinvara lokalt. Läs den [felsökning vägar](virtual-network-routes-troubleshoot-powershell.md) artikel för att lära dig att felsöka väg problem som kan hindra flödet av trafik till och från den virtuella datorn. 
* Om du har peerkoppla Vnet, som standard, expanderar taggen VIRTUAL_NETWORK automatiskt för att inkludera prefix för peerkoppla Vnet. Du kan visa dessa prefix i den **ExpandedAddressPrefix** lista, för att felsöka eventuella problem som är relaterade till VNet-peering anslutning. 
* Effektiva säkerhetsregler visas bara om det finns en NSG som är kopplade till Virtuellt datorns nätverkskort och undernät. 
* Om det finns inga NSG: er som är kopplade till nätverkskortet eller undernät och att du har en offentlig IP-adress som tilldelats till den virtuella datorn, kommer alla portar vara öppna för inkommande och utgående åtkomst. Om den virtuella datorn har en offentlig IP-adress, rekommenderas tillämpa NSG: er till nätverkskort eller undernät.

