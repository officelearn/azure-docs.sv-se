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
ms.openlocfilehash: dadb443f8b7739e3a18c0d3beb558d8c42e9d19c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Förbereda Azure Stack PKI-certifikat för distribution
Certifikatfiler [från din Certifikatutfärdare väljer](azure-stack-get-pki-certs.md) måste importeras och exporteras med egenskaper matchar Azure-stacken certifikatkrav.


## <a name="prepare-certificates-for-deployment"></a>Förbereda certifikat för distribution
Följ dessa steg för att förbereda och validera Azure Stack PKI-certifikat: 

1.  Kopiera de ursprungliga versionerna certifikat [från din Certifikatutfärdare väljer](azure-stack-get-pki-certs.md) i en katalog på värden för distribution. 
  > [!WARNING]
  > Kopiera inte filer som har redan importeras, exporteras eller ändras på något sätt från filer direkt från Certifikatutfärdaren.

2.  Importera certifikat på lokala datorns certifikatarkiv:

    a.  Högerklicka på certifikatet och välj **Installera PFX**.

    b.  I den **guiden Importera certifikat**väljer **lokal dator** som plats för importen. Välj **Nästa**.

    ![Plats för importen av lokal dator](.\media\prepare-pki-certs\1.png)

    c.  Välj **nästa** på den **Välj fil att importera** sidan.

    d.  På den **skydd av privat nyckel** sidan, ange lösenordet för certifikatet filerna och sedan aktivera den **Markera den här nyckeln kan exporteras. Du kan säkerhetskopiera eller transportera dina nycklar vid ett senare tillfälle** alternativet. Välj **Nästa**.

    ![Markera att nycklarna kan exporteras](.\media\prepare-pki-certs\2.png)

    e.  Välj **placera alla certifikat i nedanstående arkiv** och välj sedan **förtroende för företag** som platsen. Klicka på **OK** att stänga dialogrutan för val av certifikat store och sedan **nästa**.

    ![Konfigurera certifikatarkivet](.\media\prepare-pki-certs\3.png)

  f.    Klicka på **Slutför** att slutföra guiden Importera certifikat.

  g.    Upprepa proceduren för alla certifikat som du använder för din distribution.

3. Exportera certifikatet till PFX-format med krav på Azure Stack:

  a.    Öppna MMC Certificate Manager-konsolen och Anslut till lokala datorns certifikatarkiv.

  b.    Gå till den **förtroende för företag** directory.

  c.    Välj en av de certifikat som du har importerat i steg 2 ovan.

  d.    Välj uppgiften fältet certifikat Manager-konsolen **åtgärder** > **alla aktiviteter** > **exportera**.

  e.    Välj **Nästa**.

  f.    Välj **Ja, exportera den privata nyckeln**, och klicka sedan på **nästa**.

  g.    Markera under Filformat för Export **exportera alla utökade egenskaper** och klicka sedan på **nästa**.

  h.    Välj **lösenord** och ange ett lösenord för certifikaten. Kom ihåg detta lösenord eftersom den används som en parameter för distribution. Välj **Nästa**.

  i.    Välj ett filnamn och en plats att exportera pfx-fil. Välj **Nästa**.

  j.    Välj **Slutför**.

  k.    Upprepa proceduren för alla certifikat du importerade för distributionen i steg 2 ovan.

## <a name="next-steps"></a>Nästa steg
[Validera PKI-certifikat](validate-pki-certs.md)