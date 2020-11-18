---
title: Krav för Azure AD Connect moln etablering i Azure AD
description: I den här artikeln beskrivs de krav och maskin varu krav du behöver för att konfigurera molnet.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dbdd5153186ee47e37856637eac16d6d450cc5a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695188"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Krav för Azure AD Connect-molnetablering
Den här artikeln innehåller rikt linjer för hur du väljer och använder Azure Active Directory (Azure AD) Anslut moln etablering som din identitets lösning.



## <a name="cloud-provisioning-agent-requirements"></a>Krav för moln etablerings agent
Du behöver följande för att kunna använda Azure AD Connect Cloud-etablering:
    
- Ett administratörs konto med hybrid identitet för din Azure AD-klient som inte är en gäst användare.
- En lokal server för etablerings agenten med Windows 2012 R2 eller senare.  Den här servern måste vara en nivå 0-server som baseras på [Active Directory administratörs nivå modell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Konfigurationer för lokala brand väggar.

>[!NOTE]
>Etablerings agenten kan för närvarande bara installeras på engelska språk servrar. Att installera ett engelskt språk paket på en icke-engelsk server är inte en giltig lösning och leder till att agenten inte kan installeras. 

Resten av dokumentet innehåller steg-för-steg-instruktioner för dessa krav.

### <a name="in-the-azure-active-directory-admin-center"></a>I Azure Active Directory administrations Center

1. Skapa ett hybrid identitets administratörs konto med enbart moln på din Azure AD-klient. På så sätt kan du hantera konfigurationen av din klient om dina lokala tjänster inte fungerar eller blir otillgängliga. Lär dig mer om hur du [lägger till ett moln med Hybrid identiteter som endast är moln](../fundamentals/add-users-azure-active-directory.md). Att slutföra det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
1. Lägg till ett eller flera [anpassade domän namn](../fundamentals/add-custom-domain.md) i Azure AD-klienten. Användarna kan logga in med ett av dessa domän namn.

### <a name="in-your-directory-in-active-directory"></a>I din katalog i Active Directory

Kör [IdFix-verktyget](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) för att förbereda katalog-attributen för synkronisering.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en domänansluten värd server som kör Windows Server 2012 R2 eller senare med minst 4 GB RAM-minne och .NET 4.7.1 + Runtime.

1. Körnings principen för PowerShell på den lokala servern måste vara inställd på odefinierad eller RemoteSigned.

1. Om det finns en brand vägg mellan dina servrar och Azure AD konfigurerar du följande objekt:
   - Se till att agenter kan göra *utgående* begär anden till Azure AD över följande portar:

        | Portnummer | Hur den används |
        | --- | --- |
        | **80** | Hämtar listor över återkallade certifikat (CRL) När TLS/SSL-certifikatet verifierades.  |
        | **443** | Hanterar all utgående kommunikation med tjänsten. |
        | **8080** (valfritt) | Agenter rapporterar sin status var 10: e minut via port 8080, om port 443 inte är tillgänglig. Den här statusen visas i Azure AD-portalen. |
     
   - Om brand väggen tillämpar regler enligt de ursprungliga användarna öppnar du portarna för trafik från Windows-tjänster som körs som en nätverks tjänst.
   - Om din brand vägg eller proxy låter dig ange säkra suffix lägger du till anslutningar till \* . msappproxy.net och \* . ServiceBus.Windows.net. Om inte, Tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)som uppdateras varje vecka.
   - Dina agenter behöver åtkomst till login.windows.net och login.microsoftonline.com för inledande registrering. Öppna brand väggen för dessa URL: er även.
   - För certifikat validering, avblockera följande URL: er: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 och www \. Microsoft.com:80. Dessa URL: er används för certifikat validering med andra Microsoft-produkter, så du kanske redan har dessa URL: er avblockerade.

>[!NOTE]
> Det finns inte stöd för att installera moln etablerings agenten på Windows Server Core.


### <a name="additional-requirements"></a>Ytterligare krav
- [Microsoft .NET Framework-4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-krav

>[!NOTE]
>Transport Layer Security (TLS) är ett protokoll som möjliggör säker kommunikation. Att ändra TLS-inställningarna påverkar hela skogen. Mer information finns i [Uppdatera för att aktivera tls 1,1 och tls 1,2 som standard säkra protokoll i WinHTTP i Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Windows Server som är värd för Azure AD Connect Cloud Provisioning agent måste ha TLS 1,2 aktiverat innan du installerar det.

Följ dessa steg om du vill aktivera TLS 1,2.

1. Ange följande registernycklar:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starta om servern.

## <a name="known-limitations"></a>Kända begränsningar
Följande är kända begränsningar:

### <a name="delta-synchronization"></a>Deltasynkronisering

- Grupp definitions filtrering för delta-synkronisering stöder inte fler än 1500 medlemmar
- När du tar bort en grupp som används som en del av ett grupp omfångs filter, tas inte användare som är medlemmar i gruppen bort. 
- När du byter namn på ORGANISATIONSENHETen eller gruppen som är inom omfånget kommer delta synkronisering inte att ta bort användarna

### <a name="provisioning-logs"></a>Etableringsloggar
- Etablerings loggar skiljer sig inte tydligt mellan åtgärderna skapa och uppdatera.  Du kan se en skapa-åtgärd för en uppdatering och en uppdaterings åtgärd för en Create.

### <a name="cross-domain-references"></a>Kors domän referenser
- Om du har användare med medlems referenser i en annan domän, kommer de inte att synkroniseras som en del av den aktuella domän synkroniseringen för den användaren. 
- (Exempel: en administratör av den användare som du synkroniserar finns i domän B och användaren är i domän A. När du synkroniserar både domän A och B kommer de att synkroniseras, men användar hanteraren kommer inte att överföras.

### <a name="group-re-naming-or-ou-re-naming"></a>Namnbyte för grupper eller ORGANISATIONSENHET
- Om du byter namn på en grupp eller OU i AD som är inom omfånget för en specifik konfiguration kommer moln etablerings jobbet inte att kunna identifiera namn ändringen i AD. Jobbet hamnar inte i karantän och fortsätter att vara felfritt



## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)