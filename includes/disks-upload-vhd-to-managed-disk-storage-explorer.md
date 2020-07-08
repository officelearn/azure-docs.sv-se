---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74013786"
---
Storage Explorer 1.10.0 gör det möjligt för användare att ladda upp, ladda ned och kopiera hanterade diskar samt skapa ögonblicks bilder. På grund av dessa ytterligare funktioner kan du använda Storage Explorer för att migrera data från lokala datorer till Azure och migrera data i Azure-regioner.

## <a name="prerequisites"></a>Krav

För att slutföra den här artikeln behöver du följande:
- En Azure-prenumeration
- En eller flera Azure Managed disks
- Den senaste versionen av [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

Om din Storage Explorer inte är ansluten till Azure, kan du inte använda den för att hantera resurser. Det här avsnittet går igenom hur du ansluter det till ditt Azure-konto så att du kan hantera resurser med hjälp av Storage Explorer.

1. Starta Azure Storage Explorer och klicka på **plugin-** ikonen till vänster.

    ![Klicka på plugin-ikonen](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Välj **Lägg till ett Azure-konto**och klicka sedan på **Nästa**.

    ![Lägga till ett Azure-konto](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. I dialog rutan **Azure Sign in** anger du dina autentiseringsuppgifter för Azure.

    ![Dialog rutan Azure-inloggning](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Välj din prenumeration i listan och klicka sedan på **Använd**.

    ![Välj din prenumeration](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Ladda upp en hanterad disk från en lokal virtuell hård disk

1. I den vänstra rutan expanderar du **diskar** och väljer den resurs grupp som du vill ladda upp disken till.

    ![Välj resurs grupp 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Välj **Överför**.

    ![Välj Överför](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. I **Ladda upp VHD** anger du din käll-VHD, namnet på disken, OS-typen, den region som du vill ladda upp disken till, samt konto typen. I vissa regioner finns det stöd för tillgänglighets zoner, för de regionerna kan du välja en valfri zon.
1. Välj **skapa** för att börja ladda upp disken.

    ![Ladda upp VHD-dialogruta](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Status för överföringen visas nu i **aktiviteter**.

    ![Överförings status](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Om uppladdningen är färdig och du inte ser disken i den högra rutan väljer du **Uppdatera**.

## <a name="download-a-managed-disk"></a>Ladda ned en hanterad disk

Följande steg beskriver hur du laddar ned en hanterad disk till en virtuell hård disk på lokal. En disks tillstånd måste vara **frånkopplad** för att kunna hämtas kan du inte ladda ned en **ansluten** disk.

1. I den vänstra rutan, om den inte redan är expanderad, expanderar du **diskar** och väljer den resurs grupp som du vill ladda ned disken från.

    ![Välj resurs grupp 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. I den högra rutan väljer du den disk som du vill ladda ned.
1. Välj **Hämta** och välj sedan var du vill spara disken.

    ![Ladda ned en hanterad disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Välj **Spara** så börjar disken att laddas ned. Hämtningens status visas i **aktiviteter**.

    ![Hämtnings status](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Med Storage Explorer kan du kopiera en hanterade-disk inom eller mellan regioner. Så här kopierar du en disk:

1. I list rutan **diskar** till vänster väljer du den resurs grupp som innehåller den disk som du vill kopiera.

    ![Välj resurs grupp 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. I den högra rutan väljer du den disk som du vill kopiera och väljer **Kopiera**.

    ![Kopiera en hanterad disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. I den vänstra rutan väljer du den resurs grupp som du vill klistra in disken i.

    ![Välj resurs grupp 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Välj **Klistra in** i den högra rutan.

    ![Klistra in en hanterad disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Fyll i värdena i dialog rutan **Klistra in disk** . Du kan också ange en tillgänglighets zon i regioner som stöds.

    ![Dialog rutan klistra in disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Välj **Klistra in** så kommer din disk att börja kopieras, statusen visas i **aktiviteter**.

    ![Kopiera Inklistrings status](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

1. I list rutan **diskar** till vänster väljer du den resurs grupp som innehåller den disk som du vill ögonblicks bild av.

    ![Välj resurs grupp 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Till höger väljer du den disk som du vill ögonblicks bild av och väljer **skapa ögonblicks bild**.

    ![Skapa en ögonblicksbild](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. I **skapa ögonblicks bild**anger du namnet på ögonblicks bilden och den resurs grupp som du vill skapa den i. Välj sedan **Skapa**.

    ![Dialog rutan skapa ögonblicks bild](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. När ögonblicks bilden har skapats kan du välja **Öppna i portalen** i **aktiviteter** för att Visa ögonblicks bilden i Azure Portal.

    ![Öppna ögonblicks bild i portalen](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Nästa steg
