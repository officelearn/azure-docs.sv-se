---
title: 'Azure AD Connect: Använd en SAML 2.0-identitetsprovider för enkel inloggning – Azure'
description: Det här dokumentet beskriver hur du använder ett SAML 2.0-kompatibelt Idp för enkel inloggning.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4f1b81f476159c13d3725cd3cb1766aec7dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70305104"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Använda en SAML 2.0-identitetsprovider (IdP) för enkel inloggning

Det här dokumentet innehåller information om hur du använder en SAML 2.0-kompatibel SP-Lite-profilbaserad identitetsprovider som önskad STS -tjänst (Security Token Service) /identity provider. Det här scenariot är användbart när du redan har en användarkatalog och ett lösenordsarkiv lokalt som kan nås med SAML 2.0. Den här befintliga användarkatalogen kan användas för inloggning till Office 365 och andra Azure AD-skyddade resurser. SAML 2.0 SP-Lite-profilen är baserad på den allmänt använda SAML-standarden (Security Assertion Markup Language) för att tillhandahålla ett ramverk för inloggning och attribututbyte.

>[!NOTE]
>En lista över Id-adresser från tredje part som har testats för användning med Azure AD finns i [kompatibilitetslistan för Azure AD-federationen](how-to-connect-fed-compatibility.md)

Microsoft stöder den här inloggningsupplevelsen som integrering av en Microsoft-molntjänst, till exempel Office 365, med din korrekt konfigurerade SAML 2.0-profilbaserad IdP. SAML 2.0-identitetsleverantörer är tredjepartsprodukter och därför tillhandahåller Microsoft inte stöd för distribution, konfiguration, felsökning av bästa praxis för dem. När den är korrekt konfigurerad kan integreringen med SAML 2.0-identitetsprovidern testas för korrekt konfiguration med hjälp av Microsoft Connectivity Analyzer Tool, som beskrivs mer i detalj nedan. Om du vill ha mer information om din PROFILBASERAD IDENTITETSLEVERANTÖR SAML 2.0 SP-Lite ber du organisationen som tillhandahöll den.

> [!IMPORTANT]
> Endast en begränsad uppsättning klienter är tillgängliga i det här inloggningsscenariot med SAML 2.0-identitetsleverantörer, detta inkluderar:
> 
> - Webbaserade klienter som Outlook Web Access och SharePoint Online
> - E-postrika klienter som använder grundläggande autentisering och en Exchange-åtkomstmetod som stöds, till exempel IMAP, POP, Active Sync, MAPI osv. (slutpunkten för utökat klientprotokoll krävs för att distribueras), inklusive:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (olika iOS-versioner)
>     - Olika Android-enheter på Google
>     - Windows Phone 7, Windows Phone 7.8 och Windows Phone 8.0
>     - Windows 8 Mail Client och Windows 8.1 Mail Client
>     - Windows 10 E-postklient

Alla andra klienter är inte tillgängliga i det här inloggningsscenariot med din SAML 2.0-identitetsprovider. Lync 2010-klienten kan till exempel inte logga in på tjänsten med din SAML 2.0-identitetsprovider konfigurerad för enkel inloggning.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Azure AD SAML 2.0-protokollkrav
Det här dokumentet innehåller detaljerade krav på protokoll- och meddelandeformatering som din SAML 2.0-identitetsprovider måste implementera för att federera med Azure AD för att aktivera inloggning till en eller flera Microsoft-molntjänster (till exempel Office 365). Den SAML 2.0-förlitande parten (SP-STS) för en Microsoft-molntjänst som används i det här scenariot är Azure AD.

Vi rekommenderar att du ser till att dina UTDATAMEDDELANDEN för SAML 2.0-identitetsprovidern är så lika som de medföljande provspårningarna som möjligt. Använd också specifika attributvärden från de medföljande Azure AD-metadata där det är möjligt. När du är nöjd med dina utdatameddelanden kan du testa med Microsoft Connectivity Analyzer enligt beskrivningen nedan.

Azure AD-metadata kan hämtas från [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml)den här URL:en: .
För kunder i Kina som använder den Kina-specifika instansen av Office 365 bör följande federationsslutpunkt användas: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>KRAV för SAML-protokoll
I det här avsnittet beskrivs hur meddelandeparen för begäran och svar sätts ihop för att hjälpa dig att formatera meddelandena korrekt.

Azure AD kan konfigureras för att fungera med identitetsleverantörer som använder SAML 2.0 SP Lite-profilen med vissa specifika krav som anges nedan. Med hjälp av exempelmeddelanden för SAML-begäran och svar tillsammans med automatiserade och manuella tester kan du arbeta för att uppnå interoperabilitet med Azure AD.

