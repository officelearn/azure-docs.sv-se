---
title: Installera Visual Studio och Anslut till Azure Stack | Microsoft Docs
description: Läs om stegen som krävs för att installera Visual Studio och ansluta till Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: da17d114c1ffb920fbaae85a6cdcbc35a66631a4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258002"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installera Visual Studio och Anslut till Azure Stack

*Gäller för Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda Visual Studio för att skriva och distribuera Azure Resource Manager [mallar](azure-stack-arm-templates.md) till Azure Stack. Stegen i den här artikeln beskriver hur du installerar Visual Studio på [Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller på en extern dator om du planerar att använda Azure Stack via [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Installera Visual Studio

1. Ladda ned och kör den [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Öppna den **Microsoft Web Platform Installer**.

3. Sök efter **Visual Studio Community 2015 med Microsoft Azure SDK - 2.9.6**. Klicka på **lägga till**, och **installera**.

4. Avinstallera den **Microsoft Azure PowerShell** som installeras som en del av Azure SDK.

    ![Skärmbild av WebPI-installation steg](./media/azure-stack-install-visual-studio/image1.png)

5. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)

6. Starta om operativsystemet när installationen är klar.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Anslut till Azure Stack med Azure AD

1. Starta Visual Studio.

2. Från den **visa** menyn och välj **Cloud Explorer**.

3. I det nya fönstret, väljer **Lägg till konto** och logga in med dina autentiseringsuppgifter för Azure Active Directory (AD Azure).  

    ![Skärmbild av Cloud Explorer loggat in en gång och är ansluten till Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

När du loggat in kan du [distribuera mallar](azure-stack-deploy-template-visual-studio.md) eller bläddra typer av tillgängliga resurser och resursgrupper för att skapa egna mallar.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Anslut till Azure Stack med AD FS

1. Starta Visual Studio.

2. Från **verktyg**väljer **alternativ**.

3. Expandera **miljö** i den **navigeringsfönstret** och välj **konton**.

4. Välj **Lägg till**, och ange användaren Azure Resource Manager-slutpunkt. För Azure Stack Development Kit URL: en är: `https://management.local.azurestack/external`.  Integrerade Azure Stack-system använder URL: en är: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Välj **Lägg till**.  

    Visual Studio anropar Azure Resource Manager och identifierar slutpunkter, inklusive autentiseringsslutpunkten för Azure Directory Federation Services (AD FS).

    ![Skärmbild av Cloud Explorer loggat in en gång och är ansluten till Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Välj **Cloud Explorer** från den **visa** menyn.

7. Välj **Lägg till konto** och logga in med dina autentiseringsuppgifter för AD FS.  

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer frågar efter tillgängliga prenumerationer. Du kan välja en tillgänglig prenumeration för att hantera.

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Bläddra bland dina befintliga resurser, resursgrupper och distribuera mallar.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Visual Studio [sida vid sida](/visualstudio/install/install-visual-studio-versions-side-by-side) med andra versioner av Visual Studio.
- [Utveckla mallar för Azure Stack](azure-stack-develop-templates.md).