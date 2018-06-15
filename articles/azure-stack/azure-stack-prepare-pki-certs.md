---
title: Förbereda Azure Stack Public Key Infrastructure-certifikat för distribution av Azure-stacken integrerat system | Microsoft Docs
description: Beskriver hur du förbereder Azure Stack PKI-certifikat för Azure-stacken integrerat system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203484"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Förbereda Azure Stack PKI-certifikat för distribution
Certifikatfiler [från din Certifikatutfärdare väljer](azure-stack-get-pki-certs.md) måste importeras och exporteras med egenskaper matchar Azure-stacken certifikatkrav.


## <a name="prepare-certificates-for-deployment"></a>Förbereda certifikat för distribution
Följ dessa steg för att förbereda och validera Azure Stack PKI-certifikat: 

### <a name="import-the-certificate"></a>Importera certifikat

1.  Kopiera de ursprungliga versionerna certifikat [från din Certifikatutfärdare väljer](azure-stack-get-pki-certs.md) i en katalog på värden för distribution. 
  > [!WARNING]
  > Kopiera inte filer som har redan importeras, exporteras eller ändras på något sätt från filer direkt från Certifikatutfärdaren.

2.  Högerklicka på certifikatet och välj **installera certifikat** eller **Installera PFX** beroende på hur certifikatet som har skickats från din Certifikatutfärdare.

3. I den **guiden Importera certifikat**väljer **lokal dator** som plats för importen. Välj **Nästa**. På följande skärm, klicka på Nästa igen.

    ![Plats för importen av lokal dator](.\media\prepare-pki-certs\1.png)

4.  Välj **placera alla certifikat i nedanstående arkiv** och välj sedan **förtroende för företag** som platsen. Klicka på **OK** att stänga dialogrutan för val av certifikat store och sedan **nästa**.

    ![Konfigurera certifikatarkivet](.\media\prepare-pki-certs\3.png)

    a. Om du importerar en PFX visas en dialogruta som ytterligare. På den **skydd av privat nyckel** sidan, ange lösenordet för certifikatet filerna och sedan aktivera den **Markera den här nyckeln kan exporteras. Du kan säkerhetskopiera eller transportera dina nycklar vid ett senare tillfälle** alternativet. Välj **Nästa**.

    ![Markera att nycklarna kan exporteras](.\media\prepare-pki-certs\2.png)

5. Klicka på Slutför för att slutföra importen.

### <a name="export-the-certificate"></a>Exportera certifikatet

Öppna MMC Certificate Manager-konsolen och Anslut till lokala datorns certifikatarkiv.

1. Öppna Microsoft Management Console, Windows 10 högerklickar du på Start-menyn och klicka på Kör. Typen **mmc** Klicka på ok.

2. Klicka på Arkiv, klicka på Lägg till snapin-modulen Lägg till/ta bort och sedan väljer certifikat.

    ![Lägga till snapin-modulen certifikat](.\media\prepare-pki-certs\mmc-2.png)
 
3. Välj datorkonto, klicka på Nästa och sedan markera lokal dator sedan är klar. Klicka på ok om du vill stänga sidan Lägg till/ta bort snapin-modulen.

    ![Lägga till snapin-modulen certifikat](.\media\prepare-pki-certs\mmc-3.png)

4. Bläddra till certifikat > förtroende för företag > Certificate location. Kontrollera att du ser ditt certifikat till höger.

5. Välj uppgiften fältet certifikat Manager-konsolen **åtgärder** > **alla aktiviteter** > **exportera**. Välj **Nästa**.

  > [!NOTE]
  > Beroende på hur många Azure-stacken behöva certifikat som du har du slutför den här processen mer än en gång.

4. Välj **Ja, exportera den privata nyckeln**, och klicka sedan på **nästa**.

5. Markera under Filformat för Export **exportera alla utökade egenskaper** och klicka sedan på **nästa**.

6. Välj **lösenord** och ange ett lösenord för certifikaten. Kom ihåg detta lösenord eftersom den används som en parameter för distribution. Välj **Nästa**.

7. Välj ett filnamn och en plats att exportera pfx-fil. Välj **Nästa**.

8. Välj **Slutför**.

## <a name="next-steps"></a>Nästa steg
[Validera PKI-certifikat](azure-stack-validate-pki-certs.md)