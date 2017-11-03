---
title: Installera Visual Studio och ansluta till Azure-stacken | Microsoft Docs
description: "Läs de steg som krävs för att installera Visual Studio och ansluta till Azure-stacken"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 5487125095f05b2fbfa9489c5b4733f61c0212d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installera Visual Studio och ansluta till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använda Visual Studio för att skapa och distribuera Azure Resource Manager [mallar](user/azure-stack-arm-templates.md) i Azure-stacken. Du kan använda stegen som beskrivs i den här artikeln för att installera Visual Studio från [Azure Stack Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är ansluten via [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). De här stegen utför en ny installation av Visual Studio 2015 Community Edition. Läs mer om [samexistens](https://msdn.microsoft.com/library/ms246609.aspx) mellan andra versioner av Visual Studio.

## <a name="install-visual-studio"></a>Installera Visual Studio
1. Hämta och kör den [installationsprogram för webbplattform](https://www.microsoft.com/web/downloads/platform.aspx).             
2. Sök efter **Visual Studio Community 2015 med Microsoft Azure SDK - 2.9.6**, klickar du på **Lägg till**, och **installera**.

    ![Skärmbild av WebPI installera steg](./media/azure-stack-install-visual-studio/image1.png) 

3. Avinstallera den **Microsoft Azure PowerShell** som installeras som en del av Azure SDK.

    ![Skärmbild av Lägg till/ta bort program gränssnitt för Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png) 

4. [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)

5. Starta om operativsystemet när installationen är klar.

## <a name="connect-to-azure-stack"></a>Anslut till Azure Stack

1. Starta Visual Studio.

2. Från den **visa** väljer du **Cloud Explorer**.

3. Välj i rutan nytt **Lägg till konto** och logga in med dina autentiseringsuppgifter för Azure Active Directory.  
    ![Skärmbild av Cloud Explorer loggade in en gång och ansluten till Azure-stacken](./media/azure-stack-install-visual-studio/image6.png)

När du loggade in kan du [distribuera mallar](user/azure-stack-deploy-template-visual-studio.md) eller bläddra bland tillgängliga resurstyper och resursgrupper för att skapa egna mallar.  

## <a name="next-steps"></a>Nästa steg

 - [Utveckla mallar för Azure-Stack](user/azure-stack-develop-templates.md)
