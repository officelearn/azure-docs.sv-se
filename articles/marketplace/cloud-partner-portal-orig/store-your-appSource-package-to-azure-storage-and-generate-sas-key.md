---
title: Store ditt AppSource-paket till Azure storage och generera en URL med SAS-nyckel | Microsoft Docs
description: Beskriver de steg som krävs för att ladda upp och skydda ett AppSource-paket.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ad0e6eaae5c0fad74ea484827e0f8d535cfbf579
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884667"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Store ditt AppSource-paket till Azure storage och generera en URL med SAS-nyckel
=============================================================================

För att upprätthålla säkerheten för dina filer, måste alla partner lagra sina AppSource paketfilen i ett Azure blob storage-konto och använda en SAS-nyckel för att dela den. Vi hämtar paketfilen från dina Azure-lagringsplats för certifiering och för att använda den i AppSource utvärderingsversioner.

Använd följande steg för att ladda upp ditt paket till blob storage:

1. Gå till <https://azure.microsoft.com> och skapa ett kostnadsfritt konto utvärderingsversion eller faktureras.

2. Logga in på [Azure Portal](https://portal.azure.com/).

3. Skapa ett nytt Lagringskonto genom att klicka på **+ ny** och gå till den **Data + lagring** konto.

   ![Data + lagring-bladet på Microsoft Azure Portal](media/CRMScreenShot7.png)

4. Ange en **namn** och **resursgrupp** namn och klickar på **skapa** knappen.

   ![Skapa storage-konto på Microsoft Azure Portal](media/CRMScreenShot8.png)

5. Gå till din nya resursgrupp och skapa en ny blobbehållare.

   ![Ladda upp paketet som blob med Microsoft Azure Portal](media/CRMScreenShot9.png)

6. Om du inte redan har gjort det, ladda ned och installera Microsoft [Azure Storage Explorer](https://storageexplorer.com/).

7. Öppna Storage Explorer och använda ikonen för att ansluta till ditt Azure storage-konto.

8. Navigera till blob-behållaren som du skapade och klicka på **överför** att lägga till ditt paket zip-filen.

   ![Ladda upp paketet med Microsoft Storage Explorer](media/CRMScreenShot10.png)

9. Högerklicka på filen och välj **hämta signatur för delad åtkomst**.

   ![Hämta signatur för delad åtkomst till en Azure-fil](media/CRMScreenShot11.png)

10. Ändra den **förfallotiden** om du vill aktivera SAS för en månad, klicka sedan på **skapa**.

    ![Ändra SAS utgångsdatumet för en Azure-fil](media/CRMScreenShot12.png)

11. Kopiera URL-fältet och spara den till senare. Du måste ange URL: en när du skapar det associerade erbjudandet. 

    ![Kopiera SAS-Webbadressen till en Azure-fil](media/CRMScreenShot13.png)

