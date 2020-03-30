---
title: Felsöka hybrid-Azure Active Directory-anslutna enheter
description: Felsökning av hybrid-Azure Active Directory gick med i Windows 10- och Windows Server 2016-enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331776"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Felsöka hybrid-Azure Active Directory-anslutna enheter 

Innehållet i den här artikeln gäller enheter som kör Windows 10 eller Windows Server 2016.

För andra Windows-klienter finns i artikeln [Felsökning av hybrid-Azure Active Directory-anslutna enheter på lägre nivå](troubleshoot-hybrid-join-windows-legacy.md).

Den här artikeln förutsätter att du har [konfigurerat hybrid Azure Active Directory-anslutna enheter](hybrid-azuread-join-plan.md) för att stödja följande scenarier:

- Enhetsbaserad villkorlig åtkomst
- [Företagsroaming av inställningar](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello för företag](../active-directory-azureadjoin-passport-deployment.md)

Det här dokumentet innehåller felsökningsvägledning för att lösa potentiella problem. 

För Windows 10 och Windows Server 2016 stöder hybrid-Azure Active Directory-anslutning uppdateringen windows 10 november 2015 och senare.

## <a name="troubleshoot-join-failures"></a>Felsöka kopplingsfel

### <a name="step-1-retrieve-the-join-status"></a>Steg 1: Hämta anslutningsstatus 

**Så här hämtar du anslutningsstatusen:**

1. Öppna en kommandotolk som administratör
2. Skriv `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Steg 2: Utvärdera kopplingsstatus 

Granska följande fält och se till att de har de förväntade värdena:

#### <a name="domainjoined--yes"></a>DomainJoined : JA  

Det här fältet anger om enheten är ansluten till en lokal Active Directory eller inte. Om värdet är **NEJ**kan enheten inte utföra en hybrid Azure AD-koppling.  

#### <a name="workplacejoined--no"></a>ArbetsplatsJoined: NEJ  

Det här fältet anger om enheten är registrerad med Azure AD som en personlig enhet (markerad som *Arbetsplatsanserad*). Det här värdet ska vara **NEJ** för en domänansluten dator som också är hybrid Azure AD-ansluten. Om värdet är **JA**lades ett arbets- eller skolkonto till innan hybrid-Azure AD-kopplingen slutfördes. I det här fallet ignoreras kontot när du använder Anniversary Update-versionen av Windows 10 (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined : JA  

Det här fältet anger om enheten är ansluten. Värdet blir **JA** om enheten antingen är en Azure AD-ansluten enhet eller en hybrid Azure AD-ansluten enhet.
Om värdet är **NEJ**har kopplingen till Azure AD ännu inte slutförts. 

Fortsätt till nästa steg för ytterligare felsökning.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Steg 3: Hitta den fas där kopplingen misslyckades och felkoden

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 och senare

Leta efter underavsnittet "Föregående registrering" i avsnittet Diagnostikdata i utdata för kopplingsstatus. Det här avsnittet visas bara om enheten är domänansluten och inte kan hybrid Azure AD-anslutning.
Fältet Felfas anger fasen för kopplingsfelet medan "Client ErrorCode" anger felkoden för kopplingsåtgärden.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Äldre Windows 10-versioner

Använd Loggboksloggar för att hitta fas- och felkoden för kopplingsfelen.

1. Öppna händelseloggarna **för registrering av användare** enhet i loggboken. Finns under **Program och tjänster Logga** > **registrering** > av Microsoft**Windows-användaresenhet** **Windows** > 
2. Leta efter händelser med följande event-ID 304, 305, 307.

![Händelse för fellogg](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Händelse för fellogg](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Steg 4: Kontrollera om det finns möjliga orsaker och lösningar från listorna nedan

#### <a name="pre-check-phase"></a>Fas före kontroll

Möjliga orsaker till misslyckande:

- Enheten har ingen siktlinje till domänkontrollanten.
   - Enheten måste finnas i organisationens interna nätverk eller på VPN med nätverkslinje till en lokal ACTIVE Directory -domänkontrollant (AD).

#### <a name="discover-phase"></a>Upptäck fas

Möjliga orsaker till misslyckande:

- SCP-objekt (Service Connection Point) har felkonfigurerats/inte kunnat läsa SCP-objekt från DOMÄNKONTROLLANT.
   - Ett giltigt SCP-objekt krävs i AD-skogen, som enheten tillhör, som pekar på ett verifierat domännamn i Azure AD.
   - Information finns i avsnittet [Konfigurera en tjänstanslutningspunkt](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Det gick inte att ansluta och hämta identifieringsmetadata från identifieringsslutpunkten.
   - Enheten bör kunna komma `https://enterpriseregistration.windows.net`åt , i SYSTEM-kontexten, för att identifiera slutpunkter för registrering och auktorisering. 
   - Om den lokala miljön kräver en utgående proxy måste IT-administratören se till att enhetens datorkonto kan identifiera och tyst autentisera till den utgående proxyn.
