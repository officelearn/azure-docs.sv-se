---
title: Lagra ditt AppSource-paket i Azure Storage och generera en URL med SAS-nyckel
description: Information om de steg som krävs för att ladda upp och skydda ett AppSource-paket.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285375"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Lagra ditt AppSource-paket i Azure Storage och generera en URL med SAS-nyckel
=============================================================================

För att upprätthålla säkerheten för dina filer måste alla partner lagra sin AppSource-paketfil i ett Azure Blob Storage-konto och använda en SAS-nyckel för att dela den. Vi kommer att hämta paket filen från din Azure Storage-plats för certifiering och använda den för AppSource-utvärderingar.

Använd följande steg för att ladda upp paketet till Blob Storage:

1. Gå till <https://azure.microsoft.com> och skapa en kostnads fri utvärderings version eller ett fakturerat konto.

2. Logga in på [Azure-portalen](https://portal.azure.com/).

3. Skapa ett nytt lagrings konto genom att klicka på **+ nytt** och gå till **data + lagrings** kontot.

   ![Bladet data + lagring på Microsoft Azure Portal](media/CRMScreenShot7.png)

4. Ange ett **namn** och ett **resurs grupps** namn och klicka på knappen **skapa** .

   ![Skapa ett lagrings konto på Microsoft Azure Portal](media/CRMScreenShot8.png)

5. Navigera till den nya resurs gruppen och skapa en ny BLOB-behållare.

   ![Ladda upp paket som BLOB med hjälp av Microsoft Azure Portal](media/CRMScreenShot9.png)

6. Om du inte redan har gjort det kan du ladda ned och installera Microsoft [Azure Storage Explorer](https://storageexplorer.com/).

7. Öppna Storage Explorer och Använd ikonen för att ansluta till ditt Azure Storage-konto.

8. Navigera till BLOB-behållaren som du skapade och klicka på **överför** för att lägga till paketets zip-fil.

   ![Ladda upp paket med Microsoft Storage Explorer](media/CRMScreenShot10.png)

9. Högerklicka på filen och välj **Hämta signatur för delad åtkomst**.

   ![Hämta signaturen för delad åtkomst för en Azure-fil](media/CRMScreenShot11.png)

10. Ändra **förfallo tiden** för att göra sa aktiv i en månad och klicka sedan på **skapa**.

    ![Ändra förfallo datumet för SAS för en Azure-fil](media/CRMScreenShot12.png)

11. Kopiera URL-fältet och spara det för senare. Du måste ange den här URL: en när du skapar det associerade erbjudandet. 

    ![Kopiera SAS-URL: en för en Azure-fil](media/CRMScreenShot13.png)

