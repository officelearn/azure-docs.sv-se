---
title: Flera IP-adresser för virtuella Azure-datorer – Portal | Microsoft Docs
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med hjälp av Azure Portal | Resource Manager.
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
ms.openlocfilehash: 66cbb843369dee103f102c9c743da544a833ccf1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356612"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Tilldela flera IP-adresser till virtuella datorer med hjälp av Azure Portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Den här artikeln beskriver hur du skapar en virtuell dator (VM) via Azure Resource Manager distributions modellen med hjälp av Azure Portal. Det går inte att tilldela flera IP-adresser till resurser som skapats via den klassiska distributions modellen. Läs mer om Azures distributions modeller i artikeln [förstå distributions modeller](../resource-manager-deployment-model.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Om du vill skapa en virtuell dator med flera IP-adresser eller en statisk privat IP-adress måste du skapa den med hjälp av PowerShell eller Azure CLI. Klicka på PowerShell-eller CLI-alternativen överst i den här artikeln om du vill veta mer. Du kan skapa en virtuell dator med en enda dynamisk privat IP-adress och (valfritt) en enskild offentlig IP-adress. Använd portalen genom att följa stegen i [skapa en virtuell Windows-dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md) eller [skapa en Linux VM](../virtual-machines/linux/quick-create-portal.md) -artikel. När du har skapat den virtuella datorn kan du ändra IP-adress typen från dynamisk till statisk och lägga till ytterligare IP-adresser med hjälp av portalen genom att följa stegen i avsnittet [Lägg till IP-adresser till en virtuell dator](#add) i den här artikeln.

## <a name="add"></a>Lägg till IP-adresser till en virtuell dator

