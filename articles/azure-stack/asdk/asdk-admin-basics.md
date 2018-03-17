---
title: Azure-stacken Development Kit grunderna | Microsoft Docs
description: "Beskriver hur du utför grundläggande administration av Azure-stacken Development Kit."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: cb169c2d2a5aa918fb6d330ebc4677d6c16d308d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="asdk-administration-basics"></a>ASDK administration grunderna 
Det finns flera saker du behöver veta om du har använt Azure Stack Development Kit (ASDK) administration. Den här vägledningen ger en översikt över din roll som operatör Azure Stack i miljön utvärdering och hur du kontrollerar din testanvändare kan snabbt blir mer produktiv.

Först bör du granska den [vad är Azure Stack Development Kit?](asdk-what-is.md) artikel för att kontrollera att du förstå syftet med ASDK och dess begränsningar. Du bör använda development kit som en ”sandbox”, där du kan utvärdera Azure stackutrymme för att utveckla och testa dina appar i en produktionsmiljö. 

Som Azure, Azure Stack bryter ny mark snabbt så släpper vi regelbundet nya versioner av ASDK. Du kan uppgradera ASDK som du kan Azure Stack integrerat system-distributioner. Så om du vill flytta till den senaste versionen, måste du helt [omdistribuera ASDK](asdk-redeploy.md). Du kan inte använda uppdateringspaket. Den här processen tar tid, men fördelen är att du kan prova att använda de senaste funktionerna så snart de blir tillgängliga. 

## <a name="what-tools-do-i-use-to-manage"></a>Vilka verktyg använda för att hantera?
Du kan använda den [Azure Stack-Administratörsportalen](https://adminportal.local.azurestack.external) eller PowerShell för att hantera Azure-stacken. Det enklaste sättet att lära dig grunderna är via portalen. Om du vill använda PowerShell måste du installera [PowerShell för Azure-stacken](asdk-post-deploy.md#install-azure-stack-powershell) och [hämtar Azure Stack-verktyg från GitHub](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure-stacken använder Azure Resource Manager som dess underliggande mekanism för distribution, hantering och organisation. Om du ska hantera Azure-stacken och stöd för användare, bör du lär dig mer om Azure Resource Manager. Du kan lära dig mer genom att läsa den [komma igång med Azure Resource Manager whitepaper](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Dina vanliga ansvarsområden
Dina användare vill använda tjänster. Din huvudsakliga roll är att tjänsterna är tillgängliga för dem från sina perspektiv. Med ASDK kan du lära dig vilka tjänster att erbjuda och hur du gör de tjänster genom [skapa planer, erbjudanden och kvoter](asdk-offer-services.md). Du måste också lägga till objekt i marketplace, till exempel avbildningar av virtuella datorer. Det enklaste sättet är att [hämta marketplace-objekt](asdk-marketplace-item.md) från Azure till Azure-stacken.

> [!NOTE]
> Om du vill testa dina planer, erbjudanden och tjänster, bör du använda den [användarportalen](https://portal.local.azurestack.external); inte den [administratörsportal](https://adminportal.local.azurestack.external).

Förutom att tillhandahålla tjänster, måste du utföra alla vanliga uppgifter för en Azure-stacken Operator att hålla ASDK igång. Dessa uppgifter omfattar bland annat följande:
- Lägga till användarkonton för Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) distributioner.
- Tilldela roller för åtkomstkontroll (RBAC) (detta inte är begränsade till bara administratörer) för rollbaserad åtkomst
- Övervaka infrastrukturen hälsa
- Hantera nätverk och lagringsresurser
- Ersätt misslyckade development kit värden datormaskinvara 

## <a name="where-to-get-support"></a>Var du kan få support
För i development kit är ditt enda supportalternativ be supporten-relaterade frågor i den [forum för Microsoft Azure-stacken](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Om du klickar du på ikonen Hjälp och support (frågetecken) i övre högra hörnet i administratörsportalen och klicka sedan på **ny supportbegäran**, forum plats öppnas direkt. Dessa forum övervakas regelbundet. 

> [!IMPORTANT]
> Eftersom ASDK är en utvärderingsmiljö, stöds inte officiellt erbjuds via Microsoft kundservice (CSS).

## <a name="next-steps"></a>Nästa steg
[Distribuera ASDK](asdk-deploy.md)

