---
title: Översikt - Identitetsbaserad auktorisering av Azure Files
description: Azure Files stöder identitetsbaserad autentisering via SMB (Server Message Block) via Azure Active Directory Domain Services (AD DS) och Active Directory. Dina domänanslutna virtuella Datorer i Windows (VMs) kan sedan komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536596"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Översikt över Azure Files identitetsbaserad autentiseringsstöd för SMB-åtkomst
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Mer information om hur du aktiverar lokal Active Directory Domain Services-autentisering för Azure-filresurser (förhandsversion) finns i [Aktivera lokal Active Directory Domain Services-autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md).

Mer information om hur du aktiverar Azure AD DS-autentisering för Azure-filresurser finns i [Aktivera Azure Active Directory Domain Services-autentisering på Azure-filer](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Ordlista 
Det är bra att förstå några viktiga termer som rör Azure AD Domain Service-autentisering över SMB för Azure-filresurser:

-   **Kerberos-autentisering**

    Kerberos är ett autentiseringsprotokoll som används för att verifiera en användares eller värds identitet. Mer information om Kerberos finns i [Kerberos autentiseringsöversikt](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **SMB-protokoll (Server Message Block)**

    SMB är ett branschstandardprotokoll för fildelning av nätverksfiler. SMB är också känd som Common Internet File System eller CIFS. Mer information om SMB finns i [Microsoft SMB Protocol och CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) är Microsofts molnbaserade katalog- och identitetshanteringstjänst för flera innehavare. Azure AD kombinerar grundläggande katalogtjänster, hantering av programåtkomst och identitetsskydd till en enda lösning. Azure AD-anslutna virtuella Datorer med Windows (VMs) kan komma åt Azure-filresurser med dina Azure AD-autentiseringsuppgifter. Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS tillhandahåller hanterade domäntjänster som domänanslutning, grupprinciper, LDAP- och Kerberos/NTLM-autentisering. Dessa tjänster är helt kompatibla med Active Directory Domain Services. Mer information finns i [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Lokala Active Directory Domain Services (AD DS)**

    Ad DS-integrering (Active Directory Domain Services) med Azure Files (preview) innehåller metoder för att lagra katalogdata samtidigt som den blir tillgänglig för nätverksanvändare och administratörer. Säkerhet integreras med AD DS genom inloggningsautentisering och åtkomstkontroll till objekt i katalogen. Med en enda nätverksinloggning kan administratörer hantera katalogdata och organisation i hela nätverket, och behöriga nätverksanvändare kan komma åt resurser var som helst i nätverket. AD DS används ofta av företag i lokala miljöer och AD DS-autentiseringsuppgifter används som identitet för åtkomstkontroll. Mer information finns i [Översikt över Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Azure-rollbaserad åtkomstkontroll (RBAC)**

    Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med hjälp av RBAC kan du hantera åtkomst till resurser genom att ge användarna minst behörighet som behövs för att utföra sina jobb. Mer information om RBAC finns [i Vad är rollbaserad åtkomstkontroll (RBAC) i Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Vanliga användarsituationer

Identitetsbaserad autentisering och stöd för Windows-ACL:er på Azure Files utnyttjas bäst för följande användningsfall:

### <a name="replace-on-premises-file-servers"></a>Ersätta lokala filservrar

Att inaktuella och ersätta spridda lokala filservrar är ett vanligt problem som alla företag stöter på i sin IT-moderniseringsresa. Azure-filresurser med lokal AD DS-autentisering (förhandsversion) är den bästa passformen här, när du kan migrera data till Azure-filer. En fullständig migrering gör att du kan dra nytta av fördelarna med hög tillgänglighet och skalbarhet samtidigt som du minimerar ändringarna på klientsidan. Det ger en sömlös migreringsupplevelse till slutanvändare, så att de kan fortsätta att komma åt sina data med samma autentiseringsuppgifter med hjälp av sina befintliga domänanslutna datorer.

### <a name="lift-and-shift-applications-to-azure"></a>Lyfta och flytta program till Azure

När du lyfter och flyttar program till molnet vill du behålla samma autentiseringsmodell för dina data. När vi utökar den identitetsbaserade åtkomstkontrollupplevelsen till Azure-filresurser elimineras behovet av att ändra ditt program till moderna auth-metoder och påskynda molnanvändning. Azure-filresurser är möjligheten att integrera med antingen Azure AD DS eller lokalt AD DS (förhandsversion) för autentisering. Om din plan ska vara 100 % inbyggd moln och minimera arbetet med att hantera molninfrastrukturer, skulle Azure AD DS passa bättre som en fullständigt hanterad domäntjänst. Om du behöver fullständig kompatibilitet med AD DS-funktioner kan du överväga att utöka AD DS-miljön till molnet med självvärda domänkontrollanter på virtuella datorer. Hur som helst erbjuder vi flexibiliteten att välja de domäntjänster som passar dina affärsbehov.

### <a name="backup-and-disaster-recovery-dr"></a>Säkerhetskopiering och haveriberedskap (DR)

Om du håller din primära fillagring lokalt kan Azure-filresurser fungera som en idealisk lagring för säkerhetskopiering eller DR, för att förbättra affärskontinuiteten. Du kan använda Azure-filresurser för att säkerhetskopiera data från befintliga filservrar, samtidigt som Windows-DACLs bevaras. För DR-scenarier kan du konfigurera ett autentiseringsalternativ för att stödja korrekt åtkomstkontroll vid redundans.

## <a name="supported-scenarios"></a>Scenarier som stöds

I följande tabell sammanfattas de autentiseringsscenarier som stöds för Azure AD DS och lokalt AD DS (förhandsversion). Vi rekommenderar att du väljer den domäntjänst som du har antagit för din klientmiljö för integrering med Azure Files. Om du redan har konfigurerat AD DS (förhandsversion) lokalt eller i Azure där dina enheter är domän anslutna till din AD, bör du välja att använda AD DS (förhandsversion) för Azure-filresurserautentisering. På samma sätt, om du redan har antagit Azure AD DS (GA), bör du använda det för Azure-filresurser autentisering.


|Azure AD DS-autentisering  | lokal AD DS-autentisering (förhandsversion)  |
|---------|---------|
|Azure AD DS-anslutna Windows-datorer kan komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter över SMB.     |Lokala AD DS-anslutna Windows-datorer kan komma åt Azure-filresurser med lokala Active Directory-autentiseringsuppgifter som synkroniseras med Azure AD via SMB.         |

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

- Azure AD DS- och lokal AD DS-autentisering stöder inte autentisering mot datorkonton. Du kan överväga att använda ett tjänstinloggningskonto i stället.
- Azure AD DS-autentisering stöder inte autentisering mot Azure AD-anslutna enheter.

## <a name="advantages-of-identity-based-authentication"></a>Fördelar med identitetsbaserad autentisering
Identitetsbaserad autentisering för Azure Files ger flera fördelar jämfört med att använda autentisering av delade nyckel:

-   **Utöka den traditionella identitetsbaserade åtkomstupplevelsen för filresurs till molnet med lokala AD DS och Azure AD DS**  
    Om du planerar att lyfta och flytta ditt program till molnet och ersätta traditionella filservrar med Azure-filresurser, kanske du vill att ditt program ska autentisera med lokala AD DS- eller Azure AD DS-autentiseringsuppgifter för att komma åt fildata. Azure Files stöder användning av både lokala AD DS- eller Azure AD DS-autentiseringsuppgifter för att komma åt Azure-filresurser via SMB från antingen lokala AD DS- eller Azure AD DS-domänanslutna virtuella datorer.

-   **Framtvinga detaljerad åtkomstkontroll för Azure-filresurser**  
    Du kan bevilja behörigheter till en viss identitet på resurs-, katalog- eller filnivå. Anta till exempel att du har flera team som använder en enda Azure-filresurs för projektsamarbete. Du kan ge alla team åtkomst till icke-känsliga kataloger, samtidigt som åtkomsten till kataloger som innehåller känsliga ekonomiska data begränsas till ditt Ekonomi-team. 

-   **Säkerhetskopiera Windows-åtkomstkontrollistor (kallas även NTFS) tillsammans med dina data**  
    Du kan använda Azure-filresurser för att säkerhetskopiera dina befintliga lokala filresurser. Azure Files bevarar dina ACL:er tillsammans med dina data när du säkerhetskopierar en filresurs till Azure-filresurser över SMB.

## <a name="how-it-works"></a>Hur det fungerar

Azure-filresurser stöder Kerberos-autentisering för integrering med antingen Azure AD DS eller lokalt AD DS (förhandsversion). Innan du kan aktivera autentisering på Azure-filresurser måste du först konfigurera domänmiljön. För Azure AD DS-autentisering bör du aktivera Azure AD Domain Services och domän ansluta till de virtuella datorer som du planerar att komma åt fildata från. Din domänanslutna virtuella dator måste finnas i samma virtuella nätverk (VNET) som din Azure AD DS. För lokal AD DS-autentisering (förhandsversion) måste du konfigurera domänkontrollanten och domänen för att ansluta till dina datorer eller virtuella datorer.

När en identitet som är associerad med ett program som körs på en virtuell dator försöker komma åt data i Azure-filresurser skickas begäran till Azure AD DS för att autentisera identiteten. Om autentiseringen lyckas returnerar Azure AD DS en Kerberos-token. Programmet skickar en begäran som innehåller Kerberos-token och Azure-filresurser använder den token för att auktorisera begäran. Azure-filresurser tar bara emot token och beständiga inte Azure AD DS-autentiseringsuppgifter. Lokal AD DS-autentisering fungerar på ett liknande sätt, där din AD DS tillhandahåller Kerberos-token.

![Skärmbild som visar diagram över Azure AD-autentisering via SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Aktivera identitetsbaserad autentisering

Du kan aktivera identitetsbaserad autentisering med antingen Azure AD DS eller lokal AD DS (förhandsversion) för Azure-filresurser på dina nya och befintliga lagringskonton. Endast en domäntjänst kan användas för filåtkomstautentisering på lagringskontot, vilket gäller för alla filresurser i kontot. Detaljerad vägledning om hur du konfigurerar filresurser för autentisering med Azure AD DS i vår artikel [Aktivera Azure Active Directory Domain Services-autentisering på Azure-filer](storage-files-identity-auth-active-directory-domain-service-enable.md) och vägledning för lokala AD DS (förhandsversion) i vår andra artikel, Aktivera lokal Active Directory Domain [Services-autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurera behörigheter på delningsnivå för Azure-filer

När antingen Azure AD DS- eller lokal AD DS-autentisering (förhandsversion) är aktiverad kan du använda inbyggda RBAC-roller eller konfigurera anpassade roller för Azure AD-identiteter och tilldela åtkomsträttigheter till alla filresurser i dina lagringskonton. Den tilldelade behörigheten gör att den beviljade identiteten bara får åtkomst till resursen, inget annat, inte ens rotkatalogen. Du måste fortfarande konfigurera behörigheter på katalog- eller filnivå för Azure-filresurser separat.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurera behörigheter på katalog- eller filnivå för Azure-filer

Azure-filresurser framtvingar standard windows-filbehörigheter på både katalog- och filnivå, inklusive rotkatalogen. Konfiguration av katalog- eller filnivåbehörigheter stöds över både SMB och REST. Montera målfilresursen från den virtuella datorn och konfigurera behörigheter med kommandot Utforskaren, Windows [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)eller kommandot [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Använd lagringskontonyckeln för superanvändarbehörigheter

En användare med lagringskontonyckeln kan komma åt Azure-filresurser med superanvändarbehörigheter. Superanvändarebehörigheter kringgår alla åtkomstkontrollbegränsningar.

> [!IMPORTANT]
> Vår rekommenderade säkerhetspraxis är att undvika att dela dina lagringskontonycklar och utnyttja identitetsbaserad autentisering när det är möjligt.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Bevara katalog- och fil-ACL:er vid import av data till Azure-filresurser

Azure Files stöder bevarande av ACL-filer på katalog- eller filnivå när data kopieras till Azure-filresurser. Du kan kopiera ACL:er i en katalog eller fil till Azure-filresurser med hjälp av antingen Azure File Sync eller common file movement toolsets. Du kan till exempel använda [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) med `/copy:s` flaggan för att kopiera data samt ACL:er till en Azure-filresurs. ACL:er bevaras som standard, du behöver inte aktivera identitetsbaserad autentisering på ditt lagringskonto för att bevara ACL:er.

## <a name="pricing"></a>Prissättning
Det finns ingen extra serviceavgift för att aktivera identitetsbaserad autentisering via SMB på ditt lagringskonto. Mer information om prissättning finns i [prissättningen för Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) och [prissättningen för Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-filer och identitetsbaserad autentisering via SMB finns i följande resurser:

- [Planera för distribution av Azure Files](storage-files-planning.md)
- [Aktivera lokal Active Directory Domain Services-autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md)
- [Aktivera Azure Active Directory Domain Services-autentisering på Azure-filer](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [VANLIGA FRÅGOR OCH SVAR](storage-files-faq.md)
