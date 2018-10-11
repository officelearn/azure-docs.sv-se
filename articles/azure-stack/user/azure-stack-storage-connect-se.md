---
title: Anslut storage explorer till en Azure Stack-prenumeration eller ett lagringskonto | Microsoft Docs
description: Lär dig att ansluta storage explorer till en Azure Stack-prenumeration
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: ab498a108eba39088aa610188918934dd4ec1d3e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078961"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Anslut storage explorer till en Azure Stack-prenumeration eller ett lagringskonto

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

I den här artikeln lär du dig att ansluta till din Azure Stack-prenumerationer och lagringskonton med storage explorer. Azure storage explorer är en fristående app som gör det enkelt att arbeta med Azure Stack storage-data i Windows, macOS och Linux.

> [!NOTE]  
> Det finns flera tillgängliga verktyg för att flytta data till och från Azure Stack-lagring. Mer information finns i [verktyg för Azure Stack-lagring för överföring av Data](azure-stack-storage-transfer.md).

Om du inte har installerat Lagringsutforskaren ännu, [ladda ned Lagringsutforskaren](http://www.storageexplorer.com/) och installera den.

När du ansluter till en Azure Stack-prenumeration eller ett lagringskonto kan du använda den [Azure storage explorer artiklar](../../vs-azure-tools-storage-manage-with-storage-explorer.md) att arbeta med Azure Stack-data. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Förbereda för att ansluta till Azure Stack

Du behöver direkt åtkomst till Azure Stack eller en VPN-anslutning så att Lagringsutforskaren kan komma åt Azure Stack-prenumerationen. Information om hur du konfigurerar en VPN-anslutning till Azure Stack finns i [Connect to Azure Stack with VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Anslut till Azure Stack via VPN).

Du måste exportera rotcertifikatet för Azure Stack-behörighet för Azure Stack Development Kit.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportera och importera sedan certifikatet för Azure Stack

1. Öppna `mmc.exe` på en värddator för Azure Stack eller en lokal dator med en VPN-anslutning till Azure Stack. 

2. I **filen**väljer **Lägg till/ta bort snapin-modulen**, och Lägg sedan till **certifikat** att hantera **mitt användarkonto**.

3. Under **konsolen \certificated (lokal dator) \Trusted Root Certification Authorities\Certificates** hitta **AzureStackSelfSignedRootCert**.

    ![Läsa in rotcertifikatet för Azure Stack via mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Högerklicka på certifikatet, Välj **alla uppgifter** > **exportera**, och följ sedan anvisningarna för att exportera certifikatet med **Base 64-kodad X.509 (. CER)**.

    Det exporterade certifikatet ska användas i nästa steg.

5. Startar storage explorer och om du ser den **Anslut till Azure Storage** dialogrutan rutan, avbryter du den.

6. På den **redigera** menyn, peka på **SSL-certifikat**, och välj sedan **Importera certifikat**. Använd filväljaren till att leta rätt på och öppna certifikatet du exporterade i föregående steg.

    När du har importerat certifikatet, uppmanas du att starta om Lagringsutforskaren.

    ![Importera certifikatet till Lagringsutforskaren](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. När Lagringsutforskaren har startats om väljer du den **redigera** menyn och kontrollera om **Target Azure Stack** har valts. Om det inte finns väljer **Target Azure Stack**, och sedan starta om Lagringsutforskaren så att ändringen ska börja gälla. Denna konfiguration krävs för kompatibilitet med Azure Stack-miljön.

    ![Se till att Target Azure Stack (Använd Azure Stack som mål) är markerat](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Ansluta till en Azure Stack-prenumeration med Azure AD

Använd följande steg för att Anslut storage explorer till en prenumeration på Azure Stack, som hör till ett Azure Active Directory (Azure AD)-konto.

1. I den vänstra rutan i Lagringsutforskaren väljer **hantera konton**. 
    Alla Microsoft-prenumeration som du har loggat in visas.

2. Om du vill ansluta till Azure Stack-prenumerationen, Välj **Lägg till ett konto**.

    ![Lägga till ett Azure Stack-konto](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. I Anslut till Azure Storage dialogrutan under **Azure-miljön**väljer **Azure** eller **Azure Kina**, beroende på Azure Stack-konto som används, Välj **Logga in** att logga in med Azure Stack-konto som är associerad med minst en aktiv Stack Azure-prenumeration.

    ![Anslut till Azure Storage](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. När du har loggat in med ett Azure Stack-konto fylls den vänstra rutan i med de Azure Stack-prenumerationer som är kopplade till kontot. Välj de Azure Stack-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du markerar eller avmarkerar kryssrutan **Alla prenumerationer** växlar du mellan att välja alla eller inga av de Azure Stack-prenumerationer som visas.)

    ![Välj Azure Stack-prenumerationer när du har fyllt i dialogrutan Custom Cloud Environment (Anpassad molnmiljö)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    I den vänstra rutan visas de lagringskonton som är kopplade till de valda Azure Stack-prenumerationerna.

    ![Lista med lagringskonton inklusive konton för Azure Stack-prenumerationer](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Ansluta till en Azure Stack-prenumeration med AD FS-konto

> [!Note]  
> Inloggning för Azure Federated Service (AD FS) stöder Lagringsutforskaren 1.2.0 eller senare versioner med Azure Stack 1804 eller nyare update.
Använd följande steg för att ansluta storage explorer till en Azure Stack-prenumeration som tillhör en AD FS-konto.

1. Välj **hantera konton**. I Utforskaren visar en lista över de Microsoft-prenumerationer som du har loggat in på.
2. Välj **Lägg till ett konto** att ansluta till Azure Stack-prenumerationen.

    ![Lägga till ett konto](media/azure-stack-storage-connect-se/add-an-account.png)

3. Välj **Nästa**. I Anslut till Azure Storage dialogrutan under **Azure-miljön**väljer **Använd anpassad miljö**, klicka sedan på **nästa**.

    ![Anslut till Azure Storage](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Ange informationen som krävs av anpassade Azure Stack-miljön. 

    | Fält | Anteckningar |
    | ---   | ---   |
    | Miljönamn | Fältet kan anpassas av användaren. |
    | Azure Resource Manager-slutpunkt | Exempel på slutpunkter för Azure Resource Manager-resurs av Azure Stack Development Kit.<br>Operatörer: https://adminmanagement.local.azurestack.external <br> För användare: https://management.local.azurestack.external |

    Om du arbetar i Azure Stack-integrerat system och inte vet hanteringsslutpunkten, kontakta din operatör.

    ![Lägga till ett konto](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Välj **logga in**, för att ansluta till Azure Stack-konto som är associerad med minst en aktiv Stack Azure-prenumeration.



6. Välj de Azure Stack-prenumerationer som du vill arbeta med. Välj **Använd**.

    ![Kontohantering](./media/azure-stack-storage-connect-se/account-management.png)

    I den vänstra rutan visas de lagringskonton som är kopplade till de valda Azure Stack-prenumerationerna.

    ![Lista över associerade prenumerationer](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Ansluta till ett Azure Stack-lagringskonto

Du kan också ansluta till ett Azure Stack-lagringskonto med hjälp av lagringskontonamn och en nyckel.

1. Välj Hantera konton i den vänstra rutan i Lagringsutforskaren. Alla Microsoft-konton som du har loggat in visas.

    ![Lägga till ett konto](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Om du vill ansluta till Azure Stack-prenumerationen, Välj **Lägg till ett konto**.

    ![Lägga till ett konto](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. I Anslut till Azure Storage-dialogrutan, Välj **använder en lagringskontonamnet och nyckeln**.

4. Ange namnet på ditt konto i den **kontonamn**, klistra in kontonyckeln i den **kontonyckel** textrutan **andra (Ange nedan)** i **Storage slutpunkter domän** och Azure Stack-slutpunkt för indata.

    En slutpunkt i Azure Stack består av två delar: namnet på en region och Azure Stack-domänen. I Azure Stack Development Kit är standardslutpunkten **local.azurestack.external**. Kontakta molnadministratören och om du är osäker på om din slutpunkt.

    ![Bifoga namn och nyckel](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Välj **Anslut**.
6. När lagringskontot har anslutits visas lagringskontot med (**externa, andra**) i slutet av namnet.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Nästa steg

* [Kom igång med storage explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack-lagring: skillnader och överväganden](azure-stack-acs-differences.md)
* Läs mer om Azure storage i [introduktion till Microsoft Azure storage](../../storage/common/storage-introduction.md)
