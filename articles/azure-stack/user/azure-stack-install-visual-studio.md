---
title: Installera Visual Studio och ansluta till Azure-stacken | Microsoft Docs
description: Läs de steg som krävs för att installera Visual Studio och ansluta till Azure-stacken
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.openlocfilehash: cbd5e5dbcdd2565e8066b0721f45863569bfd90a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295038"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installera Visual Studio och ansluta till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda Visual Studio för att skriva och distribuera Azure Resource Manager [mallar](azure-stack-arm-templates.md) till Azure-stacken. Stegen i den här artikeln vägleder dig genom att installera Visual Studio på den [Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller på en extern dator om du planerar att Azure-stacken genom den [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Installera Visual Studio

1. Hämta och kör den [installationsprogram för webbplattform](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Öppna den **Microsoft Web Platform Installer**.

3. Sök efter **Visual Studio Community 2015 med Microsoft Azure SDK - 2.9.6**. Klicka på **lägga till**, och **installera**.

4. Avinstallera den **Microsoft Azure PowerShell** som installeras som en del av Azure SDK.

    ![Skärmbild av WebPI installera steg](./media/azure-stack-install-visual-studio/image1.png) 

5. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)

6. Starta om operativsystemet när installationen är klar.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Ansluta till Azure-stacken med Azure AD

1. Starta Visual Studio.

2. Från den **visa** väljer du **Cloud Explorer**.

3. Välj i rutan nytt **Lägg till konto** och logga in med dina autentiseringsuppgifter för Azure Active Directory (AD Azure).  

    ![Skärmbild av Cloud Explorer loggade in en gång och ansluten till Azure-stacken](./media/azure-stack-install-visual-studio/image2.png)

När du loggade in kan du [distribuera mallar](azure-stack-deploy-template-visual-studio.md) eller bläddra bland tillgängliga resurstyper och resursgrupper för att skapa egna mallar.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Ansluta till Azure-stacken med AD FS

1. Starta Visual Studio.

2. Från **verktyg**väljer **alternativ**.

3. Expandera **miljö** i den **navigeringsfönstret** och välj **konton**.

4. Välj **Lägg till**, och ange användaren Azure Resource Manager-slutpunkten.  
  För Azure-stacken Development kit, URL-Adressen är: `https://management.local.azurestack/external`.  
  För Azure-stacken integrerat system URL: en är: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Välj **Lägg till**.  

    Visual Studio anropar Azure Resource Manager och identifierar slutpunkterna inklusive autentiseringsslutpunkten för Azure Directory Federation Services (AD FS).

    ![Skärmbild av Cloud Explorer loggade in en gång och ansluten till Azure-stacken](./media/azure-stack-install-visual-studio/image6.png)

6. Välj **Cloud Explorer** från den **visa** menyn.
7. Välj **Lägg till konto** och logga in med dina AD FS-autentiseringsuppgifter.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer frågar tillgängliga prenumerationer. Du kan välja ett en tillgänglig prenumeration för att hantera.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. Bläddra ditt befintliga resurser, resursgrupper eller distribuera mallar.

## <a name="next-steps"></a>Nästa steg

 - Läs mer om [samexistens](https://msdn.microsoft.com/library/ms246609.aspx) med andra versioner av Visual Studio.
 - [Utveckla mallar för Azure-Stack](azure-stack-develop-templates.md)