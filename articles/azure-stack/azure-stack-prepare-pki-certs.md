---
title: Förbered Azure Stack Public Key Infrastructure-certifikat för distribution av integrerade Azure Stack-system eller Hemlig rotation | Microsoft Docs
description: Beskriver hur du förbereder Azure Stack PKI-certifikat för integrerade Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/02/2019
ms.openlocfilehash: 496a6c45c9f8930f77b3a3dab2f81db1b24c1801
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247212"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Förbereda Azure Stack PKI-certifikat för användning i distributionen eller rotation
Certifikatdatabasen [hämtas från din Certifikatutfärdare valfri](azure-stack-get-pki-certs.md) måste importeras och exporteras med egenskaper som matchar certifikatkrav för Azure Stack.


## <a name="prepare-certificates-for-deployment"></a>Förbereda certifikat för distribution
Följ dessa steg för att förbereda och kontrollera Azure Stack PKI-certifikat som ska användas för att distribuera en ny Azure Stack-miljö eller för att rotera hemligheter i en befintlig Azure Stack-miljö: 

### <a name="import-the-certificate"></a>Importera certifikatet

1.  Kopiera de ursprungliga versionerna certifikat [hämtas från din Certifikatutfärdare valfri](azure-stack-get-pki-certs.md) i en katalog på värden för distribution. 
  > [!WARNING]
  > Kopiera inte filer som har redan tagits importeras, exporteras eller ändras på något sätt från filerna direkt från CA: N.

1.  Högerklicka på certifikatet och välj **installera certifikat** eller **Installera PFX** beroende på hur certifikaten som har levererats från din Certifikatutfärdare.

1. I den **guiden Importera certifikat**väljer **lokal dator** som plats för importen. Välj **Nästa**. På följande skärm, klicka på Nästa igen.

    ![Importplats för lokal dator](./media/prepare-pki-certs/1.png)

1.  Välj **placera alla certifikat i nedanstående arkiv** och välj sedan **förtroende för företag** som plats. Klicka på **OK** att stänga dialogrutan för val av certifikat store och sedan **nästa**.

    ![Konfigurera certifikatarkivet](./media/prepare-pki-certs/3.png)

    a. Om du importerar en PFX visas med en ytterligare dialogruta. På den **skydd av privat nyckel** anger du lösenordet för din certifikatfiler och sedan aktivera den **Markera den här nyckeln kan exporteras. Detta gör att du kan säkerhetskopiera eller flytta dina nycklar vid ett senare tillfälle** alternativet. Välj **Nästa**.

    ![Markera att nycklarna kan exporteras](./media/prepare-pki-certs/2.png)

1. Klicka på Slutför för att slutföra importen.

### <a name="export-the-certificate"></a>Exportera certifikatet

Öppna Certifikathanteraren MMC-konsolen och Anslut till lokala datorns certifikatarkiv.

1. Öppna Microsoft Management Console, i Windows 10 högerklickar du på Start-menyn och klicka på Kör. Typ **mmc** Klicka på ok.

1. Klicka på Arkiv, klicka på Lägg till snapin-modulen Lägg till/ta bort och sedan väljer certifikat.

    ![Lägga till snapin-modulen certifikat](./media/prepare-pki-certs/mmc-2.png)
 
1. Välj datorkonto, klicka på Nästa och sedan välja lokala datorn och Slutför. Klicka på ok om du vill stänga sidan Lägg till/ta bort snapin-modulen.

    ![Lägga till snapin-modulen certifikat](./media/prepare-pki-certs/mmc-3.png)

1. Bläddra till certifikat > förtroende för företag > Certifikatplatsen. Kontrollera att du ser ditt certifikat till höger.

1. Uppgiften fältet Certificate Manager-konsolen väljer du **åtgärder** > **alla uppgifter** > **exportera**. Välj **Nästa**.

  > [!NOTE]
  > Beroende på hur många Azure Stack behöva certifikat som du har du slutföra den här processen mer än en gång.

1. Välj **Ja, exportera den privata nyckeln**, och klicka sedan på **nästa**.

1. I avsnittet filformat för Export:
    
    - Välj **inkludera om möjligt alla certifikat i certifikatet**.  
    - Välj **exportera alla utökade egenskaper**.  
    - Välj **aktivera certifikatet sekretess**.  
    - Klicka på **Nästa**.  
    
    ![Guiden för export av certifikat med markerat alternativ](./media/prepare-pki-certs\azure-stack-save-cert.png)

1. Välj **lösenord** och ange ett lösenord för certifikaten. Kom ihåg lösenordet eftersom den används som distributionsparameter. Välj **Nästa**.

1. Välj ett filnamn och plats för pfx-fil att exportera. Välj **Nästa**.

1. Välj **Slutför**.

## <a name="next-steps"></a>Nästa steg

[Verifiera PKI-certifikat](azure-stack-validate-pki-certs.md)
