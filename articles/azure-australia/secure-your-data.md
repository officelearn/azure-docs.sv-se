---
title: Data säkerhet i Azure Australien
description: Konfigurera Azure i de australiska regionerna för att uppfylla de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608345"
---
# <a name="data-security-in-azure-australia"></a>Data säkerhet i Azure Australien

De övergripande principerna för att skydda kund information är:

* Skydda data med kryptering
* Hantera hemligheter
* Begränsa data åtkomst

## <a name="encrypting-your-data"></a>Kryptera dina data

Kryptering av data kan appliceras på disk nivå (i vila) i databaser (i vila och under överföring) i program (i överförings läge) och i nätverket (under överföring). Det finns flera sätt att uppnå kryptering i Azure:

|Tjänst/funktion|Beskrivning|
|---|---|
|Kryptering av lagringstjänst|Azure Storage tjänst kryptering aktive ras på lagrings konto nivå, vilket resulterar i att block-blobbar och sid-blobar krypteras automatiskt när de skrivs till Azure Storage. När du läser data från Azure Storage dekrypteras de av lagrings tjänsten innan de returneras. Använd SSE för att skydda dina data utan att behöva ändra eller lägga till kod i några program.|
|Azure Disk Encryption|Använd Azure Disk Encryption för att kryptera de OS-diskar och data diskar som används av en virtuell Azure-dator. Integrering med Azure Key Vault ger dig kontroll och hjälper dig att hantera disk krypterings nycklar.|
|Kryptering på program nivå på klient Sidan|Kryptering på klient sidan är inbyggd i Java-och .NET-lagrings klient biblioteken, som kan använda Azure Key Vault API: er, vilket gör det enkelt att implementera. Använd Azure Key Vault för att få åtkomst till hemligheterna i Azure Key Vault för vissa personer som använder Azure Active Directory.|
|Kryptering under överföring|Den grundläggande krypteringen som är tillgänglig för anslutning till Azure Australien stöder TLS (Transport Level Security) 1,2-protokoll och X. 509-certifikat. Federal Information Processing Standard (FIPS) 140-2 nivå 1-kryptografiska algoritmer används också för infrastruktur nätverks anslutningar mellan Azures Data Center.  Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8,1 och Azure File-resurser kan använda SMB 3,0 för kryptering mellan den virtuella datorn och fil resursen. Använd kryptering på klient sidan för att kryptera data innan de överförs till lagring i ett klient program och för att dekryptera data när de har överförts från lagrings utrymmet.|
|Virtuella IaaS-datorer|Använd Azure Disk Encryption. Aktivera Kryptering för lagringstjänst för att kryptera de VHD-filer som används för att säkerhetskopiera diskarna i Azure Storage, men det krypterar bara nyligen skrivna data. Det innebär att om du skapar en virtuell dator och sedan aktiverar Kryptering för lagringstjänst på det lagrings konto som innehåller VHD-filen, kommer bara ändringarna att krypteras, inte den ursprungliga VHD-filen.|
|Kryptering av klientsidan|Detta är den säkraste metoden för att kryptera dina data, eftersom den krypterar den före överföringen och krypterar data i vila. Det kräver dock att du lägger till kod till dina program med hjälp av lagring, som du kanske inte vill göra. I dessa fall kan du använda HTTPS för dina data i överföring och Kryptering för lagringstjänst att kryptera data i vila. Kryptering på klient sidan omfattar även mer belastning på klienten – du måste ta hänsyn till detta i dina skalbarhets planer, särskilt om du krypterar och överför stora data mängder.|
|

Mer information om krypterings alternativen i Azure finns i [säkerhets guiden för lagring](https://docs.microsoft.com/azure/storage/storage-security-guide).

## <a name="protecting-data-by-managing-secrets"></a>Skydda data genom att hantera hemligheter

Säker nyckel hantering är nödvändig för att skydda data i molnet. Kunderna bör sträva efter att förenkla nyckel hanteringen och upprätthålla kontrollen över nycklar som används av moln program och tjänster för att kryptera data.

### <a name="managing-secrets"></a>Hantera hemligheter

* Använd Key Vault för att minimera risken för att hemligheter exponeras genom hårdkodade konfigurationsfiler, skript eller käll kod. Azure Key Vault krypterar nycklar (till exempel krypterings nycklarna för Azure Disk Encryption) och hemligheter (till exempel lösen ord) genom att lagra dem i FIPS 140-2 nivå 2-validerade HSM: er (Hardware Security modules). För extra trygghet kan du importera eller generera nycklar i dessa HSM: er.
* Program kod och mallar får bara innehålla URI-referenser till hemligheterna (vilket innebär att de faktiska hemligheterna inte finns i kod-, konfigurations-eller käll kods Arkiv). Detta förhindrar viktiga phishing-attacker på interna eller externa databaser, till exempel Harvest-robotar i GitHub.
* Använd starka RBAC-kontroller i Key Vault. Om en betrodd operatör lämnar företaget eller överför till en ny grupp inom företaget, bör de förhindras att få åtkomst till hemligheterna.  

Mer information finns i [Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>Isolering för att begränsa åtkomst till data

Isolering är allt om du använder gränser, segmentering och behållare för att begränsa data åtkomsten till endast behöriga användare, tjänster och program. Separationen mellan klienter är till exempel en grundläggande säkerhetsmekanism för moln plattformar med flera innehavare, till exempel Microsoft Azure. Logisk isolering förhindrar att en klient stör driften av andra klienter.

#### <a name="per-customer-isolation"></a>Isolering per kund

Azure implementerar nätverks åtkomst kontroll och åtskillnad genom Layer 2 VLAN-isolering, åtkomst kontrol listor, belastningsutjämnare och IP-filter.

Kunder kan ytterligare isolera sina resurser över prenumerationer, resurs grupper, virtuella nätverk och undernät.

Mer information om isolering i Microsoft Azure finns i isoleringen [i det offentliga Azure](../security/fundamentals/isolation-choices.md)-molnet.

## <a name="next-steps"></a>Nästa steg

Läs artikeln på [Azure VPN gateway](vpn-gateway.md)