---
title: Översikt – Azure Files identitets-baserad auktorisering
description: Azure Files stöder identitets-baserad autentisering över SMB (Server Message Block) via Azure Active Directory Domain Services (AD DS) och Active Directory. Dina domänanslutna virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 3a9a2a903bd9979cd0f9a09b7589edc6d4fd8962
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565100"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Översikt över stöd för Azure Files Identity-baserad autentisering för SMB-åtkomst
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Information om hur du aktiverar AD-autentisering för Azure-filresurser finns i [aktivera Active Directory autentisering över SMB för Azure-filresurser](storage-files-active-directory-domain-services-enable.md).

Information om hur du aktiverar Azure AD DS-autentisering för Azure-filresurser finns i [aktivera Azure Active Directory Domain Service-autentisering över SMB för Azure Files](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Ordlista 
Det är bra att förstå vissa viktiga villkor som rör Azure AD Domain Service-autentisering över SMB för Azure-filresurser:

-   **Kerberos-autentisering**

    Kerberos är ett autentiseringsprotokoll som används för att verifiera identiteten för en användare eller värd. Mer information om Kerberos finns i [Översikt över Kerberos-autentisering](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **SMB-protokoll (Server Message Block)**

    SMB är ett standardiserat nätverks fil delnings protokoll. SMB kallas även common Internet File System eller CIFS. Mer information om SMB finns i [Översikt över Microsoft SMB-protokoll och CIFS-protokoll](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Azure AD kombinerar kärn katalog tjänster, program åtkomst hantering och identitets skydd i en enda lösning. Azure AD gör det möjligt för domänanslutna virtuella Windows-datorer (VM) att komma åt Azure-filresurser med dina autentiseringsuppgifter för Azure AD. Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (Azure AD DS)**

    Azure AD Domain Services (GA) tillhandahåller hanterade domän tjänster som domän anslutning, grup principer, LDAP och Kerberos/NTLM-autentisering. Dessa tjänster är helt kompatibla med Windows Server Active Directory. Mer information finns i [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services (AD DS, även kallat AD)**

    Active Directory (AD) (för hands version) innehåller metoder för att lagra katalog data och göra dem tillgängliga för användare och administratörer i nätverket. Säkerhet är integrerat med Active Directory via inloggningsautentisering och åtkomst kontroll till objekt i katalogen. Med en enda nätverks inloggning kan administratörer hantera katalog data och organisationer över hela nätverket och auktoriserade nätverks användare kan komma åt resurser var som helst i nätverket. AD antas ofta av företag lokalt och använder AD-autentiseringsuppgifter som identitet för åtkomst kontroll. Mer information finns i [Active Directory Domain Services översikt](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Rollbaserad Access Control i Azure (RBAC)**

    Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du hantera åtkomst till resurser genom att ge användarna minst de behörigheter som krävs för att utföra sina jobb. Mer information om RBAC finns i [Vad är rollbaserad åtkomst kontroll (RBAC) i Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Vanliga användarsituationer

Identitetsbaserade autentisering och stöd för Windows ACL: er på Azure Files är bäst för följande användnings fall:

### <a name="replace-on-premises-file-servers"></a>Ersätta lokala fil servrar

Föråldrade och ersättande lokala fil servrar är ett vanligt problem som varje företag stöter på i IT-modernisering resan. Azure-filresurser med AD (förhands granskning)-autentisering är den bästa anpassningen här, när du kan migrera data till Azure Files. Med en fullständig migrering kan du dra nytta av fördelarna med hög tillgänglighet och skalbarhet samtidigt som du minimerar ändringar på klient sidan, särskilt komplicerad AD-domän infrastruktur. Det ger en sömlös migrering till slutanvändare, så de kan fortsätta att komma åt sina data med samma autentiseringsuppgifter med hjälp av sina befintliga domänanslutna datorer.

### <a name="lift-and-shift-applications-to-azure"></a>Lyft och växla program till Azure

När du lyfter och flyttar program till molnet, vill du behålla samma autentiserings modell för dina data. När vi utökar den identitetsbaserade åtkomst kontrollen till Azure-filresurser, eliminerar den behovet av att ändra programmet till modern auth-metoder och påskynda moln införande. Azure-filresurser ger möjlighet att integrera med antingen Azure AD DS (GA) eller AD (för hands version) för autentisering. Om din plan ska vara 100% Cloud Native och minimera ansträngningarna att hantera moln infrastrukturer, är Azure AD DS en bättre anpassning som en fullständigt hanterad domän tjänst. Om du behöver fullständig kompatibilitet med AD DS-funktioner (GA) kan du överväga att utöka din AD-miljö till molnet med hjälp av självvärdbaserade domänkontrollanter på virtuella datorer. I båda fallen ger vi flexibiliteten att välja de domän tjänster som passar dina affärs behov.

### <a name="backup-and-disaster-recovery-dr"></a>Säkerhets kopiering och haveri beredskap (DR)

Om du behåller din primära fil lagring kan Azure-filresurser fungera som ett idealiskt lagrings utrymme för säkerhets kopiering eller DR för att förbättra affärs kontinuiteten. Du kan använda Azure-filresurser för att säkerhetskopiera dina data från befintliga fil servrar, samtidigt som du behåller Windows-DACL-filer. För DR-scenarier kan du konfigurera ett autentiseringsalternativ som stöd för korrekt åtkomst kontroll vid redundansväxling.

## <a name="supported-scenarios"></a>Scenarier som stöds

I följande tabell sammanfattas de Azure-filresurser som stöds för Azure AD DS (GA) och AD (för hands version). Vi rekommenderar att du väljer den domän tjänst som du har antagit för din klient miljö för integrering med Azure Files. Om du har installerat AD (för hands version) lokalt eller på Azure där enheterna är domänanslutna till AD, bör du välja att använda AD (för hands version) för autentisering med Azure-filresurser. Om du redan har antagit Azure AD DS (GA) bör du använda det för autentisering med Azure-filresurser.


|Azure AD DS-autentisering (GA)  |AD (förhands granskning)-autentisering  |
|---------|---------|
|Azure AD DS-domänanslutna Windows-datorer kan komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter över SMB.     |AD-domänanslutna Windows-datorer har åtkomst till Azure-filresurser med AD-autentiseringsuppgifter som synkroniseras med Azure AD över SMB.         |

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

- Azure AD DS-och AD-autentisering (för hands version) stöder inte autentisering mot dator konton. Du kan använda ett tjänst inloggnings konto i stället.
- Azure AD DS-autentisering (GA) stöder inte autentisering mot anslutna Azure AD-enheter.

## <a name="advantages-of-identity-based-authentication"></a>Fördelar med Identity-baserad autentisering
Identitetsbaserade autentisering för Azure Files ger flera fördelar jämfört med autentisering med delade nycklar:

-   **Utöka den traditionella identitetsbaserade fil resurs åtkomst upplevelsen till molnet med AD och Azure AD DS**  
    Om du planerar att "lyfta och byta" ditt program till molnet, ersätta traditionella fil servrar med Azure-filresurser, kanske du vill att ditt program ska autentisera med AD-eller Azure AD-autentiseringsuppgifter för att komma åt fildata. Azure Files stöder användning av både AD-eller Azure AD-autentiseringsuppgifter för att få åtkomst till Azure-filresurser via SMB från AD-eller Azure AD DS-domänanslutna virtuella datorer.

-   **Använd detaljerad åtkomst kontroll på Azure-filresurser**  
    Du kan bevilja behörigheter till en speciell identitet på resurs-, katalog-eller filnivå. Anta till exempel att du har flera team som använder en enda Azure-filresurs för projekt samarbete. Du kan ge alla team åtkomst till icke-känsliga kataloger, samtidigt som du begränsar åtkomsten till kataloger som innehåller känslig finansiell information till ditt finans team. 

-   **Säkerhetskopiera Windows ACL: er (kallas även NTFS) tillsammans med dina data**  
    Du kan använda Azure-filresurser för att säkerhetskopiera befintliga lokala fil resurser. Azure Files bevarar dina ACL: er tillsammans med dina data när du säkerhetskopierar en fil resurs till Azure-filresurser över SMB.

## <a name="how-it-works"></a>Hur det fungerar
Azure-filresurser stöder Kerberos-autentisering för integrering med antingen Azure AD DS (GA) eller AD (för hands version). Innan du kan aktivera autentisering på Azure-filresurser måste du först konfigurera din domän miljö. För Azure AD DS-autentisering (GA) bör du aktivera Azure AD Domain Services och domän anslutning till de virtuella datorer som du planerar att komma åt fil data från. Din domänanslutna virtuella dator måste finnas i samma virtuella nätverk (VNET) som Azure AD Domain Services. För AD (för hands version)-autentisering måste du på samma sätt konfigurera Active Directory domänkontrollant och domän anslutning till dina datorer eller virtuella datorer.

När en identitet som är kopplad till ett program som körs på en virtuell dator försöker komma åt data i Azure-filresurser, skickas begäran till Azure AD Domain Services för att autentisera identiteten. Om autentiseringen lyckas returnerar Azure AD Domain Services en Kerberos-token. Programmet skickar en begäran som inkluderar Kerberos-token och Azure-filresurser använder denna token för att auktorisera begäran. Azure-filresurser får bara token och behåller inte Azure AD-autentiseringsuppgifter. AD-autentisering fungerar på liknande sätt, där AD tillhandahåller Kerberos-token.

![Skärm bild som visar diagram över Azure AD-autentisering över SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Aktivera identitets baserad autentisering

Du kan aktivera Identity-baserad autentisering med antingen Azure AD DS (GA) eller AD (för hands version) för Azure-filresurser på dina nya och befintliga lagrings konton. Det går bara att använda en domän tjänst för autentisering av fil åtkomst på lagrings kontot, som gäller för alla fil resurser i kontot. Detaljerade anvisningar om hur du konfigurerar fil resurser för autentisering med Azure AD DS (GA) i vår artikel [aktivera Azure Active Directory Domain Services autentisering över SMB för Azure Files](storage-files-active-directory-enable.md) och vägledning för AD (för hands version) i vår andra artikel aktiverar du [Active Directory över SMB för Azure-filresurser](storage-files-active-directory-domain-services-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurera behörigheter på resurs nivå för Azure Files

När antingen Azure AD DS-eller AD (Preview)-autentisering har Aktiver ATS kan du använda inbyggda RBAC-roller eller konfigurera anpassade roller för Azure AD-identiteter och tilldela åtkomst behörigheter till alla fil resurser i dina lagrings konton. den tilldelade behörigheten tillåter att den beviljade identiteten endast får åtkomst till resursen, inget annat, inte ens rot katalogen. Du måste fortfarande konfigurera katalog-eller fil nivå behörigheter separat för Azure-filresurser.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurera katalog-eller fil nivå behörigheter för Azure Files

Azure-filresurser tillämpar standard behörigheter för Windows-filer både på katalog-och filnivå, inklusive rot katalogen. Konfiguration av katalog-eller fil nivå behörigheter stöds över både SMB och REST. Montera mål fil resursen från den virtuella datorn och konfigurera behörigheter med Windows Utforskaren, Windows- [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)eller kommandot [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Använd lagrings konto nyckeln för behörigheter för superanvändare

En användare som har lagrings konto nyckeln kan komma åt Azure-filresurser med behörigheten Superanvändare. Behörigheter för superanvändare kringgå alla begränsningar för åtkomst kontroll.

> [!IMPORTANT]
> Vi rekommenderar att du inte delar dina lagrings konto nycklar och använder identitets baserad autentisering närhelst det är möjligt.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Bevara katalog-och fil åtkomst kontrol listor när du importerar data till Azure-filresurser

Azure Files stöder bevarande av katalog-eller filnivå-ACL: er vid kopiering av data till Azure-filresurser. Du kan kopiera ACL: er på en katalog eller fil till Azure-filresurser med hjälp av antingen Azure File Sync eller vanliga verktyg för fil rörelse. Du kan till exempel använda [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) med flaggan `/copy:s` för att kopiera data samt ACL: er till en Azure-filresurs. ACL: er bevaras som standard, du behöver inte aktivera Identity-baserad autentisering på ditt lagrings konto för att bevara ACL: er.

## <a name="pricing"></a>Priser
Det finns ingen ytterligare service avgift för att aktivera identitets baserad autentisering över SMB på ditt lagrings konto. Mer information om priser finns [Azure Files priser](https://azure.microsoft.com/pricing/details/storage/files/) och [Azure AD Domain Services prissättnings](https://azure.microsoft.com/pricing/details/active-directory-ds/) sidor om du letar efter AAD DS-information.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files-och identitets-baserad autentisering över SMB finns i följande resurser:

- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Aktivera Active Directory autentisering över SMB för Azure-filresurser](storage-files-active-directory-domain-services-enable.md)
- [Aktivera Azure Active Directory Domain Services autentisering på Azure Files](storage-files-active-directory-enable.md)
- [VANLIGA FRÅGOR OCH SVAR](storage-files-faq.md)
