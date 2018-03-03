---
title: Ansluta Lagringsutforskaren till en Azure-Stack-prenumeration
description: "Lär dig att ansluta Exporer lagring till en Azure-Stack-prenumeration"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: xiaofmao
ms.openlocfilehash: bad4b6b0d829fb68dc25e84406a453071a36476a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Ansluta Lagringsutforskaren till en Azure-Stack-prenumeration

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure Lagringsutforskaren (förhandsversion) är en fristående app som gör det enkelt att arbeta med data i Azure-stacken Storage i Windows, macOS och Linux. Det finns flera verktyg avaialble att flytta data till och från Azure Storage i stacken. Mer information finns i [dataöverföring verktyg för Azure-stacken lagring](azure-stack-storage-transfer.md).

Lär dig hur du ansluter till din Azure-stacken storage-konton med Lagringsutforskaren i den här artikeln. 

Om du inte har installerat Lagringsutforskaren ännu, [hämta](http://www.storageexplorer.com/) och och installera den.

När du ansluter till din prenumeration på Azure-stacken, kan du använda den [Azure Lagringsutforskaren artiklar](../../vs-azure-tools-storage-manage-with-storage-explorer.md) att arbeta med Azure Stack-data. 

## <a name="prepare-an-azure-stack-subscription"></a>Förbereda en prenumeration på Azure-stacken

Du behöver åtkomst till Azure-stacken värddatorn skrivbordet eller en VPN-anslutning för Lagringsutforskaren får åtkomst till stacken för Azure-prenumeration. Information om hur du konfigurerar en VPN-anslutning till Azure Stack finns i [Connect to Azure Stack with VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Anslut till Azure Stack via VPN).

För Azure-stacken Development Kit måste du exportera Azure Stack rotcertifikat.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>Exportera och importera sedan certifikatet för Azure-stacken

1. Öppna `mmc.exe` på en Azure-Stack-värddatorn eller en lokal dator med en VPN-anslutning till Azure-stacken. 

2. I **filen**väljer **Lägg till/ta bort snapin-modulen**, och Lägg sedan till **certifikat** att hantera **mitt användarkonto**.



3. Under **konsolen Root\Certificated (lokal dator) \Trusted Root Certification Authorities\Certificates** hitta **AzureStackSelfSignedRootCert**.

    ![Läsa in rotcertifikatet för Azure Stack via mmc.exe][25]

4. Högerklicka på certifikatet, Välj **alla aktiviteter** > **exportera**, och följ sedan anvisningarna för att exportera certifikat med **Base64-kodad X.509 (. CER)**.  

    Det exporterade certifikatet ska användas i nästa steg.
5. Starta Lagringsutforskaren (förhandsversion), och om du ser den **Anslut till Azure Storage** dialogrutan rutan, avbryta den.

6. På den **redigera** menyn, peka på **SSL-certifikat**, och klicka sedan på **Importera certifikat**. Använd filväljaren till att leta rätt på och öppna certifikatet du exporterade i föregående steg.

    Efter importen uppmanas du att starta om Lagringsutforskaren.

    ![Importera certifikatet till Lagringsutforskaren (förhandsversion)][27]

Du är nu redo att ansluta Lagringsutforskaren till en Azure-Stack-prenumeration.

### <a name="to-connect-an-azure-stack-subscription"></a>Att ansluta en prenumeration på Azure-stacken


1. När Lagringsutforskaren (förhandsversion) startats om väljer du **Redigera**-menyn och ser till att **Target Azure Stack** (Använd Azure Stack som mål) är markerat. Om alternativet inte är markerat markerar du det och startar om Lagringsutforskaren så att ändringen börjar gälla. Denna konfiguration krävs för kompatibilitet med Azure Stack-miljön.

    ![Se till att Target Azure Stack (Använd Azure Stack som mål) är markerat][28]

7. Välj **Hantera konton** i den vänstra rutan.  
    Alla Microsoft-konton som du är inloggad på visas.

8. Om du vill ansluta till Azure Stack-kontot väljer du **Lägg till ett konto**.

    ![Lägga till ett Azure Stack-konto][29]

9. I den **Anslut till Azure Storage** dialogrutan under **Azure-miljön**väljer **Använd Azure Stack miljö**, och klicka sedan på **nästa**.

10. Logga in med Azure Stack-konto som är kopplad till minst en aktiv prenumeration i Azure-stacken genom att fylla i den **logga in på Azure-stacken miljö** dialogrutan.  

    Information om de olika fälten:

    * **Miljönamn**: fältet kan anpassas av användaren.
    * **ARM resurs endpoint** (Slutpunkt för ARM-resurs): exempel på slutpunkter för Azure Resource Manager-resursen:

        * För operatorn i molnet:<br> https://adminmanagement.local.azurestack.external   
        * För klient:<br> https://management.local.azurestack.external
 
    * **Klient-Id**: valfria. Värdet anges bara när du måste ange katalogen.

12. När du har loggat in med ett Azure Stack-konto fylls den vänstra rutan i med de Azure Stack-prenumerationer som är kopplade till kontot. Välj de Azure Stack-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du markerar eller avmarkerar kryssrutan **Alla prenumerationer** växlar du mellan att välja alla eller inga av de Azure Stack-prenumerationer som visas.)

    ![Välj Azure Stack-prenumerationer när du har fyllt i dialogrutan Custom Cloud Environment (Anpassad molnmiljö)][30]  
    I den vänstra rutan visas de lagringskonton som är kopplade till de valda Azure Stack-prenumerationerna.

    ![Lista med lagringskonton inklusive konton för Azure Stack-prenumerationer][31]

## <a name="next-steps"></a>Nästa steg
* [Kom igång med Lagringsutforskaren (förhandsversion)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Storage för stacken: skillnader och överväganden](azure-stack-acs-differences.md)


* Mer information om Azure Storage finns [introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
