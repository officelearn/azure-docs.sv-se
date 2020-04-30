---
title: Översikt – Azure Files identitets-baserad auktorisering
description: Azure Files stöder identitets-baserad autentisering över SMB (Server Message Block) via Azure Active Directory Domain Services (AD DS) och Active Directory. Dina domänanslutna virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536596"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Översikt över stöd för Azure Files Identity-baserad autentisering för SMB-åtkomst
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Information om hur du aktiverar lokal Active Directory Domain Services autentisering för Azure-filresurser (för hands version) finns i [Aktivera lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md).

Information om hur du aktiverar Azure AD DS-autentisering för Azure-filresurser finns i [aktivera Azure Active Directory Domain Services autentisering på Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Ordlista 
Det är bra att förstå vissa viktiga villkor som rör Azure AD Domain Service-autentisering över SMB för Azure-filresurser:

-   **Kerberos-autentisering**

    Kerberos är ett autentiseringsprotokoll som används för att verifiera en användares eller värds identitet. Mer information om Kerberos finns i [Översikt över Kerberos-autentisering](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **SMB-protokoll (Server Message Block)**

    SMB är ett standardiserat nätverks fil delnings protokoll. SMB kallas även common Internet File System eller CIFS. Mer information om SMB finns i [Översikt över Microsoft SMB-protokoll och CIFS-protokoll](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Azure AD kombinerar kärn katalog tjänster, program åtkomst hantering och identitets skydd i en enda lösning. Azure AD-anslutna virtuella Windows-datorer (VM) kan komma åt Azure-filresurser med dina autentiseringsuppgifter för Azure AD. Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS tillhandahåller hanterade domän tjänster som domän anslutning, grup principer, LDAP och Kerberos/NTLM-autentisering. Dessa tjänster är helt kompatibla med Active Directory Domain Services. Mer information finns i [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Lokala Active Directory Domain Services (AD DS)**

    Den lokala Active Directory Domain Services (AD DS)-integration med Azure Files (förhands granskning) tillhandahåller metoder för att lagra katalog data och göra dem tillgängliga för användare och administratörer i nätverket. Säkerheten är integrerad med AD DS via inloggningsautentisering och åtkomst kontroll till objekt i katalogen. Med en enda nätverks inloggning kan administratörer hantera katalog data och organisationer över hela nätverket och auktoriserade nätverks användare kan komma åt resurser var som helst i nätverket. AD DS antas ofta av företag i lokala miljöer och AD DS-autentiseringsuppgifter används som identitet för åtkomst kontroll. Mer information finns i [Active Directory Domain Services översikt](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Rollbaserad Access Control i Azure (RBAC)**

    Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du hantera åtkomst till resurser genom att ge användarna minst de behörigheter som krävs för att utföra sina jobb. Mer information om RBAC finns i [Vad är rollbaserad åtkomst kontroll (RBAC) i Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Vanliga användarsituationer

Identitetsbaserade autentisering och stöd för Windows ACL: er på Azure Files är bäst för följande användnings fall:

### <a name="replace-on-premises-file-servers"></a>Ersätta lokala fil servrar

Föråldrade och ersättande lokala fil servrar är ett vanligt problem som varje företag stöter på i IT-modernisering resan. Azure-filresurser med lokal AD DS-autentisering (förhands granskning) är den bästa anpassningen här, när du kan migrera data till Azure Files. Med en fullständig migrering kan du dra nytta av fördelarna med hög tillgänglighet och skalbarhet samtidigt som du minimerar ändringar på klient sidan. Det ger en sömlös migrering till slutanvändare, så de kan fortsätta att komma åt sina data med samma autentiseringsuppgifter med hjälp av sina befintliga domänanslutna datorer.

### <a name="lift-and-shift-applications-to-azure"></a>Lyft och växla program till Azure

När du lyfter och växlar program till molnet, vill du behålla samma autentiserings modell för dina data. När vi utökar den identitetsbaserade åtkomst kontrollen till Azure-filresurser, eliminerar den behovet av att ändra programmet till modern auth-metoder och påskynda moln införande. Azure-filresurser ger möjlighet att integrera med antingen Azure AD DS eller lokal AD DS (för hands version) för autentisering. Om din plan ska vara 100% Cloud Native och minimera ansträngningarna att hantera moln infrastrukturer, är Azure AD DS en bättre anpassning som en fullständigt hanterad domän tjänst. Om du behöver fullständig kompatibilitet med AD DS-funktioner kan det vara bra att utöka din AD DS-miljö till molnet med hjälp av självvärdbaserade domänkontrollanter på virtuella datorer. I båda fallen ger vi flexibiliteten att välja de domän tjänster som passar dina affärs behov.

### <a name="backup-and-disaster-recovery-dr"></a>Säkerhets kopiering och haveri beredskap (DR)

Om du behåller din primära fil lagring kan Azure-filresurser fungera som ett idealiskt lagrings utrymme för säkerhets kopiering eller DR för att förbättra affärs kontinuiteten. Du kan använda Azure-filresurser för att säkerhetskopiera dina data från befintliga fil servrar, samtidigt som du behåller Windows-DACL-filer. För DR-scenarier kan du konfigurera ett autentiseringsalternativ som stöd för korrekt åtkomst kontroll vid redundansväxling.

## <a name="supported-scenarios"></a>Scenarier som stöds

I följande tabell sammanfattas de Azure-filresurser som stöds för Azure AD DS och lokala AD DS (för hands version). Vi rekommenderar att du väljer den domän tjänst som du har antagit för din klient miljö för integrering med Azure Files. Om du redan har installerat AD DS (Preview) lokalt eller i Azure där enheterna är anslutna till din AD, bör du välja att använda AD DS (för hands version) för autentisering med Azure-filresurser. Om du redan har antagit Azure AD DS (GA) bör du använda det för autentisering med Azure-filresurser.


|Azure AD DS-autentisering  | lokal AD DS-autentisering (förhands granskning)  |
|---------|---------|
|Azure AD DS-anslutna Windows-datorer har åtkomst till Azure-filresurser med Azure AD-autentiseringsuppgifter över SMB.     |Lokala AD DS-anslutna Windows-datorer kan komma åt Azure-filresurser med lokala Active Directory autentiseringsuppgifter som synkroniseras med Azure AD över SMB.         |

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

- Azure AD DS och lokal AD DS-autentisering stöder inte autentisering mot dator konton. Du kan använda ett tjänst inloggnings konto i stället.
- Azure AD DS-autentisering stöder inte autentisering mot Azure AD-anslutna enheter.

## <a name="advantages-of-identity-based-authentication"></a>Fördelar med Identity-baserad autentisering
Identitetsbaserade autentisering för Azure Files ger flera fördelar jämfört med autentisering med delade nycklar:

-   **Utöka den traditionella identitetsbaserade fil resurs åtkomst upplevelsen till molnet med lokal AD DS och Azure AD DS**  
    Om du planerar att lyfta och flytta ditt program till molnet, ersätta traditionella fil servrar med Azure-filresurser, kanske du vill att ditt program ska autentiseras med antingen lokala AD DS-eller Azure AD DS-autentiseringsuppgifter för att komma åt fildata. Azure Files stöder användning av både lokala AD DS-eller Azure AD DS-autentiseringsuppgifter för att få åtkomst till Azure-filresurser via SMB från antingen lokala AD DS-eller Azure AD DS-domänanslutna virtuella datorer.

-   **Använd detaljerad åtkomst kontroll på Azure-filresurser**  
    Du kan bevilja behörigheter till en speciell identitet på resurs-, katalog-eller filnivå. Anta till exempel att du har flera team som använder en enda Azure-filresurs för projekt samarbete. Du kan ge alla team åtkomst till icke-känsliga kataloger, samtidigt som du begränsar åtkomsten till kataloger som innehåller känslig finansiell information till ditt finans team. 

-   **Säkerhetskopiera Windows ACL: er (kallas även NTFS) tillsammans med dina data**  
    Du kan använda Azure-filresurser för att säkerhetskopiera befintliga lokala fil resurser. Azure Files bevarar dina ACL: er tillsammans med dina data när du säkerhetskopierar en fil resurs till Azure-filresurser över SMB.

## <a name="how-it-works"></a>Så här fungerar det

Azure-filresurser stöder Kerberos-autentisering för integrering med antingen Azure AD DS eller lokal AD DS (för hands version). Innan du kan aktivera autentisering på Azure-filresurser måste du först konfigurera din domän miljö. För Azure AD DS-autentisering bör du aktivera Azure AD Domain Services och domän anslutning till de virtuella datorer som du planerar att komma åt fil data från. Din domänanslutna virtuella dator måste finnas i samma virtuella nätverk (VNET) som Azure AD DS. För den lokala AD DS-autentiseringen (för hands version) måste du dessutom konfigurera domänkontrollanten och domänen ansluta till dina datorer eller virtuella datorer.

När en identitet som är kopplad till ett program som körs på en virtuell dator försöker få åtkomst till data i Azure-filresurser, skickas begäran till Azure AD DS för att autentisera identiteten. Om autentiseringen lyckas returnerar Azure AD DS en Kerberos-token. Programmet skickar en begäran som inkluderar Kerberos-token och Azure-filresurser använder denna token för att auktorisera begäran. Azure-filresurser får bara token och behåller inte Azure AD DS-autentiseringsuppgifter. Lokal AD DS-autentisering fungerar på liknande sätt, där AD DS tillhandahåller Kerberos-token.

![Skärm bild som visar diagram över Azure AD-autentisering över SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Aktivera identitets baserad autentisering

Du kan aktivera Identity-baserad autentisering med antingen Azure AD DS eller lokal AD DS (för hands version) för Azure-filresurser på dina nya och befintliga lagrings konton. Det går bara att använda en domän tjänst för autentisering av fil åtkomst på lagrings kontot, som gäller för alla fil resurser i kontot. Detaljerad vägledning om hur du konfigurerar dina fil resurser för autentisering med Azure AD DS i vår artikel [aktivera Azure Active Directory Domain Services autentisering på Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) och vägledning för lokal AD DS (för hands version) i vår andra artikel, [Aktivera lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurera behörigheter på resurs nivå för Azure Files

När antingen Azure AD DS eller lokal AD DS-autentisering (förhands granskning) är aktive rad kan du använda inbyggda RBAC-roller eller konfigurera anpassade roller för Azure AD-identiteter och tilldela åtkomst behörigheter till alla fil resurser i dina lagrings konton. Den tilldelade behörigheten tillåter att den beviljade identiteten endast får åtkomst till resursen, inget annat, inte ens rot katalogen. Du måste fortfarande konfigurera katalog-eller fil nivå behörigheter separat för Azure-filresurser.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurera katalog-eller fil nivå behörigheter för Azure Files

Azure-filresurser tillämpar standard behörigheter för Windows-filer både på katalog-och filnivå, inklusive rot katalogen. Konfiguration av katalog-eller fil nivå behörigheter stöds över både SMB och REST. Montera mål fil resursen från den virtuella datorn och konfigurera behörigheter med Windows Utforskaren, Windows- [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)eller kommandot [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Använd lagrings konto nyckeln för behörigheter för superanvändare

En användare med lagrings konto nyckeln kan komma åt Azure-filresurser med behörigheten Superanvändare. Behörigheter för superanvändare kringgå alla begränsningar för åtkomst kontroll.

> [!IMPORTANT]
> Vi rekommenderar att du inte delar dina lagrings konto nycklar och använder identitets baserad autentisering närhelst det är möjligt.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Bevara katalog-och fil åtkomst kontrol listor när du importerar data till Azure-filresurser

Azure Files stöder bevarande av katalog-eller filnivå-ACL: er vid kopiering av data till Azure-filresurser. Du kan kopiera ACL: er på en katalog eller fil till Azure-filresurser med hjälp av antingen Azure File Sync eller vanliga verktyg för fil rörelse. Du kan till exempel använda [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) med `/copy:s` flaggan för att kopiera data samt ACL: er till en Azure-filresurs. ACL: er bevaras som standard, du behöver inte aktivera Identity-baserad autentisering på ditt lagrings konto för att bevara ACL: er.

## <a name="pricing"></a>Prissättning
Det finns ingen ytterligare service avgift för att aktivera identitets baserad autentisering över SMB på ditt lagrings konto. Mer information om priser finns i [Azure Files priser](https://azure.microsoft.com/pricing/details/storage/files/) och [Azure AD Domain Services priser](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files-och identitets-baserad autentisering över SMB finns i följande resurser:

- [Planera för distribution av Azure Files](storage-files-planning.md)
- [Aktivera lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md)
- [Aktivera Azure Active Directory Domain Services autentisering på Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [VANLIGA FRÅGOR OCH SVAR](storage-files-faq.md)
