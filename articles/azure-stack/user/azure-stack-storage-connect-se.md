---
title: Ansluta Lagringsutforskaren till en Azure-Stack-prenumeration eller ett lagringskonto | Microsoft Docs
description: Lär dig att ansluta Lagringsutforskaren till en Azure-Stack-prenumeration
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 9704f05cc6da97e33c0043b93acedc9e66bdcc36
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714909"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Ansluta Lagringsutforskaren till en Azure-Stack-prenumeration eller ett lagringskonto

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

I den här artikeln lär du dig hur du ansluter till dina Azure-stacken prenumerationer och storage-konton med Lagringsutforskaren. Azure Lagringsutforskaren är en fristående app som gör det enkelt att arbeta med Stack för Azure storage-data i Windows, macOS och Linux.

> [!NOTE]  
> Det finns flera tillgängliga verktyg för att flytta data till och från stacken för Azure storage. Mer information finns i [dataöverföring verktyg för Azure-stacken lagring](azure-stack-storage-transfer.md).

Om du inte har installerat Lagringsutforskaren ännu, [hämta Lagringsutforskaren](http://www.storageexplorer.com/) och installera den.

När du ansluter till en Azure-Stack-prenumeration eller ett lagringskonto, kan du använda den [Azure storage explorer artiklar](../../vs-azure-tools-storage-manage-with-storage-explorer.md) att arbeta med Azure Stack-data. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Förbereda för att ansluta till Azure-stacken

Du behöver direkt åtkomst till Azure-Stack eller en VPN-anslutning för Lagringsutforskaren får åtkomst till stacken för Azure-prenumeration. Information om hur du konfigurerar en VPN-anslutning till Azure Stack finns i [Connect to Azure Stack with VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Anslut till Azure Stack via VPN).

För Azure-stacken Development Kit måste du exportera Azure Stack rotcertifikat.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportera och importera sedan certifikatet för Azure-stacken

1. Öppna `mmc.exe` på en Azure-Stack-värddatorn eller en lokal dator med en VPN-anslutning till Azure-stacken. 

2. I **filen**väljer **Lägg till/ta bort snapin-modulen**, och Lägg sedan till **certifikat** att hantera **mitt användarkonto**.

3. Under **konsolen Root\Certificated (lokal dator) \Trusted Root Certification Authorities\Certificates** hitta **AzureStackSelfSignedRootCert**.

    ![Läsa in rotcertifikatet för Azure Stack via mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Högerklicka på certifikatet, Välj **alla aktiviteter** > **exportera**, och följ sedan anvisningarna för att exportera certifikat med **Base64-kodad X.509 (. CER)**.

    Det exporterade certifikatet ska användas i nästa steg.

5. Starta Lagringsutforskaren, och om du ser den **Anslut till Azure Storage** dialogrutan rutan, avbryta den.

6. På den **redigera** menyn, peka på **SSL-certifikat**, och välj sedan **Importera certifikat**. Använd filväljaren till att leta rätt på och öppna certifikatet du exporterade i föregående steg.

    När du har importerat certifikatet, uppmanas du att starta om Lagringsutforskaren.

    ![Importera certifikatet till Lagringsutforskaren](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Lagringsutforskaren startar om och välj den **redigera** -menyn och kontrollera om **mål Azure Stack** är markerad. Välj om den inte **mål Azure Stack**, och starta sedan om Lagringsutforskaren för att ändringarna ska börja gälla. Denna konfiguration krävs för kompatibilitet med Azure Stack-miljön.

    ![Se till att Target Azure Stack (Använd Azure Stack som mål) är markerat](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Ansluta till en Azure Stack-prenumeration

Använd följande steg för att ansluta Lagringsutforskaren till en prenumeration på Azure-stacken.

1. Välj i den vänstra rutan i Lagringsutforskaren **hantera konton**. 
    Alla Microsoft-prenumeration som du har loggat in visas.

2. För att ansluta till Azure Stack-prenumeration, Välj **Lägg till ett konto**.

    ![Lägga till ett Azure Stack-konto](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. I den dialogrutan Anslut till Azure Storage, under **Azure-miljön**väljer **Azure** eller **Azure Kina**, vilken beror på Azure Stack-kontot som används, väljer **Inloggning** att logga in med kontot Azure Stack som är kopplad till minst en aktiv prenumeration i Azure-stacken.

    ![Anslut till Azure Storage](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. När du har loggat in med ett Azure Stack-konto fylls den vänstra rutan i med de Azure Stack-prenumerationer som är kopplade till kontot. Välj de Azure Stack-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du markerar eller avmarkerar kryssrutan **Alla prenumerationer** växlar du mellan att välja alla eller inga av de Azure Stack-prenumerationer som visas.)

    ![Välj Azure Stack-prenumerationer när du har fyllt i dialogrutan Custom Cloud Environment (Anpassad molnmiljö)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    I den vänstra rutan visas de lagringskonton som är kopplade till de valda Azure Stack-prenumerationerna.

    ![Lista med lagringskonton inklusive konton för Azure Stack-prenumerationer](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Ansluta till ett Azure-stacken storage-konto

Du kan också ansluta till en Stack för Azure storage-konto med hjälp av lagringskontonamn och en nyckel.

1. Välj Hantera konton i den vänstra rutan i Lagringsutforskaren. Alla Microsoft-konton som du har loggat in visas.

    ![Lägga till ett konto](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. För att ansluta till Azure Stack-prenumeration, Välj **Lägg till ett konto**.

    ![Lägga till ett konto](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. I fönstret Anslut till Azure Storage-dialogrutan Välj **använder ett lagringskontonamn och nyckel**.

4. Ange namnet på ditt konto i den **kontonamn**, klistra in kontonyckel i den **kontonyckel** text markerar **andra (Ange nedan)** i **lagring slutpunkter domän** och ange Azure Stack-slutpunkten.

    En slutpunkt för Azure-stacken innehåller två delar: namnet på en region och Azure Stack-domänen. I Azure-stacken Development Kit är standardslutpunkten **local.azurestack.external**. Kontakta molnadministratören och om du inte vet om slutpunkten.

    ![Koppla namn och nyckel](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Välj **Anslut**.
6. När lagringskontot har kopplats storage-konto visas med (**externa, andra**) i slutet av namnet.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Lagringsutforskaren](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack-lagring: skillnader och överväganden](azure-stack-acs-differences.md)
* Läs mer om Azure storage i [introduktion till Microsoft Azure storage](../../storage/common/storage-introduction.md)
