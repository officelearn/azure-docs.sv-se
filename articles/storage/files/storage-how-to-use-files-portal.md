---
title: "Hantera Azure Files från Azure-portalen | Microsoft Docs"
description: "Lär dig hur du använder Azure-portalen för att hantera Azure Files."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: e56f8bf1057a8bc2cfcde841f69022104bafff27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-files-from-the-azure-portal"></a>Använda Azure Files från Azure Portal
[Azure-portalen](https://portal.azure.com) innehåller ett användargränssnitt för att hantera Azure Files. Du kan utföra följande åtgärder från din webbläsare:

* Skapa en filresurs
* Ladda upp och ladda ned filer till och från filresursen.
* Övervaka den faktiska användningen av varje filresurs.
* Justera storlekskvoten för filresurser.
* Kopiera monteringskommandon som du använder för att montera filresursen från en Windows- eller Linux-klient.

## <a name="create-file-share"></a>Skapa en filresurs
1. Logga in på Azure Portal.
2. Klicka på **Storage-konton** eller **Storage-konton (klassiska)** på navigeringsmenyn.
    
    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. Välj ditt lagringskonto.

    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. Välj tjänsten ”Filer”.

    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. Klicka på ”Filresurser” och följ länken för att skapa din första filresurs.

    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. Fyll i filresursens namn och storleken på filresursen (upp till 5 120 GB) för att skapa din första filresurs. När filresursen har skapats kan du montera den från alla filsystem som har stöd för SMB 2.1 eller SMB 3.0. Du kan klicka på **Kvot** på filresursen för att ändra storleken på filen till upp till 5120 GB. Se [Priskalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna lagringskostnaden för att använda Azure Files.

    ![Skärmbild som visar hur du skapar en filresurs på portalen](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>Ladda upp och ladda ned filer
1. Välj en filresurs som du redan har skapat.

    ![Skärmbild som visar hur du laddar upp och laddar ned filer från portalen](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. Öppna användargränssnittet för filöverföring genom att klicka på **Överför**.

    ![Skärmbild som visar hur du laddar upp filer från portalen](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>Anslut till filresurs
-  Klicka på **Anslut** om du vill hämta kommandoraden för att montera filresursen från Windows eller Linux. Linux-användare kan även läsa [Använda Azure Files med Linux](../storage-how-to-use-files-linux.md) för mer information om montering för andra Linux-distributioner.

    ![Skärmbild som visar hur du monterar filresursen](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  Du kan kopiera kommandon för att montera filresurser på Windows eller Linux och köra dem från en virtuell Azure-dator eller en lokal dator.

    ![Skärmbild som visar kommandon för montering på Windows och Linux](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**Tips:**  
Klicka på **Visa åtkomstnycklar för lagringskontot** längst ned på sidan Anslut för att hitta åtkomstnyckeln till lagringskontot för montering.

## <a name="see-also"></a>Se även
Mer information om Azure Files finns på följande länkar.

* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning i Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Felsökning i Linux](storage-troubleshoot-linux-file-connection-problems.md)    
