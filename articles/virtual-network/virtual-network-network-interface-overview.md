---
title: "Nätverksgränssnitt i Azure | Microsoft Docs"
description: "Läs mer om Azure-nätverksgränssnitt i Azure Resource Manager-distributionsmodellen."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 3244d5b52785d820698bf26f9bf189de93ef64e4
ms.openlocfilehash: 691b79d7739246dad7191195fa049fd58340c8ff


---
# <a name="network-interfaces-in-azure"></a>Nätverksgränssnitt i Azure
Ett nätverksgränssnitt (NIC) är gränssnittet mellan en virtuell dator (VM) och det underliggande programvarunätverket. Den här artikeln förklarar vad ett nätverksgränssnitt är och hur det används i Azure Resource Manager-distributionsmodellen.

Microsoft rekommenderar att du distribuerar nya resurser med hjälp av Resource Manager-distributionsmodellen, men du kan också distribuera virtuella datorer med nätverksanslutningar i den [klassiska](virtual-network-ip-addresses-overview-classic.md) distributionsmodellen. Om du är bekant med den klassiska modellen finns det viktiga skillnader i VM-nätverksfunktionerna jämfört med Resource Manager-distributionsmodellen. Mer information om skillnaderna finns i artikeln om [virtuella nätverksfunktioner i den klassiska distributionsmodellen](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

I Azure har ett nätverksgränssnitt följande egenskaper:

1. Ett nätverksgränssnitt är en resurs som kan skapas och tas bort och som har sina egna konfigurerbara inställningar.
2. Det måste anslutas till ett undernät i ett virtuellt Azure-nätverk (VNet) när det skapas. Om du inte är bekant med virtuella nätverk finns mer information om dem i artikeln [Virtual network overview](virtual-networks-overview.md) (Översikt över virtuella nätverk). Nätverkskortet måste anslutas till ett VNet som finns på samma Azure-[plats](https://azure.microsoft.com/regions) och i samma Azure-[prenumeration](../azure-glossary-cloud-terminology.md#subscription) som nätverkskortet. När nätverkskortet har skapats kan du ändra undernätet som det är anslutet till, men du kan inte ändra det virtuella nätverket som det är anslutet till.
3. Tilldelas ett namn som inte kan ändras efter att nätverkskortet har skapats. Namnet måste vara unikt inom en Azure-[resursgrupp](../azure-resource-manager/resource-group-overview.md#resource-groups), men måste inte vara unikt inom prenumerationen, den Azure-plats som det skapas i eller det virtuella nätverk som det är anslutet till. Flera nätverkskort skapas vanligtvis i en Azure-prenumeration. Vi rekommenderar att du följer en namngivningskonvention i stället för att använda standardnamn, så att det blir enklare att hantera flera nätverkskort. Du hittar förslag i artikeln [Rekommenderade namnkonventioner för Azure-resurser](../guidance/guidance-naming-conventions.md).
4. Kan anslutas till en virtuell dator, men kan endast kopplas till en enda virtuell dator som finns på samma plats som nätverkskortet.
5. Har en MAC-adress, som bevaras med nätverkskortet så länge det är anslutet till en virtuell dator. MAC-adressen bevaras även om den virtuella datorn startas om (inifrån operativsystemet) eller stoppas (frigörs) och startas med hjälp av Azure Portal, Azure PowerShell eller Azure-kommandoradsgränssnittet. Om det kopplas bort från en virtuell dator och ansluts till en annan virtuell dator får nätverkskortet en annan MAC-adress. Om nätverkskortet tas bort tilldelas MAC-adressen till andra nätverkskort.
6. Måste tilldelas en primär **privat** statisk eller dynamisk *IPv4*-adress.
7. Kan associeras med en offentlig IP-adressresurs.
8. Har stöd för nätverksaccelerering med SR-IOV (Single-Root I/O Virtualization) för specifika VM-storlekar som kör specifika versioner av Microsoft Windows Server-operativsystemet. Mer information om den här förhandsfunktionen finns i artikeln [Accelerated networking for a virtual machine](virtual-network-accelerated-networking-powershell.md) (Nätverksaccelerering för en virtuell dator).
9. Kan ta emot trafik som inte är avsedda för privata IP-adresser som det tilldelats om IP-vidarebefordring är aktiverat för nätverkskortet. Om en virtuell dator till exempel kör brandväggsprogramvara dirigerar det paket som inte är avsedda för dess egna IP-adresser. Den virtuella datorn måste fortfarande köra programvara som kan dirigera eller vidarebefordra trafik, men för att göra det måste IP-vidarebefordring vara aktiverat för ett nätverkskort.
10. Skapas ofta i samma resursgrupp som den virtuella datorn som det är anslutet till eller i samma virtuella nätverk som det är anslutet till, även om det inte är ett krav.

## <a name="vms-with-multiple-network-interfaces"></a>En virtuell dator med flera nätverksgränssnitt
Flera nätverkskort kan anslutas till en virtuell dator, men när du gör det är det viktigt att du tänker på följande:  

* Den virtuella datorns storlek måste ha stöd för flera nätverkskort. Mer information om vilka VM-storlekar som stöder flera nätverkskort finns i artiklarna om [VM-storlekar för Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) och [VM-storlekar för Linux](../virtual-machines/virtual-machines-linux-sizes.md).
* Den virtuella datorn måste skapas med minst två nätverkskort. Om den virtuella datorn skapas med bara ett nätverkskort, även om VM-storleken stöder flera nätverkskort, kan du inte ansluta fler nätverkskort till den virtuella datorn efter att den har skapats. Om den virtuella datorn skapades med minst två nätverkskort kan du ansluta fler nätverkskort till den virtuella datorn när den har skapats, förutsatt att VM-storleken stöder fler än två nätverkskort.  
* Du kan koppla bort sekundära nätverkskort (men inte det primära nätverkskortet) från en virtuell dator om den virtuella datorn har minst tre anslutna nätverkskort. Du kan inte koppla bort nätverkskort om den virtuella datorn har två eller färre anslutna nätverkskort.  
* Ordningen på nätverkskorten i den virtuella datorn är slumpmässig och kan även ändras i samband med Azure-infrastrukturuppdateringar. IP-adresserna och motsvarande Ethernet-MAC-adresser förblir dock desamma. Anta exempelvis att operativsystemet identifierar Azure NIC1 som Eth1. Eth1 har IP-adressen 10.1.0.100 och MAC-adressen 00-0D-3A-B0-39-0D. Efter en Azure-infrastrukturuppdatering och omstart kan operativsystemet identifiera Azure NIC1 som Eth2, men IP- och MAC-adresserna är samma som när operativsystemet identifierade Azure NIC1 som Eth1. Om en omstart initieras av kunden är nätverkskortens ordning samma i operativsystemet.  
* Om den virtuella datorn är medlem i en [tillgänglighetsuppsättning](../azure-glossary-cloud-terminology.md#availability-set) måste alla virtuella datorer i tillgänglighetsuppsättningen ha antingen ett enda nätverkskort eller flera nätverkskort. Om de virtuella datorerna har flera nätverkskort måste inte alla ha samma antal, förutsatt att varje virtuell dator har minst två nätverkskort.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du skapar en virtuell dator med ett enda nätverkskort finns i artikeln [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
* Mer information om hur du skapar en virtuell dator med flera nätverkskort finns i artikeln [Distribuera en virtuell dator med flera nätverkskort](virtual-network-deploy-multinic-arm-ps.md).
* Mer information om hur du skapar ett nätverkskort med flera IP-konfigurationer finns i artikeln [Flera IP-adresser för virtuella datorer i Azure](virtual-network-multiple-ip-addresses-powershell.md).




<!--HONumber=Jan17_HO5-->


