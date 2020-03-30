---
title: Flera IP-adresser för virtuella Azure-datorer – Portal | Microsoft-dokument
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med Azure-portalen | Resurshanteraren.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: anavin
ms.openlocfilehash: a5bb9bb9c584aef8ac79b3c275d01d3c498da843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060615"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Tilldela flera IP-adresser till virtuella datorer med Azure-portalen

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> I den här artikeln beskrivs hur du skapar en virtuell dator (VM) via Distributionsmodellen för Azure Resource Manager med hjälp av Azure-portalen. Flera IP-adresser kan inte tilldelas resurser som skapats via den klassiska distributionsmodellen. Mer information om Azure-distributionsmodeller finns i artikeln [Förstå distributionsmodeller.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Om du vill skapa en virtuell dator med flera IP-adresser, eller en statisk privat IP-adress, måste du skapa den med PowerShell eller Azure CLI. Om du vill veta mer klickar du på powershell- eller CLI-alternativen högst upp i den här artikeln. Du kan skapa en virtuell dator med en enda dynamisk privat IP-adress och (eventuellt) en enda offentlig IP-adress. Använd portalen genom att följa stegen i artiklarna [Skapa en virtuell dator för Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) eller Skapa en virtuell [Linux-dator.](../virtual-machines/linux/quick-create-portal.md) När du har skapat den virtuella datorn kan du ändra IP-adresstypen från dynamisk till statisk och lägga till ytterligare IP-adresser med hjälp av portalen genom att följa stegen i avsnittet [Lägg till IP-adresser i en virtuell dator](#add) i den här artikeln.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Lägga till IP-adresser till en virtuell dator

Du kan lägga till privata och offentliga IP-adresser i ett Azure-nätverksgränssnitt genom att slutföra stegen som följer. Exemplen i följande avsnitt förutsätter att du redan har en virtuell dator med de tre IP-konfigurationerna som beskrivs i [scenariot](#scenario), men det är inte nödvändigt.

### <a name="core-steps"></a><a name="coreadd"></a>Kärnsteg

1. Bläddra till Azure-portalen och logga in på https://portal.azure.com den om det behövs.
2. Klicka på **Fler tjänster** > skriver *virtuella datorer* i filterrutan i portalen och klickar sedan på Virtuella **datorer**.
3. Klicka på den virtuella dator som du vill lägga till IP-adresser i i fönstret **Virtuella datorer.** Navigera till **fliken Nätverk.** Klicka på **Nätverksgränssnittet** på sidan. Som visas på bilden nedan: 


    ![Lägg till en offentlig IP-adress till en virtuell dator](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. Klicka på **IP-konfigurationerna**i fönstret **Nätverksgränssnitt** .

5. Klicka på **IP-konfigurationer**i fönstret som visas för det nätverkskort som du valde. Klicka på **Lägg till,** slutför stegen i ett av följande avsnitt, baserat på vilken typ av IP-adress du vill lägga till och klicka sedan på **OK**. 

### <a name="add-a-private-ip-address"></a>**Lägga till en privat IP-adress**

Gör så här för att lägga till en ny privat IP-adress:

1. Fyll i stegen i avsnittet [Kärnsteg](#coreadd) i den här artikeln.
2. Klicka på **Lägg till**. Skapa en IP-konfiguration med namnet *IPConfig-4* med *10.0.0.7* som *en statisk* privat IP-adress i fönstret Lägg **till** IP-konfiguration som visas och klicka sedan på **OK**.

    > [!NOTE]
    > När du lägger till en statisk IP-adress måste du ange en oanvänd, giltig adress på undernätet som nätverkskortet är anslutet till. Om adressen du väljer inte är tillgänglig visar portalen ett X för IP-adressen och du måste välja ett annat.

3. När du klickar på OK stängs fönstret och du ser den nya IP-konfigurationen i listan. Stäng fönstret Lägg till **IP-konfiguration** genom att klicka på **OK.**
4. Du kan klicka på **Lägg** till om du vill lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra lägger till IP-adresser.
5. Lägg till de privata IP-adresserna i operativsystemet VM genom att slutföra stegen i avsnittet [Lägg till IP-adresser i ett vm-operativsystem](#os-config) i den här artikeln.

### <a name="add-a-public-ip-address"></a>Lägga till en offentlig IP-adress

En offentlig IP-adress läggs till genom att associera en offentlig IP-adressresurs till antingen en ny IP-konfiguration eller en befintlig IP-konfiguration.

> [!NOTE]
> Offentliga IP-adresser har en nominell avgift. Mer information om IP-adressprissättning finns på [prissidan för IP-adresser.](https://azure.microsoft.com/pricing/details/ip-addresses) Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Skapa en offentlig IP-adressresurs

En offentlig IP-adress är en inställning för en offentlig IP-adressresurs. Om du har en offentlig IP-adressresurs som för närvarande inte är kopplad till en IP-konfiguration som du vill associera till en IP-konfiguration, hoppar du över följande steg och slutför stegen i ett av de avsnitt som följer, som du behöver. Om du inte har en tillgänglig offentlig IP-adressresurs gör du följande steg för att skapa en:

1. Bläddra till Azure-portalen och logga in på https://portal.azure.com den om det behövs.
3. Klicka på **Skapa en offentlig** > IP-adress för**nätverk i** > **portalen.**
4. I fönstret **Skapa offentlig IP-adress** som visas anger du ett **namn,** väljer en **IP-adresstilldelningstyp,** en **prenumeration,** en **resursgrupp**och en **plats**och klickar sedan på **Skapa**, som visas i följande bild:

    ![Skapa en offentlig IP-adressresurs](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Slutför stegen i ett av de avsnitt som följer för att associera den offentliga IP-adressresursen till en IP-konfiguration.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associera den offentliga IP-adressresursen till en ny IP-konfiguration

1. Fyll i stegen i avsnittet [Kärnsteg](#coreadd) i den här artikeln.
2. Klicka på **Lägg till**. Skapa en IP-konfiguration med namnet *IPConfig-4*i fönstret **Lägg till IP-konfiguration** som visas. Aktivera den **offentliga IP-adressen** och välj en befintlig, tillgänglig offentlig IP-adressresurs i fönstret **Välj offentlig IP-adress** som visas.

    När du har valt den offentliga IP-adressresursen klickar du på **OK** och fönstret stängs. Om du inte har en befintlig offentlig IP-adress kan du skapa en genom att slutföra stegen i avsnittet [Skapa en offentlig IP-adressresurs](#create-public-ip) i den här artikeln. 

3. Granska den nya IP-konfigurationen. Även om en privat IP-adress inte uttryckligen tilldelats, tilldelades en automatiskt till IP-konfigurationen, eftersom alla IP-konfigurationer måste ha en privat IP-adress.
4. Du kan klicka på **Lägg** till om du vill lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra lägger till IP-adresser.
5. Lägg till den privata IP-adressen i operativsystemet VM genom att slutföra stegen för operativsystemet i avsnittet [Lägg till IP-adresser i ett vm-operativsystem](#os-config) i den här artikeln. Lägg inte till den offentliga IP-adressen i operativsystemet.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associera den offentliga IP-adressresursen till en befintlig IP-konfiguration

1. Fyll i stegen i avsnittet [Kärnsteg](#coreadd) i den här artikeln.
2. Klicka på den IP-konfiguration som du vill lägga till den offentliga IP-adressresursen i.
3. Klicka på **IP-adress**i FÖNSTRET IPConfig som visas .
4. Välj en offentlig IP-adress i fönstret **Välj offentlig IP-adress** som visas.
5. Klicka på **Spara** och fönstren stängs. Om du inte har en befintlig offentlig IP-adress kan du skapa en genom att slutföra stegen i avsnittet [Skapa en offentlig IP-adressresurs](#create-public-ip) i den här artikeln.
3. Granska den nya IP-konfigurationen.
4. Du kan klicka på **Lägg** till om du vill lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra lägger till IP-adresser. Lägg inte till den offentliga IP-adressen i operativsystemet.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
