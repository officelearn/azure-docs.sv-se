---
title: Azure Stack Development Kit grunderna | Microsoft Docs
description: Beskriver hur du utför grundläggande administrationsuppgifter för Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9a07a829aac9659ac7ab8d04b64a1ea1a9a2de78
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428066"
---
# <a name="asdk-administration-basics"></a>Grundläggande om administration av ASDK 
Det finns flera saker du behöver veta om du inte har använt Azure Stack Development Kit (ASDK) administration. Den här vägledningen innehåller en översikt över din roll som Azure Stack-operatör i utvecklingsmiljö och kontrollera din testanvändare kan vara produktiva snabbt.

Först bör du granska den [vad är Azure Stack Development Kit?](asdk-what-is.md) artikeln om du vill kontrollera att du förstår syftet med ASDK och dess begränsningar. Du bör använda i development kit som en ”sandlåda”, där du kan utvärdera Azure Stack för att utveckla och testa dina appar i en produktionsmiljö. 

Som Azure, Azure Stack bryter ny mark snabbt så släpper vi regelbundet nya versioner av ASDK. Men kan inte du uppgradera ASDK som du integrerade Azure Stack-system distributioner. Så om du vill flytta till den senaste versionen, måste du helt [distribuera om ASDK](asdk-redeploy.md). Du kan inte använda uppdateringspaket. Den här processen tar tid, men fördelen är att du kan prova de senaste funktionerna så fort de blir tillgängliga. 

## <a name="what-account-should-i-use"></a>Vilket konto ska jag använda?
Det finns några överväganden för användarkonton som du bör känna till när du hanterar Azure Stack. Särskilt i distributioner med Windows Server Active Directory Federation Services (AD FS) som identitetsleverantör i stället för Azure Active Directory (AD Azure). Följande gäller konto både integrerade Azure Stack-system och ASDK distributioner:

|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Lokal administratör (. \administratör)|ASDK värd administratör|ASDK värd administratör|
|AzureStack\AzureStackAdmin|ASDK värd administratör<br><br>Kan användas för att logga in på administrationsportalen för Azure Stack<br><br>Åtkomst till se och administrera Service Fabric-ringar|ASDK värd administratör<br><br>Ingen åtkomst till Azure Stack-administrationsportalen<br><br>Åtkomst till se och administrera Service Fabric-ringar<br><br>Inte längre ägare av standard providern prenumeration (DPS)|
|AzureStack\CloudAdmin|Kan komma åt och kör tillåtna kommandon inom den privilegierad slutpunkt|Kan komma åt och kör tillåtna kommandon inom den privilegierad slutpunkt<br><br>Det går inte att logga in på ASDK värden<br><br>Ägaren av providern Standardprenumeration (DPS)|
|Azure AD Global Administrator|Används under installationen<br><br>Ägaren av providern Standardprenumeration (DPS)|Inte tillämpligt|
|

## <a name="what-tools-do-i-use-to-manage"></a>Vilka verktyg kan jag använda för att hantera?
Du kan använda den [Azure Stack-Administratörsportalen](https://adminportal.local.azurestack.external) eller PowerShell för att hantera Azure Stack. Det enklaste sättet att lära dig de grundläggande principerna är via portalen. Om du vill använda PowerShell måste du installera [PowerShell för Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) och [ladda ned Azure Stack-verktyg från GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure Stack använder Azure Resource Manager som dess underliggande mekanism för distribution, hantering och organisation. Om du planerar att hantera Azure Stack och att ge stöd för användare, bör du lära dig om Azure Resource Manager. Du kan lära dig mer genom att läsa den [komma igång med Azure Resource Manager White Paper](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Dina vanliga ansvarsområden
Dina användare vill använda tjänster. Deras ur är dina viktigaste roll att gör de här tjänsterna tillgängliga för dem. Använd ASDK och du kan lära dig vilka tjänster som erbjuder och göra dessa tjänster genom [skapa planer, erbjudanden och kvoter](asdk-offer-services.md). Du måste också lägga till objekt i marketplace, till exempel avbildningar av virtuella datorer. Det enklaste sättet är att [hämta marketplace-objekt](asdk-marketplace-item.md) från Azure till Azure Stack.

> [!NOTE]
> Om du vill testa dina planer, erbjudanden och tjänster, bör du använda den [användarportalen](https://portal.local.azurestack.external); inte den [administratörsportalen](https://adminportal.local.azurestack.external).

Förutom att tillhandahålla tjänster, måste du utföra alla vanliga uppgifter för en Azure Stack-Operator att hålla ASDK igång. Dessa uppgifter omfattar bland annat följande:
- Lägga till användarkonton för Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS)-distributioner.
- Tilldela roller för åtkomstkontroll (RBAC) (detta inte är begränsade till bara administratörer) för rollbaserad åtkomst
- Övervaka tillståndet i infrastrukturen
- Hantera nätverk och lagringsresurser
- Ersätt misslyckade development kit värd datormaskinvara 

## <a name="where-to-get-support"></a>Var du kan få support
För development kit är ditt enda supportalternativ ställa support-relaterade frågor i den [Microsoft Azure Stack-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Om du klickar du på ikonen Hjälp och support (frågetecken) i det övre högra hörnet av administratörsportalen och klicka sedan på **ny supportbegäran**, öppnas på forumwebbplatsen direkt. Dessa forum övervakas regelbundet. 

> [!IMPORTANT]
> Eftersom ASDK är en utvecklingsmiljö, det finns inga officiella stöd som erbjuds via Microsofts kundsupport (CSS).

## <a name="next-steps"></a>Nästa steg
[Distribuera ASDK](asdk-install.md)

