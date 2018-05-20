---
title: Installera Visual Studio och ansluta till Azure-stacken | Microsoft Docs
description: Läs de steg som krävs för att installera Visual Studio och ansluta till Azure-stacken
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 3eaefbe011c4d98fe9a76d4f277a76a2f167b191
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installera Visual Studio och ansluta till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använda Visual Studio för att skapa och distribuera Azure Resource Manager [mallar](azure-stack-arm-templates.md) i Azure-stacken. Du kan använda stegen som beskrivs i den här artikeln för att installera Visual Studio från [Azure Stack Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är ansluten via [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). Anvisningarna i den här artikeln gäller för en ny installation av Visual Studio 2015 Community Edition. Läs mer om [samexistens](https://msdn.microsoft.com/library/ms246609.aspx) med andra versioner av Visual Studio.

## <a name="install-visual-studio"></a>Installera Visual Studio

1. Hämta och kör den [installationsprogram för webbplattform](https://www.microsoft.com/web/downloads/platform.aspx).
2. Sök efter **Visual Studio Community 2015 med Microsoft Azure SDK - 2.9.6**väljer **Lägg till**, och välj sedan **installera**.

    ![Skärmdump av WebPI installera steg](./media/azure-stack-install-visual-studio/image1.png)

3. Avinstallera den **Microsoft Azure PowerShell** som installeras som en del av Azure SDK.

    ![Skärmdump av Lägg till/ta bort program gränssnitt för Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png)

4. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)

5. Starta om operativsystemet när installationen är klar.

## <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

1. Starta Visual Studio.

2. Från den **visa** väljer du **Cloud Explorer**.

3. Välj i rutan nytt **Lägg till konto** och logga in med dina autentiseringsuppgifter för Azure Active Directory.
    ![Skärmdump av Cloud Explorer när du loggar in och ansluten till Azure-stacken](./media/azure-stack-install-visual-studio/image6.png)

När du loggade in kan du [distribuera mallar](azure-stack-deploy-template-visual-studio.md) eller bläddra bland tillgängliga resurstyper och resursgrupper för att skapa egna mallar.

## <a name="next-steps"></a>Nästa steg

* [Utveckla mallar för Azure-Stack](azure-stack-develop-templates.md)
