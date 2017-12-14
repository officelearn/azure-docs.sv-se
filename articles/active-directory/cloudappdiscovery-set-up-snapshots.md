---
title: "Skapa rapporter för Cloud App Discovery ögonblicksbild i Azure Active Directory | Microsoft Docs"
description: "Innehåller information om att söka efter och hantera program med Cloud App Discovery, vilka är fördelarna och hur det fungerar."
services: active-directory
keywords: cloud app discovery, hantera program
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: 69a633b39e68596c536700dbb2c7c8d35f3b44bb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Skapa rapporter för ögonblicksbild av Cloud App Discovery

Överför en loggen manuellt innan du konfigurerar automatisk logginsamlaren och låt Cloud App Security tolkas. Om du ännu inte har en logg och du vill se ett exempel på hur loggen ska se ut, använder du proceduren nedan för att hämta en exempelfil loggen om du vill se vad loggen ska se ut.

## <a name="to-create-a-snapshot-report"></a>Så här skapar du en ögonblicksbild av en rapport

1. Samla in loggfiler från brandväggen och proxyservern servern som användare i din organisation åtkomst till Internet. Samla in loggar under tider med högtrafik som är representativ för användaraktivitet i din organisation.
2. På [menyraden Cloud App Security](https://portal.cloudappsecurity.com)väljer **identifiera**, och sedan **skapa rapportögonblicksbild**.
  
  ![Skapa ny rapport för ögonblicksbild](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. Ange en **rapportnamnet** och en **beskrivning**.
    
  ![Ny ögonblicksbild av en rapport](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. Välj den **datakällan** som du vill ladda upp loggfilerna från.
5. Kontrollera ditt loggformat se till att den är korrekt formaterad enligt exemplet kan du hämta. Klicka på **visa och verifiera** och klicka sedan på **Download exempellogg**. Jämför din logg med exemplet som anges för att kontrollera att den är kompatibel.
  
  ![Kontrollera ditt loggformat](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > FTP-exempel formatet stöds i ögonblicksbilder och automatiserade överför medan syslog har stöd för automatisk uppladdning. Hämta en exempellogg laddar ned en exempellogg FTP.
6. **Välj trafikloggarna** som du vill överföra. Du kan ladda upp högst 20 filer samtidigt. Komprimerade och komprimerade filer stöds också.
  
7. Klicka på **Skapa**. Det kan ta lite tid för dem att parsas och analyseras när överföringen har slutförts. Om den finns, skickar Cloud App Discovery ett e-postmeddelande när de är klar.

8. Välj **hantera ögonblicksbild rapporter** och välj ögonblicksbild av rapporten.
  
  ![Hantering av ögonblicksbilder](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Cloud App Discovery i Azure AD](cloudappdiscovery-get-started.md)
* [Konfigurera automatisk logguppladdningen för kontinuerlig rapportering](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Använda en parser för loggar](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
