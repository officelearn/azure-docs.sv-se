---
title: Översikt över Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion), Azure Storage
description: Azure Files stöder identity-baserad autentisering över SMB (Server Message Block) (förhandsversion) via Azure Active Directory (Azure AD) Domain Services. Dina domänanslutna Windows-datorer (VM) kan sedan komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: 21087424be1a7a3edfe2dddcbec830bd74559b23
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269355"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>Översikt över autentiseringsstöd för Azure Files Azure Active Directory Domain Service (AAD DS) för SMB-åtkomst (förhandsversion)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Läs hur du aktiverar AAD DS-autentisering för Azure Files i [aktivera Azure Active Directory Domain Service-autentisering över SMB för Azure Files (förhandsversion)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Ordlista 
Det kan vara bra att förstå vissa viktiga termer som är relaterade till Azure AD Domain Service-autentisering över SMB för Azure Files:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) är Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Azure AD kombinerar viktiga katalogtjänster, åtkomsthantering för program och identitetsskydd i en enda lösning. Mer information finns i [vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Azure AD Domain Services tillhandahåller hanterade domäntjänster, till exempel domänanslutning, grupprinciper, LDAP och Kerberos/NTLM-autentisering. De här tjänsterna är helt kompatibla med Windows Server Active Directory. Mer information finns i [domäntjänster för Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

-   **Azure rollbaserad åtkomstkontroll (RBAC)**  
    Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan hantera du åtkomst till resurser genom att tilldela användare till de minst antal behörigheter som krävs för att utföra sitt arbete. Mer information om RBAC finns [vad är rollbaserad åtkomstkontroll (RBAC) i Azure?](../../role-based-access-control/overview.md)

-   **Kerberos-autentisering**

    Kerberos är ett autentiseringsprotokoll som används för att verifiera en användares eller värds identitet. Mer information om Kerberos finns i [översikt över Kerberos-autentisering](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block (SMB) protokollet**  
    SMB är en branschstandard fildelning nätverksprotokoll. SMB kallas även Common Internet File System eller CIFS. Mer information om SMB finns [Microsoft SMB-protokollet och CIFS Protokollöversikt](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Fördelarna med Azure AD Domain Service-autentisering
Azure AD Domain Service-autentisering för Azure Files erbjuder flera fördelar jämfört med autentisering med delad nyckel:

-   **Utöka traditionella identitetsbaserade filen åtkomst delningsmetoderna till molnet med Azure AD och Azure AD Domain Service**  
    Om du planerar att ”lift and shift” administratörsautentiseringsuppgifter ditt program till molnet, ersätta traditionella filservrar med Azure Files, och du kanske vill att programmet ska autentisera med Azure AD för att komma åt fildata. Azure filer kan användas med autentiseringsuppgifter för Azure AD åtkomst till Azure Files över SMB från AAD DS domänanslutna-Windows-datorer. Du kan också välja att synkronisera alla dina lokala Active Directory-objekt till Azure AD för att bevara användarnamn, lösenord och andra grupptilldelningar.

-   **Tillämpa detaljerad åtkomstkontroll på Azure-filresurser**  
    Du kan ge behörigheter till en viss identitet på resursen, katalogen eller filnivå. Anta exempelvis att du har flera team som använder en enda Azure-filresurs för projektsamarbete. Du kan bevilja alla grupper åtkomst till icke-känsliga kataloger och begränsa åtkomst till kataloger som innehåller känsliga ekonomiska data till ditt ekonomi team. 

-   **Säkerhetskopiera ACL: er tillsammans med dina data**  
    Du kan använda Azure Files för att säkerhetskopiera dina befintliga lokala filresurser. Azure Files bevarar dina ACL: er tillsammans med dina data när du säkerhetskopierar en fil delar till Azure Files över SMB.

## <a name="how-it-works"></a>Hur det fungerar
Azure Files använder Azure AD Domain Services för Kerberos-autentisering med autentiseringsuppgifter för Azure AD från domänanslutna virtuella datorer. Innan du kan använda Azure AD med Azure Files, måste du först aktivera Azure AD Domain Services och ansluta till domänen från de virtuella datorerna från vilken du planerar att få åtkomst till fildata. Din VM med ansluten till domänen måste finnas i samma virtuella nätverk (VNET) som Azure AD Domain Services. 

När en identitet som är associerad med ett program som körs på en virtuell dator försöker komma åt data i Azure Files, skickas begäran till Azure AD Domain Services att autentisera identiteten. Om autentiseringen lyckas returnerar en Kerberos-token i Azure AD Domain Services. Programmet skickar en begäran som innehåller Kerberos-token och Azure Files använder den token för att auktorisera begäran. Azure Files tar emot en token endast och sparas inte autentiseringsuppgifter för Azure AD.

![Skärmbild som visar diagram över Azure AD-autentisering över SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Aktivera Azure AD Domain-tjänst-autentisering för SMB-åtkomst
Du kan aktivera Azure AD Domain-tjänst-autentisering för Azure Files på din nya och befintliga lagringskonton som skapats efter den 24 September 2018. 

Innan du aktiverar den här funktionen, kontrollerar du att Azure AD Domain Services har distribuerats för primärt Azure AD-klient som är associerad till ditt lagringskonto. Om du inte redan har upprättat Azure AD Domain Services, följer du stegvisa anvisningar finns i [aktivera Azure Active Directory Domain Services med Azure portal](../../active-directory-domain-services/create-instance.md).

Azure AD Domain Services-distribution tar vanligtvis 10 – 15 minuter. När Azure AD Domain Services har distribuerats, kan du aktivera Azure AD-autentisering över SMB för Azure Files. Mer information finns i [aktivera Azure Active Directory Domain Service-autentisering över SMB för Azure Files (förhandsversion)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurera resursnivåer för Azure Files
När Azure AD Domain Service-autentisering har aktiverats, kan du konfigurera anpassad RBAC-roller för Azure AD-identiteter och tilldela behörighet till alla filresurser i lagringskontot.

När ett program som körs på en domänansluten dator försöker montera en Azure-filresurs eller få åtkomst till en katalog eller fil, verifieras programmets autentiseringsuppgifter för Azure AD att åtkomstbehörighet till resursnivå och NTFS-behörigheter. Information om hur du konfigurerar resursnivåer finns i [aktivera Azure Active Directory Domain Service-autentisering över SMB (förhandsversion)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Konfigurera behörigheter för directory - eller filnivå för Azure Files 
Azure Files använder standard filen NTFS-behörigheter på nivån katalog och ett filnamn, inklusive i rotkatalogen. Konfigurationen av katalog - eller filnivå behörigheter stöds bara över SMB. Montera målfilresursen från din virtuella dator och konfigurera behörigheter med hjälp av Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) eller [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) kommando. 

> [!NOTE]
> Konfigurera NTFS-behörigheter via Windows Utforskaren stöds inte i förhandsversionen.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Använd lagringskontonyckeln för superanvändare behörigheter 
En användare som har lagringskontonyckeln kan komma åt Azure Files med behörigheter för superanvändare. Behörigheter för superanvändare överträffa alla åtkomstkontrollbegränsningarna konfigurerats på resurs-nivå med RBAC och framtvingas av Azure AD. Superanvändare behörigheter krävs för att montera en Azure-filresurs. 

> [!IMPORTANT]
> Undvik att dela dina lagringskontonycklar som en del av metodtips för säkerhet, och utnyttja Azure AD-behörigheter om möjligt.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Bevara ACL: er för katalog och ett filnamn för import av data till Azure-filresurser
Azure Files stöder nu bevarar mapp eller fil-ACL: er när du kopierar data till Azure-filresurser. Du kan kopiera ACL: er på en katalog eller en fil till Azure Files. Du kan till exempel använda [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) med flaggan `/copy:s` att kopiera både data och ACL: er till en Azure-filresurs. ACL konservering är aktiverad som standard och du behöver inte uttryckligen aktivera funktionen för tjänsten för Azure AD Domain authentication på ditt lagringskonto. 

## <a name="pricing"></a>Prissättning
Det är kostnadsfritt ytterligare service för att aktivera Azure AD-autentisering över SMB på ditt lagringskonto. Mer information om priser finns i [priser för Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) och [priser för Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/) sidor.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files och Azure AD-autentisering över SMB finns i följande källor:

- [Introduktion till Azure Files](storage-files-introduction.md)
- [Aktivera Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](storage-files-active-directory-enable.md)
- [Vanliga frågor och svar](storage-files-faq.md)
