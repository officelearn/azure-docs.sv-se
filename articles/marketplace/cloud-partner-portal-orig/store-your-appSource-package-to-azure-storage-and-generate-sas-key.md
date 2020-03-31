---
title: Lagra ditt AppSource-paket till Azure-lagring och generera en URL med SAS-nyckel
description: Information om de steg som krävs för att ladda upp och säkra ett AppSource-paket.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285375"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Lagra ditt AppSource-paket till Azure-lagring och generera en URL med SAS-nyckel
=============================================================================

För att upprätthålla säkerheten för dina filer måste alla partner lagra sin AppSource-paketfil i ett Azure blob storage-konto och använda en SAS-nyckel för att dela den. Vi hämtar paketfilen från din Azure-lagringsplats för certifiering och för att använda den för AppSource-utvärderingsversioner.

Gör så här för att ladda upp paketet till blob-lagring:

1. Gå <https://azure.microsoft.com> till och skapa en kostnadsfri utvärderingsversion eller ett fakturerat konto.

2. Logga in på [Azure Portal](https://portal.azure.com/).

3. Skapa ett nytt lagringskonto genom att klicka på **+ Nytt** och gå till **data + lagringskontot.**

   ![Data + lagringsblad på Microsoft Azure Portal](media/CRMScreenShot7.png)

4. Ange **namn-** och **resursgruppsnamn** och klicka på **Knappen Skapa.**

   ![Skapa lagringskonto på Microsoft Azure Portal](media/CRMScreenShot8.png)

5. Navigera till din nyskapade resursgrupp och skapa en ny blob-behållare.

   ![Ladda upp paket som blob med Microsoft Azure Portal](media/CRMScreenShot9.png)

6. Om du inte redan har gjort det hämtar och installerar du Microsoft [Azure Storage Explorer](https://storageexplorer.com/).

7. Öppna Storage Explorer och använd ikonen för att ansluta till ditt Azure-lagringskonto.

8. Navigera till blob-behållaren som du skapade och klicka på **Ladda upp** för att lägga till zip-filen för paketet.

   ![Ladda upp paket med Microsoft Storage Explorer](media/CRMScreenShot10.png)

9. Högerklicka på filen och välj **Hämta signature för delad åtkomst**.

   ![Få signature för delad åtkomst för en Azure-fil](media/CRMScreenShot11.png)

10. Ändra **förfallotiden** för att göra SAS-systemet aktivt i en månad och klicka sedan på **Skapa**.

    ![Ändra SAS utgångsdatum för en Azure-fil](media/CRMScreenShot12.png)

11. Kopiera URL-fältet och spara det till senare. Du måste ange den här webbadressen när du skapar det associerade erbjudandet. 

    ![Kopiera SAS-URL:en för en Azure-fil](media/CRMScreenShot13.png)

