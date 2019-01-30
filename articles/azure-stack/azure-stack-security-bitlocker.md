---
title: Data för kryptering av vilande data i Azure Stack
description: Lär dig hur du skyddar dina data med kryptering i vila för Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/06/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 12/06/2018
keywords: ''
ms.openlocfilehash: 563534424cad7f3e02be07ad582be0b3966950b4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55237990"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Data för kryptering av vilande data i Azure Stack

Azure Stack skyddar användar- och infrastruktur på undersystemet lagringsnivå med kryptering i vila. Azure Stack-underlagringssystem har krypterats med BitLocker med 128-bitars AES-kryptering. BitLocker-nycklar finns kvar i en intern Arkiv.

Data för kryptering av vilande data är ett vanligt krav för många av de viktiga efterlevnadsstandarder (till exempel PCI-DSS, FedRAMP, HIPAA). Azure Stack kan du uppfylla dessa krav utan extra arbete eller konfigurationer som krävs. Mer information om hur Azure Stack hjälper dig att uppfylla vissa efterlevnadsstandarder, finns i den [Microsoft Service Trust Portal](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Data för kryptering av vilande data skyddar dina data mot som används av någon som fysiskt stjäl en eller flera hårddiskar. Data för kryptering av vilande data skyddar inte mot data som snappas upp över nätverket (data under överföring), som används för tillfället data (data i minnet) eller mer i allmänhet data som exfiltrated medan systemet är igång och körs.

## <a name="retrieving-bitlocker-recovery-keys"></a>Hämta nycklar för BitLocker-återställning

Azure Stack BitLocker-nycklar för vilande data hanteras internt. Du behöver inte ange dem för normal drift eller vid systemstart. Supportscenarier kan dock kräva BitLocker återställningsnycklar att systemet online.  

> [!WARNING]
> Hämta dina nycklar för BitLocker-återställning och lagra dem på en säker plats utanför Azure Stack. Har inte återställningsnycklar under vissa supportscenarier kan resultera i dataförlust och kräver en återställning från en säkerhetskopia.

Hämtning av nycklarna för BitLocker-återställning kräver åtkomst till den [privilegierad slutpunkt](azure-stack-privileged-endpoint.md) (program). En program-session och kör du cmdleten Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Valfria parametrar för *Get-AzsRecoveryKeys* cmdlet:

| Parameter | Beskrivning | Type | Krävs |
|---------|---------|---------|---------|
|*rådata* | Returnerar rådata för mappning mellan återställningsnyckel, datornamn och lösenord ID på varje volym som är krypterad  | Växel | Ingen (utformad för supportscenarier)|


## <a name="troubleshoot-issues"></a>Felsöka problem

I extrema fall en BitLocker-upplåsning via begäran misslyckas, vilket resulterar i en viss volym att inte starta. Beroende på tillgängligheten för vissa komponenter i arkitekturen, kan det här felet orsaka driftstopp och potentiell dataförlust om du inte har dina nycklar för BitLocker-återställning.

> [!WARNING]
> Hämta dina nycklar för BitLocker-återställning och lagra dem på en säker plats utanför Azure Stack. Har inte återställningsnycklar under vissa supportscenarier kan resultera i dataförlust och kräver en återställning från en säkerhetskopia.

Om du misstänker att datorn upplever problem med BitLocker, till exempel Azure Stack misslyckas med att starta, kontakta supporten. Support kräver att dina nycklar för BitLocker-återställning. Merparten av BitLocker-relaterade problem kan lösas med en FRU-åtgärd för specifika VM/värd/volymen. I andra fall måste kan en manuell att låsa upp proceduren med hjälp av nycklar för BitLocker-återställning utföras. Om BitLocker återställningsnycklar inte är tillgängliga är det enda alternativet att återställa från en säkerhetskopia. Beroende på när den senaste säkerhetskopieringen utfördes från, kan det uppstå dataförlust.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Stack-säkerhet](azure-stack-security-foundations.md)
- Mer information om hur BitLocker skyddar CSV: er finns i [skyddar kluster klusterdelade volymer och SAN-nätverk med BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).