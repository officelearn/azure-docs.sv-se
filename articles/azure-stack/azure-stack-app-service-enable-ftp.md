---
title: Aktivera FTP i App Service Azure stacken | Microsoft Docs
description: "Steg för att slutföra för att aktivera FTP i App Service på Azure-stacken"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Aktivera FTP i App Service i Azure Stack

När du har distribuerats Apptjänst Azure stacken om du vill aktivera FTP-publicering, så att klienterna kan överföra sina filer och innehåll, finns det några ytterligare steg som måste slutföras.  I kommande versioner automatiskt de här stegen.

> [!NOTE]
> Anvisningarna gäller för tjänsten eller Företagsadministratörer konfigurerar en Apptjänst på Azure-stacken Resource Provider.

## <a name="enable-ftp"></a>Aktivera FTP

1.  Logga in på Azure Stack-portal som tjänstadministratör.
2.  Bläddra till **nätverksgränssnitt** och välj den **FTP-NIC** under **resursgruppen** - **Apptjänst-lokala**. ![Azure-stacken nätverksgränssnitt][1]
3.  Observera den **offentliga IP-adressen** av den **FTP-NIC**. 
![Azure Network Interface Stackinformation][2]
4.  Bläddra sedan till **virtuella datorer** och välj den **FTP0 VM**. ![Azure-stacken virtuella datorer][3]
5.  Öppnar en fjärrskrivbordssession till en virtuell dator med hjälp av den **Anslut** knappen och logga in till sessionen med administratörsbehörighet som du anger under App Service-distributionen.  
![Azure Stackinformation för virtuell dator][4]
6.  Öppna **Internet Information Service (IIS) Manager** på FTP-VM (FTP0 VM).
7.  Under **platser** Välj **värd för FTP-platsen**.
8.  Öppna **FTP-brandväggsstöd**. ![IIS-hanteraren på Apptjänst FTP0-VM][5]
9.  Ange den offentliga IP-adressen i FTP-NIC och klickar på **tillämpa** ![IIS Manager FTP-brandväggsstöd][6]

## <a name="validate-the-enabling-of-ftp"></a>Verifiera att aktivera FTP

1.  Logga in som tjänstadministratör eller som en klient Azure Stack-portalen.
2.  Bläddra till **Apptjänster** och välj en webbplats, Mobil eller API-App du har skapat. ![App Services][7]
3.  I programmet information noteringen den **FTP-värdnamn** och **användarnamn för FTP-/ distributionsanvändare**. ![Information om App Service appar][8]
> [!NOTE]
> Om du inte ser en post under **användarnamn för FTP-/ distributionsanvändare**, måste du ange de autentiseringsuppgifter första med den **autentiseringsuppgifter för distribution** bladet.

4.  Öppna Windows Explorer, ange FTP-värdnamnet i filen adressfältet exempelvis ftp://ftp.appservice.azurestack.local
5.  När du uppmanas ange den **distributionsbehörigheterna** du antecknade i steg 3, om funktionen har aktiverats visas en lista över app-tjänstprogrammet innehållet. ![FTP-fil som innehåller][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
