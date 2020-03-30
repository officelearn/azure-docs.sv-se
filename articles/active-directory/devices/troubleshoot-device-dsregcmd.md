---
title: Felsöka med kommandot dsregcmd - Azure Active Directory
description: Använda utdata från dsregcmd för att förstå tillståndet för enheter i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128766"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Felsöka enheter med kommandot dsregcmd

Verktyget dsregcmd /status måste köras som ett domänanvändarkonto.

## <a name="device-state"></a>Enhetstillstånd

I det här avsnittet visas tillståndsparametrarna för enhetskoppling. I tabellen nedan visas kriterierna för att enheten ska vara i olika kopplingstillstånd.

| AzureAdJoined | FöretagJoined | DomänJoninerad | Enhetstillstånd |
| ---   | ---   | ---   | ---   |
| JA | NO | NO | Azure AD-ansluten |
| NO | NO | JA | Domänen har anslutit |
| JA | NO | JA | Hybrid AD-ansluten |
| NO | JA | JA | Lokala DRS Förenade |

> [!NOTE]
> Tillståndet arbetsplatsanslutning (Azure AD-registrerad) visas i avsnittet Användartillstånd

- **AzureAdJoined:** - Ange till "JA" om enheten är ansluten till Azure AD. "NEJ" annars.
- **EnterpriseJoined:** - Ställ in på "JA" om enheten är ansluten till en lokal DRS. En enhet kan inte vara både EnterpriseJoined och AzureAdJoined.
- **DomainJoined:** - Ställ in på "JA" om enheten är ansluten till en domän (AD).
- **Domännamn:** - Ange namnet på domänen om enheten är ansluten till en domän.

### <a name="sample-device-state-output"></a>Exempel på enhetstillståndsutdata

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Information om enhet

Visas endast när enheten är Azure AD-ansluten eller hybrid Azure AD-ansluten (inte Azure AD registrerad). I det här avsnittet visas information om enhetsidentifiering som lagras i molnet.

- **DeviceId:** - Unikt ID för enheten i Azure AD-klienten
- **Tumavtryck:** - Tumavtryck för enhetscertifikatet 
- **DeviceCertificateValidity:** - Enhetscertifikatets giltighet
- **KeyContainerId:** - ContainerId för enhetens privata nyckel som är associerad med enhetscertifikatet
- **KeyProvider:** - KeyProvider (Hårdvara/Mjukvara) som används för att lagra enhetens privata nyckel.
- **TpmSkyddad:** - "JA" om enhetens privata nyckel lagras i en maskinvaru-TPM.

### <a name="sample-device-details-output"></a>Exempel på enhetsinformation utdata

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Information om klientorganisation

Visas endast när enheten är Azure AD-ansluten eller hybrid Azure AD-ansluten (inte Azure AD registrerad). I det här avsnittet visas den vanliga klientinformationen när en enhet ansluts till Azure AD.

> [!NOTE]
> Om MDM-url:erna i det här avsnittet är tomma anger det att MDM antingen inte har konfigurerats eller att den aktuella användaren inte omfattas av MDM-registrering. Kontrollera mobilitetsinställningarna i Azure AD för att granska din MDM-konfiguration.

> [!NOTE]
> Även om du ser MDM-url:er betyder det inte att enheten hanteras av en MDM. Informationen visas om klienten har MDM-konfiguration för automatisk registrering även om själva enheten inte hanteras. 

### <a name="sample-tenant-details-output"></a>Exempel på utdata för klientinformation

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Användartillstånd

I det här avsnittet visas status för olika attribut för den användare som för närvarande är inloggad på enheten.

> [!NOTE]
> Kommandot måste köras i en användarkontext för att kunna hämta giltig status.

- **NgcSet:** - Ställ in på "JA" om en Windows Hello-nyckel är inställd för den aktuella inloggade användaren.
- **NgcKeyId:** - ID för Windows Hello-tangenten om en är inställd för den aktuella inloggade användaren.
- **CanReset:** - Anger om Windows Hello-tangenten kan återställas av användaren. 
- **Möjliga värden:** - DestruktivtOnly, NonDestructiveOnly, DestruktivandNonDestructive eller Okänd om fel. 
- **WorkplaceJoined:** - Ange till "JA" om Azure AD-registrerade konton har lagts till i enheten i den aktuella NTUSER-kontexten.
- **WamDefaultSet:** - Ange till "JA" om en WAM-standardwebbkonto skapas för den inloggade användaren. Det här fältet kan visa ett fel om dsreg /status körs från en upphöjd kommandotolk. 
- **WamDefaultAuthority:** - Ange till "organisationer" för Azure AD.
- **WamDefaultId:** -https://login.microsoft.comAlltid " " för Azure AD.
- **WamDefaultGUID:** - WAM-providerns (Azure AD/Microsoft-konto) GUID för standard-WAM WebAccount. 

