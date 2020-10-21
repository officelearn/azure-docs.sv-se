---
title: Skapa ett erbjudande för virtuella Azure-datorer på Azure Marketplace med din egen avbildning
description: Lär dig hur du publicerar ett erbjudande för virtuella datorer på Azure Marketplace med din egen avbildning.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284804"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Så här skapar du en virtuell dator med en egen avbildning

Den här artikeln beskriver hur du skapar och distribuerar en användardefinierad avbildning av en virtuell dator (VM).

> [!NOTE]
> Innan du påbörjar den här proceduren bör du gå igenom de [tekniska kraven](marketplace-virtual-machines.md#technical-requirements) för virtuella Azure-datorer, inklusive krav på virtuell hård disk (VHD).

Om du vill använda en godkänd bas avbildning i stället följer du anvisningarna i [skapa en avbildning av en virtuell dator från en godkänd bas](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Konfigurera den virtuella datorn

I det här avsnittet beskrivs hur du ändrar storlek, uppdaterar och generaliserar en virtuell Azure-dator. Dessa steg är nödvändiga för att förbereda din virtuella dator så att den distribueras på Azure Marketplace.

### <a name="size-the-vhds"></a>Ändra storlek på de virtuella hård diskarna

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Utför ytterligare säkerhets kontroller

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utför anpassad konfiguration och schemalagda aktiviteter

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>Ladda upp den virtuella hård disken till Azure

Konfigurera och Förbered den virtuella datorn så att den laddas upp enligt beskrivningen i [förbereda en Windows VHD eller VHDX för att ladda upp till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) eller [skapa och ladda upp en Linux-VHD](../virtual-machines/linux/create-upload-generic.md).

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>Extrahera den virtuella hård disken från avbildningen (om du använder avbildnings Bygg tjänster)

Om du använder en avbildnings Bygg tjänst, till exempel [Packer](https://www.packer.io/), kan du behöva extrahera den virtuella hård disken från avbildningen. Det finns inget direkt sätt att göra detta. Du måste skapa en virtuell dator och extrahera den virtuella hård disken från den virtuella dator disken.

### <a name="create-the-vm-on-the-azure-portal"></a>Skapa den virtuella datorn på Azure Portal

Följ de här stegen för att skapa den virtuella dator avbildningen på [Azure Portal](https://ms.portal.azure.com/).

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/).
2. Välj **Virtuella datorer**.
3. Välj **+ Lägg** till för att öppna skärmen **skapa en virtuell dator** .
4. Välj avbildningen i list rutan eller Välj **Bläddra bland alla offentliga och privata avbildningar** för att söka eller bläddra bland alla tillgängliga avbildningar av virtuella datorer.
5. Om du vill skapa en virtuell dator i **generation 2** går du till fliken **Avancerat** och väljer alternativet **gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Välj gen 1 eller gen 2.":::

6. Välj storleken på den virtuella dator som ska distribueras.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Välj gen 1 eller gen 2.":::

7. Ange övrig information som krävs för att skapa den virtuella datorn.
8. Välj **Granska + skapa** för att granska dina val. När meddelandet **verifieringen lyckades** visas väljer du **skapa**.

Azure börjar etablering av den virtuella dator som du har angett. Spåra förloppet genom att välja fliken **Virtual Machines** på den vänstra menyn. När den har skapats ändras statusen för den virtuella datorn till att **köras**.

### <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Se följande dokumentation för att ansluta till din virtuella [Windows](../virtual-machines/windows/connect-logon.md) -eller [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) -dator.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Nästa steg

- Rekommenderat nästa steg: [testa din VM-avbildning](azure-vm-image-test.md) för att säkerställa att den uppfyller publicerings kraven för Azure Marketplace. Detta är valfritt.
- Om du inte testar din VM-avbildning fortsätter du med [att generera SAS-URI: n](azure-vm-get-sas-uri.md).
- Om du har problem med att skapa din nya Azure-baserade virtuella hård disk läser du [vanliga frågor och svar om virtuella datorer för Azure Marketplace](azure-vm-create-faq.md).
