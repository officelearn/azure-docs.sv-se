---
title: Förstå säkerhetskontroller för Azure Stack | Microsoft Docs
description: Lär dig om de säkerhetskontroller som tillämpas på Azure Stack som en tjänstadministratör
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: mabrigg
ms.openlocfilehash: a3bd314a1df3c45c76b2e3a5acb31c1474d0fdf5
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008834"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Säkerhetspositionen för Azure Stack-infrastruktur

*Gäller för: integrerade Azure Stack-system*

Säkerhetsöverväganden och kompatibilitetsföreskrifter är bland de viktigaste drivrutinerna för att använda hybridmoln. Azure Stack har utformats för dessa scenarier, och det är viktigt att förstå kontroller redan på plats när Azure Stack.

Två säkerhetslager hållning samexistera i Azure Stack. Det första lagret är Azure Stack-infrastrukturen, vilken omfattar maskinvarukomponenter upp till Azure Resource Manager. Det första lagret innehåller administratören och klient-portaler. Det andra lagret består av arbetsbelastningar skapas, distribueras och hanteras av klienter. Det andra lagret innehåller sådant som virtuella datorer och webbplatser i App Services.

## <a name="security-approach"></a>Metod för säkerhet

Säkerhetspositionen för Azure Stack är utformat för att skydda mot moderna hot och byggdes för att uppfylla kraven från större efterlevnadsstandarder. Därför bygger säkerhetspositionen för Azure Stack-infrastruktur på två pelare:

 - **Förmoda överträdelse**  
Från och med antagandet att systemet redan har skett, fokusera på *identifiera och begränsa effekten av överträdelser* jämfört med endast försök att förhindra attacker. 
 - **Bättre skydd som standard**  
Eftersom infrastrukturen körs på väldefinierad maskinvara och programvara, Azure Stack *aktiverar, konfigurerar och verifierar alla säkerhetsfunktioner* som standard.

Eftersom Azure Stack levereras som ett integrerat system, har säkerhetspositionen för Azure Stack-infrastruktur definierats av Microsoft. Precis som i Azure ansvarar klienter för att definiera säkerhetspositionen för sina klienternas arbetsbelastningar. Det här dokumentet innehåller grundläggande kunskaper om säkerhetspositionen för Azure Stack-infrastruktur.

## <a name="data-at-rest-encryption"></a>Data för kryptering av vilande data
Alla Azure Stack-infrastruktur- och klienttrafik data krypteras i vila med Bitlocker. Den här krypteringen skyddar mot fysisk förlust eller stöld av Azure Stack lagringskomponenter. 

## <a name="data-in-transit-encryption"></a>Data i rörelse kryptering
Azure Stack-infrastrukturkomponenter kommunicera med kanaler som krypterats med TLS 1.2. Krypteringscertifikat hanteras själva av infrastrukturen. 

Alla externa infrastrukturslutpunkter, till exempel REST-slutpunkter eller Azure Stack-portalen stöder TLS 1.2 för säker kommunikation. Krypteringscertifikat, antingen från en tredje part eller ditt företag certifikatutfärdare, måste anges för dessa slutpunkter. 

Självsignerade certifikat kan användas för dessa externa slutpunkter, Microsoft rekommenderar att du inte använder dem. 

## <a name="secret-management"></a>Hemlighetshantering
Azure Stack-infrastruktur använder en mängd olika hemligheter som lösenord, för att fungera. De flesta av dem roteras automatiskt ofta, eftersom de är gruppen hanterade tjänstkonton, som rotera var 24: e timme.

De återstående hemligheter som inte är gruppen hanterade tjänstkonton kan roteras manuellt med ett skript i den privilegierade slutpunkten.

## <a name="code-integrity"></a>Kodintegritet
Azure Stack gör användning av den senaste Windows Server 2016 säkerhetsfunktioner. En av dem är Windows Defender Device Guard, som innehåller listan över tillåtna program och säkerställer att endast behöriga koden körs i Azure Stack-infrastruktur. 

Auktoriserade kod är signerat av Microsoft eller OEM-partner och den ingår i en lista över tillåtna program som anges i en princip som definierats av Microsoft. Med andra ord kan bara program som har godkänts för att köras i Azure Stack-infrastruktur köras. Alla försök att köra kod blockeras och en granskningslogg ska genereras.

Device Guard-princip förhindrar också från tredje part för agenter eller programvara som körs i Azure Stack-infrastruktur.

## <a name="credential-guard"></a>Credential Guard
En annan Windows Server 2016-säkerhetsfunktion i Azure Stack är Windows Defender Credential Guard, som används för att skydda autentiseringsuppgifter för Azure Stack-infrastruktur från Pass-the-Hash och Pass-the-Ticket-attacker.

## <a name="antimalware"></a>Programvara mot skadlig kod
Alla komponenter i Azure Stack (Hyper-V-värdar och virtuella datorer) är skyddad med Windows Defender Antivirus.

Definitioner och motor antivirusuppdateringar tillämpas i anslutna scenarier, flera gånger per dag. Program mot skadlig kod uppdateringar tillämpas i frånkopplade lösningar, som en del av den månatliga Azure Stack uppdateringar. Se [uppdatera Windows Defender Antivirus på Azure Stack](azure-stack-security-av.md) för mer information.

## <a name="constrained-administration-model"></a>Begränsad administrationsmodell
Administration i Azure Stack kontrolleras med hjälp av tre startpunkter, var och en med ett visst syfte: 
1. Den [Administratörsportalen](azure-stack-manage-portals.md) tillhandahåller en peka och klicka för dagliga hanteringsåtgärder.
2. Azure Resource Manager visar alla hanteringsåtgärder för Administratörsportalen via ett REST-API som används av PowerShell och Azure CLI. 
3. För specifika på låg nivå, till exempel data center integration eller stöd för scenarier, Azure Stack Exponerar en PowerShell-slutpunkt som anropas [privilegierad slutpunkt](azure-stack-privileged-endpoint.md). Den här slutpunkten visar endast en godkänd uppsättning cmdlets och granskas kraftigt.

## <a name="network-controls"></a>Nätverkskontroller
Azure Stack-infrastruktur levereras med flera lager av network Access Control List(ACL). ACL: er förhindra obehörig åtkomst till infrastrukturkomponenter och begränsa infrastruktur kommunikation till de sökvägar som krävs för dess funktion. 

ACL: er för nätverket tillämpas i tre lager:
1.  Tor-växlar
2.  Programvarudefinierade nätverk
3.  Värden och Virtuella operativsystem brandväggar

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du rotera dina hemligheter i Azure Stack](azure-stack-rotate-secrets.md)