### <a name="sample-user-state-output"></a>Exempel på utdata för användartillstånd

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO-tillstånd

Det här avsnittet kan ignoreras för Azure AD-registrerade enheter.

> [!NOTE]
> Kommandot måste köras i en användarkontext för att hämta giltig status för den användaren.

- **AzureAdPrt:** - Ange till "JA" om det finns en PRT på enheten för den inloggade användaren.
- **AzureAdPrtUpdateTime:** - Ställ in på tiden i UTC när PRT senast uppdaterades.
- **AzureAdPrtExpiryTime:** - Ställ in på tiden i UTC när PRT:n upphör att gälla om den inte förnyas.
- **AzureAdPrtAuthority:** - URL för Azure AD-behörighet
- **EnterprisePrt:** - Ställ in på "JA" om enheten har PRT från lokala ADFS. För hybrid Azure AD-anslutna enheter kan enheten ha PRT från både Azure AD och lokalt AD samtidigt. Lokala anslutna enheter har bara en Enterprise PRT.
- **EnterprisePrtUpdateTime:** - Ställ in på tiden i UTC när Enterprise PRT senast uppdaterades.
- **EnterprisePrtExpiryTime:** - Ställ in tiden i UTC när PRT:n upphör att gälla om den inte förnyas.
- **EnterprisePrtAuthority:** - URL för ADFS-myndighet

### <a name="sample-sso-state-output"></a>Exempel på SSO-tillståndsutdata

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Diagnostikdata

### <a name="pre-join-diagnostics"></a>Förkoppling diagnostik

Det här avsnittet visas bara om enheten är domänansluten och inte kan hybrid Azure AD-anslutning.

Det här avsnittet utför olika tester för att diagnostisera kopplingsfel. Detta avsnitt innehåller också information om den tidigare (?). Den här informationen omfattar felfasen, felkoden, serverbegärande-ID, serversvar http-status, felmeddelandet för serversvar.

- **Användarkontext:** - Den kontext där diagnostiken körs. Möjliga värden: SYSTEM, UN-FÖRHÖJD användare, förhöjd användare. 

   > [!NOTE]
   > Eftersom den faktiska kopplingen utförs i SYSTEM-kontexten är körningen diagnostiken i SYSTEM-kontexten närmast det faktiska kopplingsscenariot. Om du vill köra diagnostik i SYSTEM-kontexten måste kommandot dsregcmd /status köras från en upphöjd kommandotolk.

- **Klienttid:** - Systemtiden i UTC.
- **AD-anslutningstest:** - Test utför ett anslutningstest till domänkontrollanten. Fel i det här testet kommer sannolikt att resultera i kopplingsfel i fasen före kontrollen.
- **AD-konfigurationstest:** - Testa läser och verifierar om SCP-objektet är korrekt konfigurerat i den lokala AD-skogen. Fel i det här testet skulle sannolikt resultera i kopplingsfel i identifieringsfasen med felkoden 0x801c001d.
- **DRS Discovery Test:** - Testet hämtar DRS-slutpunkter från identifieringsmetadataslutpunkten och utför en användarskapsbegäran. Fel i det här testet skulle sannolikt resultera i kopplingsfel i identifieringsfasen.
- **DRS-anslutningstest:** - Test utför grundläggande anslutningstest till DRS-slutpunkten.
- **Token acquisition Test:** - Test försöker hämta en Azure AD-autentiseringstoken om användarens klientorganisation är federerad. Fel i det här testet skulle sannolikt resultera i kopplingsfel i auth-fasen. Om auth misslyckas synkroniseringskoppling kommer att försöka som reserv, om inte reservåter är uttryckligen inaktiverad med nedan registernyckelinställningarna.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Återgång till Synkronisering-Join:** - Ställ in på "Aktiverad" om ovanstående registernyckel, för att förhindra återgången att synkronisera gå med auth fel, är inte närvarande. Det här alternativet är tillgängligt från Windows 10 1803 och senare.
- **Tidigare registrering:** - Tid då det tidigare kopplingsförsöket inträffade. Endast misslyckade kopplingsförsök loggas.
- **Felfas:** - Det stadium där kopplingen avbröts. Möjliga värden är förkontroll, upptäck, auth, gå med.
- **Client ErrorCode:** - Klientfelkod returneras (HRESULT).
- **ServerfelKoda:** - Serverfelkod om en begäran skickades till servern och servern svarade tillbaka med en felkod. 
- **Servermeddelande:** - Servermeddelandet returneras tillsammans med felkoden.
- **Https Status:** - Http-status som returneras av servern.
- **Begärande-ID:** - KlientbegäranId skickas till servern. Användbart att korrelera med serverloggar.