Du kan lägga till privata och offentliga IP-adresser i ett Azure-nätverks gränssnitt genom att följa anvisningarna nedan. I exemplen i följande avsnitt förutsätts att du redan har en virtuell dator med de tre IP-konfigurationerna som beskrivs i [scenariot](#scenario), men det är inte obligatoriskt.

### <a name="coreadd"></a>Grundläggande steg

1. Bläddra till Azure Portal på https://portal.azure.com och logga in på den, om det behövs.
2. I portalen klickar du på **fler tjänster** > Ange *virtuella datorer* i filter rutan och klickar sedan på **virtuella datorer**.
3. I fönstret **virtuella datorer** klickar du på den virtuella dator som du vill lägga till IP-adresser i. Klicka på **nätverks gränssnitt** i rutan virtuell dator som visas och välj sedan det nätverks gränssnitt som du vill lägga till IP-adresserna i. I exemplet som visas i följande bild är NÄTVERKSKORTet med namnet *myNIC* från den virtuella datorn med namnet *myVM* valt:

    ![Nätverksgränssnitt](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. I fönstret som visas för det nätverkskort du har valt klickar du på **IP-konfigurationer**.

Slutför stegen i något av de avsnitt som följer, baserat på vilken typ av IP-adress du vill lägga till.

### <a name="add-a-private-ip-address"></a>**Lägg till en privat IP-adress**

Utför följande steg för att lägga till en ny privat IP-adress:

1. Slutför stegen i avsnittet [grundläggande steg](#coreadd) i den här artikeln.
2. Klicka på **Lägg till**. I rutan **Lägg till IP-konfiguration** som visas skapar du en IP-konfiguration med namnet *ipconfig-4* med *10.0.0.7* som en *statisk* privat IP-adress och klickar sedan på **OK**.

    > [!NOTE]
    > När du lägger till en statisk IP-adress måste du ange en oanvänd, giltig adress på under nätet som NÄTVERKSKORTet är anslutet till. Om adressen du väljer inte är tillgänglig visar portalen ett X för IP-adressen och du måste välja en annan.

3. När du klickar på OK stängs fönstret och den nya IP-konfigurationen visas. Klicka på **OK** för att stänga fönstret **Lägg till IP-konfiguration** .
4. Du kan klicka på **Lägg** till för att lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra tillägg av IP-adresser.
5. Lägg till de privata IP-adresserna till operativ systemet VM genom att slutföra stegen i avsnittet [Lägg till IP-adresser till ett operativ system för virtuella datorer](#os-config) i den här artikeln.

### <a name="add-a-public-ip-address"></a>Lägg till en offentlig IP-adress

En offentlig IP-adress läggs till genom att associera en offentlig IP-adressresurs till antingen en ny IP-konfiguration eller en befintlig IP-konfiguration.

> [!NOTE]
> Offentliga IP-adresser har en nominell avgift. Läs mer om pris information om IP-adresser i [pris](https://azure.microsoft.com/pricing/details/ip-addresses) sidan för IP-adresser. Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-public-ip"></a>Skapa en offentlig IP-adressresurs

En offentlig IP-adress är en inställning för en offentlig IP-adressresurs. Om du har en offentlig IP-adressresurs som för närvarande inte är kopplad till en IP-konfiguration som du vill koppla till en IP-konfiguration, hoppar du över följande steg och slutför stegen i något av de avsnitt som följer, efter behov. Om du inte har någon tillgänglig offentlig IP-adressresurs utför du följande steg för att skapa en:

1. Bläddra till Azure Portal på https://portal.azure.com och logga in på den, om det behövs.
3. I portalen klickar du på **skapa en resurs** > **nätverk** > **offentlig IP-adress**.
4. I fönstret **skapa offentlig IP-adress** som visas anger du ett **namn**, väljer en **IP-adress tilldelnings** typ, en **prenumeration**, en **resurs grupp**och en **plats**och klickar sedan på **skapa**, som du ser i följande bild:

    ![Skapa en offentlig IP-adressresurs](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Slutför stegen i något av de avsnitt som följer för att associera den offentliga IP-adressresursen till en IP-konfiguration.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associera den offentliga IP-adressresursen till en ny IP-konfiguration

1. Slutför stegen i avsnittet [grundläggande steg](#coreadd) i den här artikeln.
2. Klicka på **Lägg till**. I rutan **Lägg till IP-konfiguration** som visas skapar du en IP-konfiguration med namnet *ipconfig-4*. Aktivera den **offentliga IP-adressen** och välj en befintlig, tillgänglig offentlig IP-adressresurs från fönstret **Välj offentlig IP-adress** som visas.

    När du har valt den offentliga IP-adressresursen klickar du på **OK** så stängs fönstret. Om du inte har en befintlig offentlig IP-adress kan du skapa en genom att följa stegen i avsnittet [skapa en offentlig IP-adress](#create-public-ip) i den här artikeln. 

3. Granska den nya IP-konfigurationen. Även om en privat IP-adress inte uttryckligen tilldelades, har en automatiskt tilldelats IP-konfigurationen, eftersom alla IP-konfigurationer måste ha en privat IP-adress.
4. Du kan klicka på **Lägg** till för att lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra tillägg av IP-adresser.
5. Lägg till den privata IP-adressen till den virtuella datorns operativ system genom att slutföra stegen för ditt operativ system i avsnittet [Lägg till IP-adresser till ett operativ system för virtuella datorer](#os-config) i den här artikeln. Lägg inte till den offentliga IP-adressen i operativ systemet.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associera den offentliga IP-adressresursen till en befintlig IP-konfiguration

1. Slutför stegen i avsnittet [grundläggande steg](#coreadd) i den här artikeln.
2. Klicka på den IP-konfiguration som du vill lägga till den offentliga IP-adressresursen till.
3. I fönstret IPConfig som visas klickar du på **IP-adress**.
4. I fönstret **Välj offentlig IP-adress** som visas väljer du en offentlig IP-adress.
5. Klicka på **Spara** och Fönstren stängs. Om du inte har en befintlig offentlig IP-adress kan du skapa en genom att följa stegen i avsnittet [skapa en offentlig IP-adress](#create-public-ip) i den här artikeln.
3. Granska den nya IP-konfigurationen.
4. Du kan klicka på **Lägg** till för att lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra tillägg av IP-adresser. Lägg inte till den offentliga IP-adressen i operativ systemet.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
