---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013786"
---
Storage Explorer 1.10.0 gör det möjligt för användare att ladda upp, ladda ned och kopiera hanterade diskar samt skapa ögonblicksbilder. På grund av dessa ytterligare funktioner kan du använda Storage Explorer för att migrera data från lokala till Azure och migrera data över Azure-regioner.

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här artikeln behöver du följande:
- En Azure-prenumeration
- En eller flera Hanterade Azure-diskar
- Den senaste versionen av [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

Om din Storage Explorer inte är ansluten till Azure kan du inte använda den för att hantera resurser. Det här avsnittet går över att ansluta den till ditt Azure-konto så att du kan hantera resurser med Lagringsutforskaren.

1. Starta Azure Storage Explorer och klicka på **plugin-ikonen** till vänster.

    ![Klicka på plugin-ikonen](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Välj **Lägg till ett Azure-konto**och klicka sedan på **Nästa**.

    ![Lägga till ett Azure-konto](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Ange dina Azure-autentiseringsuppgifter i dialogrutan **Azure-inloggning.**

    ![Dialogrutan Logga in i Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Välj din prenumeration i listan och klicka sedan på **Använd**.

    ![Välj din prenumeration](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Ladda upp en hanterad disk från en vhd-fil på prem

1. Expandera **Diskar** i den vänstra rutan och välj den resursgrupp som du vill ladda upp disken till.

    ![Välj resursgrupp 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Välj **Överför**.

    ![Välj Överför](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. I **Ladda upp VHD** ange din källa VHD, namnet på disken, OS-typen, den region du vill ladda upp disken till, samt kontotyp. I vissa regioner stöds tillgänglighetszoner, för dessa regioner kan du välja en zon som du väljer.
1. Välj **Skapa** om du vill börja ladda upp disken.

    ![Dialogrutan Ladda upp vhd](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Statusen för överföringen visas nu i **Aktiviteter**.

    ![Ladda upp status](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Om överföringen är klar och du inte ser disken i den högra rutan väljer du **Uppdatera**.

## <a name="download-a-managed-disk"></a>Ladda ned en hanterad disk

Följande steg förklarar hur du hämtar en hanterad disk till en vhd-hårddisk på prem. En disk tillstånd måste vara **Lös för** att kunna hämtas, du kan inte hämta en **bifogad** disk.

1. Om den inte redan är expanderad i den vänstra rutan expanderar du **Diskar** och väljer den resursgrupp som du vill hämta disken från.

    ![Välj resursgrupp 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Markera den disk som du vill hämta i den högra rutan.
1. Välj **Hämta** och välj sedan var du vill spara disken.

    ![Ladda ned en hanterad disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Välj **Spara** så börjar disken laddas ned. Statusen för nedladdningen visas i **Aktiviteter**.

    ![Ladda ner status](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Med Storage Explorer kan du kopiera en hårddisk i eller mellan regioner. Så här kopierar du en disk:

1. Välj den resursgrupp som innehåller den disk som du vill kopiera i listrutan **Diskar** till vänster.

    ![Välj resursgrupp 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Markera den disk som du vill kopiera i den högra rutan och välj **Kopiera**.

    ![Kopiera en hanterad disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. Markera den resursgrupp som du vill klistra in disken i i den vänstra rutan.

    ![Välj resursgrupp 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Välj **Klistra in** i den högra rutan.

    ![Klistra in en hanterad disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Fyll i värdena i dialogrutan **Klistra in disk.** Du kan också ange en tillgänglighetszon i regioner som stöds.

    ![Dialogrutan Klistra in disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Välj **Klistra in** och disken börjar kopieras, statusen visas i **Aktiviteter**.

    ![Kopiera inklistringsstatus](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

1. Välj den resursgrupp som innehåller den disk som du vill ögonblicksbilda i listrutan **Diskar** till vänster.

    ![Välj resursgrupp 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Till höger väljer du den disk som du vill ögonblicksbild och väljer **Skapa ögonblicksbild**.

    ![Skapa en ögonblicksbild](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. Ange namnet på ögonblicksbilden och den resursgrupp som du vill skapa den i i **Skapa ögonblicksbild.** Välj sedan **Skapa**.

    ![Dialogrutan Skapa ögonblicksbilder](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. När ögonblicksbilden har skapats kan du välja **Öppna i portalen** i **Aktiviteter** för att visa ögonblicksbilden i Azure-portalen.

    ![Öppna ögonblicksbild i portalen](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Nästa steg