### <a name="sample-pre-join-diagnostics-output"></a>Exempel på diagnostikutdata före anslutning

I följande exempel visas diagnostiktestet misslyckas med ett identifieringsfel.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

Följande exempel visar diagnostiktester passerar men registreringsförsöket misslyckades med ett katalogfel, vilket förväntas för synkroniseringskoppling. När Azure AD Connect-synkroniseringsjobbet är klart kan enheten ansluta.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

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

### <a name="post-join-diagnostics"></a>Diagnostik efter anslutning

I det här avsnittet visas utdata från sanity-kontroller som utförs på en enhet som är ansluten till molnet.

- **AadRecoveryEnabled:** - Om "JA" är nycklarna som lagras i enheten inte användbara och enheten är markerad för återställning. Nästa inloggning utlöser återställningsflödet och registrerar enheten igen.
- **KeySignTest:** - Om "PASSED" enhetens nycklar är vid god hälsa. Om KeySignTest misslyckas markeras enheten vanligtvis för återställning. Nästa inloggning utlöser återställningsflödet och registrerar enheten igen. För hybrid Azure AD-anslutna enheter är återställningen tyst. Medan Azure AD gick med eller Azure AD-registrerade, kommer enheter att fråga efter användarautentisering för att återställa och registrera om enheten om det behövs. **KeySignTest kräver förhöjda privilegier.**

#### <a name="sample-post-join-diagnostics-output"></a>Exempel på diagnostikutdata efter anslutning

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC förutsättning kontroll

I det här avsnittet utförs de erforderliga kontrollerna för etablering av Windows Hello for Business (WHFB). 

> [!NOTE]
> Du kanske inte ser NGC-förundersökningsinformation i dsregcmd /status om användaren redan har konfigurerat WHFB.

- **IsDeviceJoined:** - Ställ in på "JA" om enheten är ansluten till Azure AD.
- **IsUserAzureAD:** - Ställ in på "JA" om den inloggade användaren finns i Azure AD .
- **PolicyEnabled:** - Ställ in på "JA" om WHFB-principen är aktiverad på enheten.
- **PostLogonEnabled:** - Ställ in på "JA" om WHFB-registrering utlöses inbyggt av plattformen. Om den är inställd på "NEJ" betyder det att Registreringen för Windows Hello för företag utlöses av en anpassad mekanism
- **DeviceEligible:** - Ställ in på "JA" om enheten uppfyller maskinvarukravet för registrering med WHFB.
- **SessionIsNotRemote:** - Ställ in på "JA" om den aktuella användaren är inloggad direkt på enheten och inte på distans.
- **CertEnrollment:** - Specifik för DISTRIBUTION AV WHFB-certifikatförtroende, med angivande av certifikatregistreringsutfärdaren för WHFB. Ange till "registreringsmyndighet" om källan till WHFB-principen är Grupprincip, "hantering av mobila enheter" om källan är MDM. "ingen" annars
- **AdfsRefreshToken:** - Specifikt för DISTRIBUTION AV WHFB-certifikatförtroende. Endast om CertEnrollment är "registreringsmyndighet". Anger om enheten har en företags-PRT för användaren.
- **AdfsRaIsReady:** - Specifik för DISTRIBUTION AV WHFB-certifikatförtroende.  Endast om CertEnrollment är "registreringsmyndighet". Ange till "JA" om ADFS anges i identifieringsmetadata att den stöder WHFB *och* om inloggningscertifikatmall är tillgänglig.
- **LogonCertTemplateReady:** - Specifik för DISTRIBUTION AV WHFB-certifikatförtroende. Endast om CertEnrollment är "registreringsmyndighet". Ange till "JA" om inloggningscertifikatmallens tillstånd är giltigt och hjälper till att felsöka ADFS RA.
- **PreReqResult:** - Ger resultat av alla WHFB nödvändig utvärdering. Ange "Kommer-etablera" om WHFB-registrering skulle startas som en post-inloggningsuppgift när användaren loggar in nästa gång.

### <a name="sample-ngc-prerequisite-check-output"></a>Prov NGC nödvändiga kontroll utgång

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Nästa steg

Frågor om enhetshantering finns i vanliga frågor om [enhetshantering](faq.md)
