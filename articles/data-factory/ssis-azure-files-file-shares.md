---
title: Öppna och spara filer med SSIS-paket som distribueras i Azure
description: Lär dig hur du öppnar och sparar filer lokalt och i Azure när du lyfter och flyttar SSIS-paket som använder lokala filsystem till SSIS i Azure
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 630a8ead409ad60b9146838f062210a3301b43b5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760204"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Öppna och spara filer lokalt och i Azure med SSIS-paket som distribueras i Azure

I den här artikeln beskrivs hur du öppnar och sparar filer lokalt och i Azure när du lyfter och flyttar SSIS-paket som använder lokala filsystem till SSIS i Azure.

## <a name="save-temporary-files"></a>Spara temporära filer

Om du behöver lagra och bearbeta temporära filer under en enda`.`paketkörning kan`%TEMP%`paket använda den aktuella arbetskatalogen ( ) eller den tillfälliga mappen ( ) för dina Azure-SSIS Integration Runtime-noder.

## <a name="use-on-premises-file-shares"></a>Använda lokala filresurser

Så här fortsätter du att använda **lokala filresurser** när du lyfter och flyttar paket som använder lokala filsystem till SSIS i Azure:

1. Överför filer från lokala filsystem till lokala filresurser.

2. Gå med i lokala filresurser till ett virtuellt Azure-nätverk.

3. Gå med i din Azure-SSIS IR till samma virtuella nätverk. Mer information finns i [Ansluta till en Azure-SSIS-integreringskörning till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Anslut din Azure-SSIS IR till lokala filresurser i samma virtuella nätverk genom att konfigurera åtkomstautentiseringsuppgifter som använder Windows-autentisering. Mer information finns i [Ansluta till data- och filresurser med Windows-autentisering](ssis-azure-connect-with-windows-auth.md).

5. Uppdatera lokala filsökvägar i dina paket till UNC-sökvägar som pekar på lokala filresurser. Uppdatera till `C:\abc.txt` exempel `\\<on-prem-server-name>\<share-name>\abc.txt`.

## <a name="use-azure-file-shares"></a>Använda Azure-filresurser

Så här använder du **Azure Files** när du lyfter och flyttar paket som använder lokala filsystem till SSIS i Azure:

1. Överför filer från lokala filsystem till Azure Files. Mer information finns i [Azure Files](https://azure.microsoft.com/services/storage/files/).

2. Anslut din Azure-SSIS IR till Azure-filer genom att konfigurera åtkomstautentiseringsuppgifter som använder Windows-autentisering. Mer information finns i [Ansluta till data- och filresurser med Windows-autentisering](ssis-azure-connect-with-windows-auth.md).

3. Uppdatera lokala filsökvägar i dina paket till UNC-sökvägar som pekar på Azure-filer. Uppdatera till `C:\abc.txt` exempel `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`.

## <a name="next-steps"></a>Nästa steg

- Distribuera dina paket. Mer information finns i [Distribuera ett SSIS-projekt till Azure med SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Kör dina paket. Mer information finns [i Kör SSIS-paket i Azure med SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Schemalägg dina paket. Mer information finns i [Schemalägga SSIS-paket i Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).
