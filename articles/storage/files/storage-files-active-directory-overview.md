---
title: Översikt över Azure Active Directory autentisering över SMB för Azure Files-Azure Storage
description: Azure Files stöder identitets-baserad autentisering över SMB (Server Message Block) via Azure Active Directory (Azure AD) Domain Services. Dina domänanslutna virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/30/2019
ms.author: rogarana
ms.openlocfilehash: 604cf2bbe0cf8ab036c76ee9223d1ee34fd4bd3d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854551"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Översikt över stöd för Azure AD DS-autentisering (Azure Files Azure Active Directory Domain Service) för SMB-åtkomst
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Information om hur du aktiverar Azure AD DS-autentisering för Azure Files finns i [aktivera autentisering med Azure Active Directory Domain Service över SMB för Azure Files](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Ordlista 
Det är bra att förstå vissa viktiga villkor som rör Azure AD Domain Service-autentisering över SMB för Azure Files:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Azure AD kombinerar kärn katalog tjänster, program åtkomst hantering och identitets skydd i en enda lösning. Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Azure AD Domain Services tillhandahåller hanterade domän tjänster som domän anslutning, grup principer, LDAP och Kerberos/NTLM-autentisering. Dessa tjänster är helt kompatibla med Windows Server Active Directory. Mer information finns i [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

-   **Rollbaserad Access Control i Azure (RBAC)**  
    Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du hantera åtkomst till resurser genom att ge användarna minst de behörigheter som krävs för att utföra sina jobb. Mer information om RBAC finns i [Vad är rollbaserad åtkomst kontroll (RBAC) i Azure?](../../role-based-access-control/overview.md)

-   **Kerberos-autentisering**

    Kerberos är ett autentiseringsprotokoll som används för att verifiera identiteten för en användare eller värd. Mer information om Kerberos finns i [Översikt över Kerberos-autentisering](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **SMB-protokoll (Server Message Block)**  
    SMB är ett standardiserat nätverks fil delnings protokoll. SMB kallas även common Internet File System eller CIFS. Mer information om SMB finns i [Översikt över Microsoft SMB-protokoll och CIFS-protokoll](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Fördelar med Azure AD Domain Service-autentisering
Azure AD Domain Service-autentisering för Azure Files ger flera fördelar jämfört med autentisering med delade nycklar:

-   **Utöka den traditionella identitetsbaserade fil resurs åtkomst upplevelsen till molnet med Azure AD och Azure AD Domain Service**  
    Om du planerar att "lyfta och byta" ditt program till molnet, ersätta traditionella fil servrar med Azure Files, kanske du vill att ditt program ska autentiseras med Azure AD-autentiseringsuppgifter för att få åtkomst till fildata. Azure Files stöder användning av Azure AD-autentiseringsuppgifter för att få åtkomst till Azure Files över SMB från Azure AD DS-domänanslutna virtuella Windows-datorer. Du kan också välja att synkronisera alla lokala Active Directory objekt till Azure AD för att bevara användar namn, lösen ord och andra grupp tilldelningar.

-   **Använd detaljerad åtkomst kontroll på Azure-filresurser**  
    Du kan bevilja behörigheter till en speciell identitet på resurs-, katalog-eller filnivå. Anta till exempel att du har flera team som använder en enda Azure-filresurs för projekt samarbete. Du kan ge alla team åtkomst till icke-känsliga kataloger, samtidigt som du begränsar åtkomsten till kataloger som innehåller känslig finansiell information till ditt finans team. 

-   **Säkerhetskopiera ACL: er tillsammans med dina data**  
    Du kan använda Azure Files för att säkerhetskopiera befintliga lokala fil resurser. Azure Files bevarar dina ACL: er tillsammans med dina data när du säkerhetskopierar en fil resurs till Azure Files över SMB.

## <a name="how-it-works"></a>Hur det fungerar
Azure Files använder Azure AD Domain Services för att stödja Kerberos-autentisering med Azure AD-autentiseringsuppgifter från domänanslutna virtuella datorer. Innan du kan använda Azure AD med Azure Files måste du först aktivera Azure AD Domain Services och ansluta domänen från de virtuella datorer som du planerar att komma åt fildata från. Din domänanslutna virtuella dator måste finnas i samma virtuella nätverk (VNET) som Azure AD Domain Services. 

När en identitet som är kopplad till ett program som körs på en virtuell dator försöker komma åt data i Azure Files skickas begäran till Azure AD Domain Services för att autentisera identiteten. Om autentiseringen lyckas returnerar Azure AD Domain Services en Kerberos-token. Programmet skickar en begäran som inkluderar Kerberos-token och Azure Files använder denna token för att auktorisera begäran. Azure Files tar emot enbart token och behåller inte Azure AD-autentiseringsuppgifter.

![Skärm bild som visar diagram över Azure AD-autentisering över SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Aktivera Azure AD Domain Service-autentisering för SMB-åtkomst
Du kan aktivera Azure AD Domain Service-autentisering för Azure Files på dina nya och befintliga lagrings konton som skapats efter den 24 september 2018. 

Innan du aktiverar den här funktionen kontrollerar du att Azure AD Domain Services har distribuerats för den primära Azure AD-klient som ditt lagrings konto är associerat med. Om du ännu inte har konfigurerat Azure AD Domain Services följer du de stegvisa anvisningarna i [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](../../active-directory-domain-services/create-instance.md).

Azure AD Domain Services distributionen tar vanligt vis 10 till 15 minuter. När Azure AD Domain Services har distribuerats kan du aktivera Azure AD-autentisering över SMB för Azure Files. Mer information finns i [aktivera Azure Active Directory Domain Service-autentisering över SMB för Azure Files](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurera behörigheter på resurs nivå för Azure Files
När Azure AD Domain Service-autentisering har Aktiver ATS kan du konfigurera anpassade RBAC-roller för Azure AD-identiteter och tilldela åtkomst behörigheter till alla fil resurser i lagrings kontot.

När ett program som körs på en domänansluten virtuell dator försöker montera en Azure-filresurs eller åtkomst till en katalog eller fil, verifieras programmets autentiseringsuppgifter för Azure AD för att säkerställa rätt behörigheter på resurs nivå och NTFS-behörighet. Information om hur du konfigurerar behörigheter på resurs nivå finns i [aktivera Azure Active Directory Domain Service Authentication över SMB](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Konfigurera behörigheter för katalog-eller filnivå för Azure Files 
Azure Files tillämpar standard behörigheter för NTFS-filer på katalog-och filnivå, inklusive i rot katalogen. Konfiguration av behörigheter på katalog-eller filnivå stöds endast över SMB. Montera mål fil resursen från den virtuella datorn och konfigurera behörigheter med Windows Utforskaren, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) eller [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) kommando. 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Använd lagrings konto nyckeln för behörigheter för superanvändare 
En användare som har lagrings konto nyckeln kan komma åt Azure Files med behörigheten Superanvändare. Behörigheter för superanvändare överskrider alla åtkomst kontroll begränsningar som kon figurer ATS på delnings nivå med RBAC och framtvingas av Azure AD. Behörigheter för superanvändare krävs för att montera en Azure-filresurs. 

> [!IMPORTANT]
> Som en del av metod tipsen för säkerhet bör du undvika att dela dina lagrings konto nycklar och utnyttja Azure AD-behörigheter närhelst det är möjligt.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Bevara katalog-och fil-ACL: er för data import till Azure-filresurser
Azure Files har nu stöd för att bevara katalog-eller fil-ACL: er när du kopierar data till Azure-filresurser. Du kan kopiera ACL: er på en katalog eller fil till Azure Files. Du kan till exempel använda [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) med flagga `/copy:s` för att kopiera både data och ACL: er till en Azure-filresurs. Bevara ACL är aktiverat som standard och du behöver inte uttryckligen aktivera Azure AD Domain Service Authentication-funktionen på ditt lagrings konto. 

## <a name="pricing"></a>Prissättning
Det finns ingen extra service avgift för att aktivera Azure AD-autentisering över SMB på ditt lagrings konto. Mer information om priser finns [Azure Files priser](https://azure.microsoft.com/pricing/details/storage/files/) och [Azure AD Domain Services prissättnings](https://azure.microsoft.com/pricing/details/active-directory-ds/) sidor.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files och Azure AD-autentisering över SMB finns i följande resurser:

- [Introduktion till Azure Files](storage-files-introduction.md)
- [Aktivera Azure Active Directory autentisering över SMB för Azure Files](storage-files-active-directory-enable.md)
- [Vanliga frågor och svar](storage-files-faq.md)