- Det gick inte att ansluta till slutpunkten för användarskapet och utföra sfäridentifiering. (Endast Windows 10 version 1809 och senare)
   - Enheten ska kunna komma `https://login.microsoftonline.com`åt , i SYSTEM-kontexten, för att utföra sfäridentifiering för den verifierade domänen och bestämma domäntypen (hanterad/federerad).
   - Om den lokala miljön kräver en utgående proxy måste IT-administratören se till att SYSTEM-kontexten på enheten kan identifiera och tyst autentisera till den utgående proxyn.

**Vanliga felkoder:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Orsak: Det går inte att läsa SCP-objektet och hämta Azure AD-klientinformationen.
   - Lösning: Se avsnittet [Konfigurera en tjänstanslutningspunkt](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Orsak: Generisk identifiering misslyckande. Det gick inte att hämta identifieringsmetadata från DRS.
   - Lösning: Hitta undertexten nedan för att undersöka vidare.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - Orsak: Åtgärden timelade ut när du utför Discovery.
   - Lösning: Se `https://enterpriseregistration.windows.net` till att det är tillgängligt i SYSTEM-kontexten. Mer information finns i avsnittet Krav för [nätverksanslutning](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Orsak: Generic Realm Discovery misslyckande. Det gick inte att fastställa domäntypen (hanterad/federerad) från STS. 
   - Lösning: Hitta undertexten nedan för att undersöka vidare.

**Vanliga underleverantörskoder:**

Om du vill hitta underfelskoden för identifieringsfelkoden använder du någon av följande metoder.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 och senare

Leta efter "DRS Discovery Test" i avsnittet "Diagnostikdata" i anslutningsstatusutdata. Det här avsnittet visas bara om enheten är domänansluten och inte kan hybrid Azure AD-anslutning.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Äldre Windows 10-versioner

Använd Loggboksloggar för att hitta fas- och felkoden för kopplingsfelen.

1. Öppna händelseloggarna **för registrering av användare** enhet i loggboken. Finns under **Program och tjänster Logga** > **registrering** > av Microsoft**Windows-användaresenhet** **Windows** > 
2. Leta efter händelser med följande event-ID 201

![Händelse för fellogg](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Nätverksfel

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Orsak: Det gick inte att upprätta anslutningen till servern
   - Lösning: Säkerställ nätverksanslutningen till de nödvändiga Microsoft-resurserna. Mer information finns i [Krav på nätverksanslutning](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Orsak: Allmän tidsgränsen för nätverket.
   - Lösning: Säkerställ nätverksanslutningen till de nödvändiga Microsoft-resurserna. Mer information finns i [Krav på nätverksanslutning](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Orsak: Nätverksstacken kunde inte avkoda svaret från servern.
   - Lösning: Kontrollera att nätverksproxyn inte stör och ändrar serversvaret.

###### <a name="http-errors"></a>HTTP-fel

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Orsak: SCP-objekt konfigurerat med fel klient-ID. Eller så hittades inga aktiva prenumerationer i klienten.
   - Lösning: Se till att SCP-objektet är konfigurerat med rätt Azure AD-klient-ID och aktiva prenumerationer eller finns i klienten.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Orsak: HTTP 503 från DRS-servern.
   - Lösning: Servern är inte tillgänglig för tillfället. framtida kopplingsförsök kommer sannolikt att lyckas när servern är online igen.

###### <a name="other-errors"></a>Andra fel

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Orsak: Serversvar JSON kunde inte tolkas. Sannolikt på grund av proxy returnera HTTP 200 med en HTML-autentisering sida.
   - Lösning: Om den lokala miljön kräver en utgående proxy måste IT-administratören se till att SYSTEM-kontexten på enheten kan identifiera och tyst autentisera till den utgående proxyn.

#### <a name="authentication-phase"></a>Autentiseringsfas

Gäller endast för federerade domänkonton.

Orsaker till misslyckande:

- Det gick inte att hämta en Access-token tyst för DRS-resursen.
   - Windows 10-enheter hämtar en autentiseringstoken från federationstjänsten med integrerad Windows-autentisering till en aktiv WS-Trust-slutpunkt. Detaljer: [Federation Service Konfiguration](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Vanliga felkoder:**

Använd Loggboksloggar för att hitta felkoden, underfelskoden, serverfelkoden och serverfelmeddelandet.

1. Öppna händelseloggarna **för registrering av användare** enhet i loggboken. Finns under **Program och tjänster Logga** > **registrering** > av Microsoft**Windows-användaresenhet** **Windows** > 
2. Leta efter händelser med följande eventID 305

![Händelse för fellogg](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Konfigurationsfel

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Orsak: Autentiseringsprotokoll är inte WS-Trust.
   - Lösning: Den lokala identitetsprovidern måste ha stöd för WS-Trust 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Orsak: Lokal federationstjänst returnerade inte ett XML-svar.
   - Lösning: Se till att MEX-slutpunkten returnerar en giltig XML. Kontrollera att proxyn inte stör och returnerar icke-xml-svar.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Orsak: Det gick inte att identifiera slutpunkten för autentisering av användarnamn/lösenord.
   - Lösning: Kontrollera inställningarna för den lokala identitetsprovidern. Kontrollera att WS-Trust-slutpunkterna är aktiverade och se till att MEX-svaret innehåller rätt slutpunkter.

##### <a name="network-errors"></a>Nätverksfel

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Orsak: Allmän tidsgränsen för nätverket.
   - Lösning: Se `https://login.microsoftonline.com` till att det är tillgängligt i SYSTEM-kontexten. Se till att den lokala identitetsprovidern är tillgänglig i SYSTEM-kontexten. Mer information finns i [Krav på nätverksanslutning](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Orsak: Anslutningen till auth-slutpunkten avbröts.
   - Lösning: Försök igen efter någon gång eller försök att ansluta från en alternativ stabil nätverksplats.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Orsak: Det gick inte att validera certifikatet för transportlagersäkerhet (TLS), som tidigare kallades SSL (Secure Sockets Layer), som skickades av servern.
   - Lösning: Kontrollera klienttidssnedgången. Försök igen efter någon gång eller försök att ansluta från en alternativ stabil nätverksplats. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Orsak: Försöket att `https://login.microsoftonline.com` ansluta till misslyckades.
   - Lösning: Kontrollera nätverksanslutningen till `https://login.microsoftonline.com`.

##### <a name="other-errors"></a>Andra fel

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Orsak: SAML-token från den lokala identitetsprovidern accepterades inte av Azure AD.
   - Lösning: Kontrollera inställningarna för federationsservern. Leta efter serverfelkoden i autentiseringsloggarna.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Orsak: Server WS-Trust svar rapporterade fel undantag och det misslyckades med att få påstående
   - Lösning: Kontrollera inställningarna för federationsservern. Leta efter serverfelkoden i autentiseringsloggarna.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Orsak: Tog emot ett fel när du försökte hämta åtkomsttoken från tokenslutpunkten.
   - Lösning: Leta efter det underliggande felet i ADAL-loggen. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Orsak: Allmänt ADAL misslyckande
   - Lösning: Leta efter underfelskoden eller serverfelkoden från autentiseringsloggarna.
    
#### <a name="join-phase"></a>Gå med i fas

Orsaker till misslyckande:

Hitta registreringstypen och leta efter felkoden i listan nedan.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 och senare

Leta efter underavsnittet "Föregående registrering" i avsnittet Diagnostikdata i utdata för kopplingsstatus. Det här avsnittet visas bara om enheten är domänansluten och inte kan hybrid Azure AD-anslutning.
Fältet Registreringstyp anger vilken typ av koppling som utförs.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Äldre Windows 10-versioner

Använd Loggboksloggar för att hitta fas- och felkoden för kopplingsfelen.

1. Öppna händelseloggarna **för registrering av användare** enhet i loggboken. Finns under **Program och tjänster Logga** > **registrering** > av Microsoft**Windows-användaresenhet** **Windows** > 
2. Leta efter händelser med följande event-ID 204

![Händelse för fellogg](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>HTTP-fel som returnerats från DRS-servern

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Orsak: Fick ett felmeddelande från DRS med ErrorCode: "DirectoryError"
   - Lösning: Se serverns felkod av möjliga orsaker och lösningar.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Orsak: Fick ett felmeddelande från DRS med ErrorCode: "AuthenticationError" och ErrorSubCode är INTE "DeviceNotFound". 
   - Lösning: Se serverns felkod av möjliga orsaker och lösningar.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Orsak: Fick ett felmeddelande från DRS med ErrorCode: "DirectoryError"
   - Lösning: Se serverns felkod av möjliga orsaker och lösningar.

##### <a name="tpm-errors"></a>TPM-fel

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Orsak: TPM-åtgärden misslyckades eller var ogiltig
   - Upplösning: Troligen på grund av en dålig sysprep-bild. Kontrollera att datorn som sysprep-avbildningen skapades från inte är Azure AD-ansluten, hybrid Azure AD-ansluten eller Azure AD registrerad.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Orsak: Generiskt TPM-fel. 
   - Lösning: Inaktivera TPM på enheter med det här felet. Windows 10 version 1809 and higher automatically detects TPM failures and completes hybrid Azure AD join without using the TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Orsak: TPM i FIPS-läge stöds inte för närvarande.
   - Lösning: Inaktivera TPM på enheter med det här felet. Windows 1809 automatically detects TPM failures and completes hybrid Azure AD join without using the TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Orsak: TPM utelåst.
   - Lösning: Övergående fel. Vänta på cooldown perioden. Gå med försök efter en tid bör lyckas. Mer information finns i artikeln [TPM fundamentals](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Nätverksfel

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Orsak: Allmän nätverks time out försöker registrera enheten på DRS
   - Lösning: Kontrollera nätverksanslutningen till `https://enterpriseregistration.windows.net`.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Orsak: Det gick inte att matcha serverns namn eller adress.
   - Lösning: Kontrollera nätverksanslutningen till `https://enterpriseregistration.windows.net`. Kontrollera att DNS-lösningen för värdnamnet är korrekt i n/w och på enheten.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Orsak: Anslutningen till servern avslutades onormalt.
   - Lösning: Försök igen efter någon gång eller försök att ansluta från en alternativ stabil nätverksplats.

##### <a name="federated-join-server-errors"></a>Federerade anslutningsserverfel

| Serverfelkod | Felmeddelande för servern | Möjliga orsaker | Lösning |
| --- | --- | --- | --- |
| Katalogerror | Din begäran begränsas tillfälligt. Försök efter 300 sekunder. | Förväntat fel. Möjligen på grund av att göra flera registreringsförfrågningar i snabb följd. | Försök ansluta igen efter cooldown-perioden |

##### <a name="sync-join-server-errors"></a>Synkronisera anslutningsserverfel

| Serverfelkod | Felmeddelande för servern | Möjliga orsaker | Lösning |
| --- | --- | --- | --- |
| Katalogerror | AADSTS90002: <UUID> Klienten hittades inte. Det här felet kan inträffa om det inte finns några aktiva prenumerationer för klienten. Kontakta prenumerationsadministratören. | Klient-ID i SCP-objektet är felaktigt | Kontrollera att SCP-objektet är konfigurerat med rätt Azure AD-klient-ID och aktiva prenumerationer och finns i klienten. |
| Katalogerror | Det gick inte att hitta enhetsobjektet med det angivna ID:et. | Förväntat fel för synkroniseringskoppling. Enhetsobjektet har inte synkroniserats från AD till Azure AD | Vänta tills Synkroniseringen för Azure AD Connect har slutförts och nästa anslutningsförsök efter slutförande av synkroniseringen löser problemet |
| AuthenticationError | Kontroll av måldatorns SID | Certifikatet på Azure AD-enheten matchar inte certifikatet som används för att signera blobben under synkroniseringskopplingen. Det här felet innebär vanligtvis att synkroniseringen inte har slutförts ännu. |  Vänta tills Synkroniseringen för Azure AD Connect har slutförts och nästa anslutningsförsök efter slutförande av synkroniseringen löser problemet |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Steg 5: Samla in loggar och kontakta Microsoft Support

Få offentliga skript här: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Öppna en kommandotolk `start_ngc_tracing_public.cmd`för administratörer och kör .
2. Utför stegen för att återskapa problemet.
3. Sluta köra loggningsskriptet `stop_ngc_tracing_public.cmd`genom att köra .
4. Zip och skicka loggarna under `%SYSTEMDRIVE%\TraceDJPP\*` för analys.

## <a name="troubleshoot-post-join-issues"></a>Felsöka problem efter anslutning

### <a name="retrieve-the-join-status"></a>Hämta anslutningsstatus 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: JA och AzureADPrt: JA
  
Dessa fält anger om användaren har autentiserats till Azure AD när han loggar in på enheten. Om värdena är **NEJ**kan det bero på:

- Dålig lagringsnyckel i TPM:en som är associerad med enheten vid registrering (kontrollera KeySignTest när du kör förhöjd).
- Alternativt inloggnings-ID
- HTTP-proxy hittades inte

## <a name="known-issues"></a>Kända problem
- Under Inställningar -> Konton -> Access Work eller School kan Hybrid Azure AD-anslutna enheter visa två olika konton, ett för Azure AD och ett för lokala AD, när de är anslutna till mobila hotspots eller externa WiFi-nätverk. Detta är bara ett gränssnittsproblem och har ingen inverkan på funktionaliteten. 
 
## <a name="next-steps"></a>Nästa steg

Fortsätt [felsöka enheter med kommandot dsregcmd](troubleshoot-device-dsregcmd.md)

Frågor om enhetshantering finns i vanliga frågor om [enhetshantering](faq.md)
