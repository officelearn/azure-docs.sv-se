---
title: "Förstå Azure Stack säkerhetsåtgärder | Microsoft Docs"
description: "Som tjänstadministratör Lär dig mer om de säkerhetskontroller som tillämpas på Azure-stacken"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1d92f8f2ed9e8ab504afc65bab861e1f7bb3689
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure säkerhetstillståndet för Stack-infrastruktur

*Gäller för: Azure Stack integrerat system*

Säkerhetsaspekter och förordningar är bland huvudsakliga drivrutiner för användning av hybrid moln. Azure-stacken har utformats för dessa scenarier och det är viktigt att förstå kontroller redan på plats vid införandet av Azure-stacken.

Det finns två hållningsdata säkerhetsskikt samexisterar i Azure stacken. Det första lagret omfattar Azure Stack-infrastrukturen som går mellan maskinvarukomponenter ända fram till Azure Resource Manager och innehåller administratören och klient-portaler. Det andra lagret består av arbetsbelastningarna som klienter skapa, distribuera och hantera och innehåller sådant som virtuella datorer eller Apptjänster-webbplatser.  

## <a name="security-approach"></a>Säkerhet, metod
Azure-stacken med en säkerhetstillståndet har utformats för att skydda mot aktuella hot och har skapats för att uppfylla kraven från större efterlevnadsstandarder. Säkerhetstillståndet Azure Stack-infrastrukturen är därför bygger på två pelare:

 - **Anta intrång.**  
Från antagandet att systemet har redan har utsatts för intrång fokusera på *identifiera och begränsa effekten av överträdelser* jämfört med endast försök att förhindra angrepp. 
 - **Härdat som standard.**  
Eftersom infrastrukturen som körs på väldefinierade maskinvara och programvara, vi *aktivera, konfigurera och verifiera alla säkerhetsfunktioner* som standard.



Eftersom Azure Stack skickas som ett integrerat system har säkerhetstillståndet Azure Stack-infrastrukturen definierats av Microsoft. Precis som i Azure ansvarar hyresgäster för att definiera säkerhetstillståndet av deras klienternas arbetsbelastningar. Det här dokumentet innehåller grundläggande kunskaper om säkerhetstillståndet Azure Stack-infrastrukturen.

## <a name="data-at-rest-encryption"></a>Data för kryptering av vilande data
Alla Azure-stacken infrastruktur- och data krypteras vilande med Bitlocker. Denna kryptering skyddar mot fysisk förlust eller stöld av Azure-stacken lagringskomponenter. 

## <a name="data-in-transit-encryption"></a>Data i överföringen kryptering
Azure-stacken infrastrukturkomponenter kommunicerar med kanaler som krypterats med TLS 1.2. Krypteringscertifikat hanteras själva av infrastrukturen. 

Alla externa infrastruktur slutpunkter, till exempel REST-slutpunkter eller Azure Stack-portal stöder TLS 1.2 för säker kommunikation. Krypteringscertifikat antingen från en tredje part eller ditt företag certifikatutfärdare, måste anges för dessa slutpunkter. 

Medan självsignerade certifikat kan användas för dessa externa slutpunkter, Microsoft rekommenderar att du inte använder dem. 

## <a name="secret-management"></a>Hemlig management
Azure Stack-infrastrukturen använder en mängd olika hemligheter, t.ex. lösenord, för att fungera. De flesta roteras automatiskt ofta eftersom de är grupp hanterade tjänstkonton, som rotera var 24: e timme.

De återstående hemligheter som inte är grupp hanterade tjänstkonton kan roteras manuellt med ett skript i Privilegierade slutpunkten.

## <a name="code-integrity"></a>Kodintegritet
Azure-stacken är användning av den senaste Windows Server 2016 säkerhetsfunktioner. En av dem är Windows Defender Device Guard, som ger vitlistning av program och säkerställer att endast behöriga koden körs i Azure Stack-infrastruktur. 

Auktoriserade koden är signerat av Microsoft eller OEM-partner och den ingår i listan över tillåtna program som anges i en princip som definierats av Microsoft. Med andra ord kan bara program som har godkänts för att köras i Azure Stack-infrastruktur köras. Alla försök att köra kod blockeras och ett revisionsstatusmeddelande skapas.

Principen Device Guard förhindrar också från tredje part för agenter eller programvara körs i Azure Stack-infrastruktur.

## <a name="credential-guard"></a>Credential Guard
En annan Windows Server 2016 säkerhetsfunktion i Azure-stacken är Windows Defender autentiseringsuppgifter Guard som används för att skydda Azure Stack infrastruktur autentiseringsuppgifterna från Pass-the-Hash- och Pass-the-Ticket-attacker.

## <a name="antimalware"></a>Programvara mot skadlig kod
Varje komponent i Azure-stacken (Hyper-V-värdar och virtuella datorer) skyddas med Windows Defender antivirusprogram.

## <a name="constrained-administration-model"></a>Begränsad administrationsmodell
Administration i Azure-stacken kontrolleras genom att använda tre startpunkter, var och en med ett specifikt syfte: 
1. Den [Administratörsportal](azure-stack-manage-portals.md) tillhandahåller en plats och klicka på för dagliga hanteringsåtgärder.
2. Azure Resource Manager visar vilka hanteringsåtgärder av Administratörsportalen via ett REST-API som används av PowerShell och Azure CLI. 
3. För specifika låg nivå, till exempel data center integration eller stöd för scenarier, Azure Stack Exponerar en PowerShell-slutpunkt som kallas [Privilegierade Endpoint](azure-stack-privileged-endpoint.md). Den här slutpunkten visar endast en godkända uppsättning cmdlets och granskas kraftigt.

## <a name="network-controls"></a>Nätverkskontroller
Azure Stack-infrastrukturen har flera lager för network Access Control List(ACL). ACL: erna förhindra obehörig åtkomst till infrastrukturkomponenter och begränsa infrastruktur kommunikation till av sökvägar som krävs för dess funktion. 

ACL: er för nätverket tillämpas i tre lager:
1.  Överkant Rack växlar
2.  Programvarudefinierade nätverk
3.  Värden och Virtuella operativsystem brandväggar 


