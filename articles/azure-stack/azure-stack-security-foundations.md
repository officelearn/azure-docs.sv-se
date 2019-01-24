---
title: Förstå Azure Stack säkerhetskontroller
description: Lär dig om de säkerhetskontroller som tillämpas på Azure Stack som en tjänstadministratör
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: fba4020c5e947c135d9e17cfb1d0b2a79ad8a502
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819347"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Säkerhetspositionen för Azure Stack-infrastruktur

*Gäller för: Integrerade Azure Stack-system*

Säkerhetsöverväganden och kompatibilitetsföreskrifter är bland de viktigaste drivrutinerna för att använda hybridmoln. Azure Stack är utformat för dessa scenarier. Den här artikeln beskriver säkerhetskontrollerna för Azure Stack.

Två säkerhetslager hållning samexistera i Azure Stack. Det första lagret är Azure Stack-infrastrukturen, vilken omfattar maskinvarukomponenter upp till Azure Resource Manager. Det första lagret innehåller administratören och klient-portaler. Det andra lagret består av arbetsbelastningar skapas, distribueras och hanteras av klienter. Det andra lagret innehåller sådant som virtuella datorer och webbplatser i App Services.

## <a name="security-approach"></a>Metod för säkerhet

Säkerhetspositionen för Azure Stack är utformat för att skydda mot moderna hot och byggdes för att uppfylla kraven från större efterlevnadsstandarder. Därför bygger säkerhetspositionen för Azure Stack-infrastruktur på två pelare:

 - **Förmoda överträdelse**  
Från och med antagandet att systemet redan har skett, fokusera på *identifiera och begränsa effekten av överträdelser* jämfört med endast försök att förhindra attacker. 
 - **Bättre skydd som standard**  
Eftersom infrastrukturen körs på väldefinierad maskinvara och programvara, Azure Stack *aktiverar, konfigurerar och verifierar alla säkerhetsfunktioner* som standard.

Eftersom Azure Stack levereras som ett integrerat system, har säkerhetspositionen för Azure Stack-infrastruktur definierats av Microsoft. Precis som i Azure ansvarar klienter för att definiera säkerhetspositionen för sina klienternas arbetsbelastningar. Det här dokumentet innehåller grundläggande kunskaper om säkerhetspositionen för Azure Stack-infrastruktur.