## <a name="signature-block-requirements"></a>Krav på signaturblock
I SAML-svarsmeddelandet innehåller signaturnoden information om den digitala signaturen för själva meddelandet. Signaturblocket har följande krav:

1. Själva kontrollnoden måste signeras
2.  RSA-sha1-algoritmen måste användas som DigestMethod. Andra algoritmer för digital signatur accepteras inte.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Du kan också signera XML-dokumentet. 
4.  Transform-algoritmen måste matcha värdena i följande exempel:`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  SignatureMethod-algoritmen måste matcha följande exempel:`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Bindningar som stöds
Bindningar är de transportrelaterade kommunikationsparametrar som krävs. Följande krav gäller för bindningarna

1. HTTPS är den transport som krävs.
2.  Azure AD kräver HTTP POST för tokenöverföring under inloggning.
3.  Azure AD använder HTTP POST för autentiseringsbegäran till identitetsprovidern och OMDIRIGERA för ut logga ut-meddelandet till identitetsprovidern.

## <a name="required-attributes"></a>Obligatoriska attribut
I den här tabellen visas krav för specifika attribut i SAML 2.0-meddelandet.
 
|Attribut|Beskrivning|
| ----- | ----- |
|NameID|Värdet för det här påståendet måste vara detsamma som Azure AD-användarens ImmutableID. Det kan vara upp till 64 alfanumeriska tecken. Alla säkra tecken som inte är html måste kodas, till exempel visas ett "+"-tecken som ".2B".|
|IDPEmail|Upn (User Principal Name) visas i SAML-svaret som ett element med namnet IDPEmail Användarens UserPrincipalName (UPN) i Azure AD/Office 365. UPN är i e-postadressformat. UPN-värde i Windows Office 365 (Azure Active Directory).|
|Utfärdare|Måste vara en URI för identitetsleverantören. Återanvänd inte Utfärdaren från exempelmeddelandena. Om du har flera toppdomäner i dina Azure AD-klienter måste Utfärdaren matcha den angivna URI-inställningen som konfigurerats per domän.|

>[!IMPORTANT]
>Azure AD stöder för närvarande följande NameID-format URI för SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-format:persistent.

## <a name="sample-saml-request-and-response-messages"></a>Exempel på MEDDELANDEN om SAML-begäran och svar
Ett meddelandepar för begäran och svar visas för meddelandet för inloggning.
Följande är ett exempel begäran meddelande som skickas från Azure AD till ett exempel SAML 2.0 identitetsprov provider. Exempelleverantören SAML 2.0 är AD FS (Active Directory Federation Services) konfigurerat för att använda SAML-P-protokollet. Interoperabilitetstestning har också slutförts med andra SAML 2.0-identitetsleverantörer.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Följande är ett exempel på svarsmeddelande som skickas från exempelleverantören SAML 2.0 till Azure AD / Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurera din SAML 2.0-kompatibel identitetsprovider
Det här avsnittet innehåller riktlinjer för hur du konfigurerar din SAML 2.0-identitetsprovider för att federera med Azure AD för att aktivera enkel inloggning till en eller flera Microsoft-molntjänster (till exempel Office 365) med SAML 2.0-protokollet. Den SAML 2.0-förlitande parten för en Microsoft-molntjänst som används i det här scenariot är Azure AD.

## <a name="add-azure-ad-metadata"></a>Lägga till Azure AD-metadata
Din SAML 2.0-identitetsprovider måste följa information om azure AD-förlitande part. Azure AD publicerar https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlmetadata på .

Vi rekommenderar att du alltid importerar de senaste Azure AD-metadata när du konfigurerar din SAML 2.0-identitetsprovider.

>[!NOTE]
>Azure AD läser inte metadata från identitetsprovidern.

## <a name="add-azure-ad-as-a-relying-party"></a>Lägga till Azure AD som en förlitande part
Du måste aktivera kommunikation mellan din SAML 2.0-identitetsprovider och Azure AD. Den här konfigurationen är beroende av din specifika identitetsprovider och du bör läsa dokumentation för den. Du anger vanligtvis det förlitande part-ID:et till samma som entityID från Azure AD-metadata.

>[!NOTE]
>Kontrollera att klockan på saml 2.0-identitetsproviderns server är synkroniserad med en korrekt tidskälla. En felaktig klocktid kan orsaka federerade inloggningar att misslyckas.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Installera Windows PowerShell för inloggning med SAML 2.0-identitetsprovider
När du har konfigurerat din SAML 2.0-identitetsprovider för användning med Azure AD-inloggning är nästa steg att hämta och installera Azure Active Directory Module för Windows PowerShell. När du har installerat kommer du att använda dessa cmdlets för att konfigurera dina Azure AD-domäner som federerade domäner.

Azure Active Directory Module för Windows PowerShell är en nedladdning för att hantera dina organisationers data i Azure AD. Den här modulen installerar en uppsättning cmdlets i Windows PowerShell. du kör dessa cmdlets för att ställa in enkel inloggningsåtkomst till Azure AD och i sin tur till alla molntjänster som du prenumererar på. Instruktioner om hur du hämtar och installerar cmdlets finns i[https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Konfigurera ett förtroende mellan din SAML-identitetsprovider och Azure AD
Innan du konfigurerar federationen på en Azure AD-domän måste den ha en anpassad domän konfigurerad. Du kan inte federera standarddomänen som tillhandahålls av Microsoft. Standarddomänen från Microsoft slutar med "onmicrosoft.com".
Du kör en serie cmdlets i kommandoradsgränssnittet i Windows PowerShell för att lägga till eller konvertera domäner för enkel inloggning.

Varje Azure Active Directory-domän som du vill federera med din SAML 2.0-identitetsprovider måste antingen läggas till som en enda inloggningsdomän eller konverteras till en enda inloggningsdomän från en standarddomän. Om du lägger till eller konverterar en domän skapas ett förtroende mellan din SAML 2.0-identitetsprovider och Azure AD.

Följande procedur går igenom konvertering av en befintlig standarddomän till en federerad domän med SAML 2.0 SP-Lite. 

>[!NOTE]
>Din domän kan uppleva ett avbrott som påverkar användare upp till 2 timmar efter att du har tagit det här steget.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurera en domän i Din Azure AD-katalog för federation


1. Anslut till din Azure AD-katalog som klientadministratör: Connect-MsolService .
2.  Konfigurera önskad Office 365-domän så att federationen används med SAML 2.0:`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $BrandName -Authentication Federated -PassiveLogOnUri $LogOnUrl -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $MyURI -LogOffUri $LogOffUrl -PreferredAuthenticationProtocol $Protocol` 

3.  Du kan hämta signeringscertifikatbas64-kodad sträng från din IDP-metadatafil. Ett exempel på den här platsen har angetts men kan skilja sig något beroende på din implementering.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Mer information om "Set-MsolDomainAuthentication" [https://technet.microsoft.com/library/dn194112.aspx](https://technet.microsoft.com/library/dn194112.aspx)finns i: .

>[!NOTE]
>Du får `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` bara köra användning om du ställer in ett ECP-tillägg för din identitetsprovider. Exchange Online-klienter, exklusive Outlook Web Application (OWA), förlitar sig på en POST-baserad aktiv slutpunkt. Om din SAML 2.0 STS implementerar en aktiv slutpunkt som liknar Shibboleths ECP-implementering av en aktiv slutpunkt kan det vara möjligt för dessa rika klienter att interagera med Exchange Online-tjänsten.

När federationen har konfigurerats kan du växla tillbaka till "icke-federerade" (eller "hanterad"), men den här ändringen tar upp till två timmar att slutföra och det kräver att nya slumpmässiga lösenord tilldelas för molnbaserad inloggning till varje användare. Det kan krävas att du byter tillbaka till "hanterad" i vissa fall för att återställa ett fel i inställningarna. Mer information om domänkonvertering finns i: [https://msdn.microsoft.com/library/windowsazure/dn194122.aspx](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Etablera användarobjekt för Azure AD / Office 365
Innan du kan autentisera dina användare till Office 365 måste du etablera Azure AD med användarobjekt som motsvarar påståendet i SAML 2.0-anspråket. Om dessa användarobjekt inte är kända för Azure AD i förväg kan de inte användas för federerade inloggningar. Antingen Azure AD Connect eller Windows PowerShell kan användas för att etablera användarobjekt.

Azure AD Connect kan användas för att etablera huvudnamn till dina domäner i din Azure AD-katalog från den lokala Active Directory. Mer detaljerad information finns i [Integrera dina lokala kataloger med Azure Active Directory](whatis-hybrid-identity.md).

Windows PowerShell kan också användas för att automatisera lägger till nya användare i Azure AD och för att synkronisera ändringar från den lokala katalogen. Om du vill använda Windows PowerShell-cmdletar måste du hämta [Azure Active Directory-moduler .](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0)

Den här proceduren visar hur du lägger till en enskild användare i Azure AD.


1. Anslut till din Azure AD-katalog som klientadministratör: Connect-MsolService.
2.  Skapa ett nytt användarobjektnamn:
    ```powershell
    New-MsolUser
      -UserPrincipalName elwoodf1@contoso.com
      -ImmutableId ABCDEFG1234567890
      -DisplayName "Elwood Folk"
      -FirstName Elwood 
      -LastName Folk 
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
      -UsageLocation "US" 
    ```

För mer information om "New-MsolUser" kassan,[https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>Värdet "UserPrinciplName" måste matcha det värde som du skickar för "IDPEmail" i ditt SAML 2.0-anspråk och värdet "ImmutableID" måste matcha värdet som skickas i ditt "NameID"-påstående.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Verifiera enkel inloggning med SAML 2.0 IDP
Som administratör, innan du verifierar och hanterar enkel inloggning (kallas även identitetsfederation), granska informationen och utför stegen i följande artiklar för att konfigurera enkel inloggning med din SAML 2.0 SP-baserad identitetsprovider:


1.  Du har granskat Azure AD SAML 2.0-protokollkraven
2.  Du har konfigurerat din SAML 2.0-identitetsprovider
3.  Installera Windows PowerShell för enkel inloggning med SAML 2.0-identitetsprovider
4.  Konfigurera ett förtroende mellan SAML 2.0-identitetsprovidern och Azure AD
5.  Etablerat ett känt testanvändarobjektnamn till Azure Active Directory (Office 365) antingen via Windows PowerShell eller Azure AD Connect.
6.  Konfigurera katalogsynkronisering med [Azure AD Connect](whatis-hybrid-identity.md).

När du har konfigurerat enkel inloggning med din SAML 2.0 SP-Lite-baserade identitetsprovider bör du kontrollera att den fungerar korrekt.

>[!NOTE]
>Om du har konverterat en domän i stället för att lägga till en, kan det ta upp till 24 timmar att konfigurera enkel inloggning.
Innan du verifierar enkel inloggning bör du slutföra inrättandet av Active Directory-synkronisering, synkronisera katalogerna och aktivera synkroniserade användare.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Använd verktyget för att kontrollera att enkel inloggning har ställts in korrekt
Om du vill kontrollera att enkel inloggning har konfigurerats korrekt kan du utföra följande procedur för att bekräfta att du kan logga in på molntjänsten med dina företagsautentiseringsuppgifter.

Microsoft har tillhandahållit ett verktyg som du kan använda för att testa din SAML 2.0-baserade identitetsleverantör. Innan du kör testverktyget måste du ha konfigurerat en Azure AD-klientorganisation för att federera med din identitetsprovider.

>[!NOTE]
>Anslutningsanalysen kräver Internet Explorer 10 eller senare.



1. Ladda ner Connectivity Analyzer från, [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Klicka på Installera nu om du vill börja hämta och installera verktyget.
3.  Välj "Jag kan inte konfigurera federation med Office 365, Azure eller andra tjänster som använder Azure Active Directory".
4.  När verktyget har hämtats och körts visas fönstret Anslutningsdiagnostik. Verktyget kommer att gå igenom att testa din federationsanslutning.
5.  Connectivity Analyzer öppnar din SAML 2.0 IDP för att du ska logga in, ![ange autentiseringsuppgifterna för användarens huvudnamn som du testar: SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  I fönstret Federationstest inloggning bör du ange ett kontonamn och lösenord för Azure AD-klienten som är konfigurerad för att federeras med din SAML 2.0-identitetsprovider. Verktyget kommer att försöka logga in med hjälp av dessa autentiseringsuppgifter och detaljerade resultat av tester som utförts under inloggningsförsöket kommer att tillhandahållas som utdata.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Det här fönstret visar ett misslyckat resultat av testning. Om du klickar på Granska detaljerade resultat visas information om resultaten för varje test som utfördes. Du kan också spara resultaten på disken för att dela dem.
 
>[!NOTE]
>Anslutningsanalysatorn testar också Active Federation med WS*-baserade och ECP/PAOS-protokoll. Om du inte använder dessa kan du bortse från följande fel: Testa det aktiva inloggningsflödet med hjälp av identitetsleverantörens active federation-slutpunkt.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Kontrollera manuellt att enkel inloggning har ställts in korrekt
Manuell verifiering innehåller ytterligare åtgärder som du kan vidta för att säkerställa att din SAML 2.0-identitetsprovider fungerar korrekt i många scenarier.
Så här kontrollerar du att enkel inloggning har konfigurerats korrekt:


1. På en domänansluten dator loggar du in på molntjänsten med samma inloggningsnamn som du använder för dina företagsautentiseringsuppgifter.
2.  Klicka i lösenordsrutan. Om enkel inloggning är konfigurerad kommer lösenordsrutan att skuggas och följande meddelande visas: "Du måste &lt;nu&gt;logga in på ditt företag ."
3.  Klicka på länken &lt;Logga&gt; in på företagets företagslänk. Om du kan logga in har enkel inloggning ställts in.

## <a name="next-steps"></a>Efterföljande moment


- [Hantering och anpassning av Active Directory Federation Services med Azure AD Connect](how-to-connect-fed-management.md)
- [Kompatibilitetslista för Azure AD-federation](how-to-connect-fed-compatibility.md)
- [Anpassad installation i Azure AD Connect](how-to-connect-install-custom.md)