## <a name="data-at-rest-encryption"></a>Data för kryptering av vilande data
Alla Azure Stack-infrastruktur- och klienttrafik data krypteras i vila med BitLocker. Den här krypteringen skyddar mot fysisk förlust eller stöld av Azure Stack lagringskomponenter. Mer information finns i [data kryptering av vilande data i Azure Stack](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Data i rörelse kryptering
Azure Stack-infrastrukturkomponenter kommunicera med kanaler som krypterats med TLS 1.2. Krypteringscertifikat hanteras själva av infrastrukturen. 

Alla externa infrastrukturslutpunkter, till exempel REST-slutpunkter eller Azure Stack-portalen stöder TLS 1.2 för säker kommunikation. Krypteringscertifikat, antingen från en tredje part eller ditt företag certifikatutfärdare, måste anges för dessa slutpunkter. 

Självsignerade certifikat kan användas för dessa externa slutpunkter, Microsoft rekommenderar att du inte använder dem. 

## <a name="secret-management"></a>Hemlighetshantering
Azure Stack-infrastruktur använder en mängd olika hemligheter som lösenord, för att fungera. De flesta av dem roteras automatiskt ofta, eftersom de är Group-Managed tjänstkonton, som rotera var 24: e timme.

De återstående hemligheter som inte är Group-Managed tjänstkonton kan roteras manuellt med ett skript i den privilegierade slutpunkten.

## <a name="code-integrity"></a>Kodintegritet
Azure Stack gör användning av den senaste Windows Server 2016 säkerhetsfunktioner. En av dem är Windows Defender Device Guard, som innehåller listan över tillåtna program och säkerställer att endast behöriga koden körs i Azure Stack-infrastruktur. 

Auktoriserade kod är signerat av Microsoft eller OEM-partner. Signerade auktoriserade koden ingår i en lista över tillåtna program som anges i en princip som definierats av Microsoft. Med andra ord kan bara program som har godkänts för att köras i Azure Stack-infrastruktur köras. Alla försök att köra kod blockeras och en granskningslogg ska genereras.

Device Guard-princip förhindrar också från tredje part för agenter eller programvara som körs i Azure Stack-infrastruktur.

## <a name="credential-guard"></a>Credential Guard
En annan Windows Server 2016-säkerhetsfunktion i Azure Stack är Windows Defender Credential Guard, som används för att skydda autentiseringsuppgifter för Azure Stack-infrastruktur från Pass-the-Hash och Pass-the-Ticket-attacker.

## <a name="antimalware"></a>Programvara mot skadlig kod
Alla komponenter i Azure Stack (Hyper-V-värdar och virtuella datorer) är skyddad med Windows Defender Antivirus.

Definitioner och motor antivirusuppdateringar tillämpas i anslutna scenarier, flera gånger per dag. Program mot skadlig kod uppdateringar tillämpas i frånkopplade lösningar, som en del av den månatliga Azure Stack uppdateringar. Mer information finns i [uppdatera Windows Defender Antivirus på Azure Stack](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Begränsad administrationsmodell
Administration i Azure Stack styrs via tre startpunkter, var och en med ett visst syfte: 
1. Den [Administratörsportalen](azure-stack-manage-portals.md) tillhandahåller en peka och klicka för dagliga hanteringsåtgärder.
2. Azure Resource Manager visar alla hanteringsåtgärder för Administratörsportalen via ett REST-API som används av PowerShell och Azure CLI. 
3. För specifika på låg nivå, till exempel data center integration eller stöd för scenarier, Azure Stack Exponerar en PowerShell-slutpunkt som anropas [privilegierad slutpunkt](azure-stack-privileged-endpoint.md). Den här slutpunkten visar endast en godkänd uppsättning cmdlets och granskas kraftigt.

## <a name="network-controls"></a>Nätverkskontroller
Azure Stack-infrastruktur levereras med flera lager i nätverket åtkomstkontrollistan (ACL). ACL: er förhindra obehörig åtkomst till infrastrukturkomponenter och begränsa infrastruktur kommunikation till de sökvägar som krävs för dess funktion. 

ACL: er för nätverket tillämpas i tre lager:
1.  Tor-växlar
2.  Programvarudefinierade nätverk
3.  Värden och Virtuella operativsystem brandväggar

## <a name="regulatory-compliance"></a>Regelefterlevnad

Azure Stack har gått igenom en formell utvärdering från tredje part oberoende granskning företaget. Därför finns dokumentation om hur Azure Stack-infrastruktur uppfyller de tillämpliga kontrollerna från flera viktiga efterlevnadsstandarder. Dokumentationen är inte en certifiering av Azure Stack på grund av de standarder inklusive flera personal-relaterade och process-relaterade kontroller. Kunder kan i stället använda den här dokumentationen för att ge sina certifieringsprocessen.

Utvärderingar är följande standarder:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) adresser payment card branschen.
- [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) är en omfattande mappning mellan flera standarder, inklusive FedRAMP måttlig, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 och andra.
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) för myndigheter.

Efterlevnad-dokumentation finns på den [Microsoft Service Trust Portal](https://servicetrust.microsoft.com/ViewPage/Blueprint). Riktlinjerna för efterlevnad är en skyddad resurs och kräver att du logga in med dina Azure cloud service.

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du rotera dina hemligheter i Azure Stack](azure-stack-rotate-secrets.md)
- [PCI-DSS och CSA-CCM-dokument för Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [DoD och NIST dokument för Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
