---
title: 'Azure AD Connect: Använda en SAML-identitetsprovider 2.0 för enkel inloggning på | Microsoft Docs'
description: Det här dokumentet beskriver hur du använder en kompatibel Idp SAML 2.0 för enkel inloggning på.
services: active-directory
author: billmath
manager: mtillman
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 74051ae8ad4ca7065561607feb13a2b98094fffc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333190"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Använda en SAML 2.0-identitetsprovider (IdP) för enkel inloggning på

Det här dokumentet innehåller information om hur du använder en SAML 2.0 kompatibla SP-Lite profil-baserade identitetsleverantör som den prioriterade säkerhet säkerhetstokentjänst (STS) / identitetsleverantör. Det här scenariot är användbart när du redan har ett katalog för användarnamn och lösenord som lagras lokalt som kan nås med SAML 2.0. Den här befintliga användarkatalog kan användas för inloggning till Office 365 och andra Azure AD-skyddade resurser. SAML 2.0 SP-Lite profilen baserat på standarden federerad identitet vanliga Security Assertion Markup Language (SAML) för att ge en inloggning och attributet exchange.

>[!NOTE]
>En lista över 3 part Idps som har testats för användning med Azure AD finns i [kompatibilitetslista för Azure AD-federation](active-directory-aadconnect-federation-compatibility.md)

Microsoft stöder den här inloggning som en Microsoft-molntjänst som Office 365, med en korrekt konfigurerad SAML 2.0 profil-baserade IdP-integrering. SAML 2.0 identitetsleverantörer är produkter från tredje part och därmed Microsoft tillhandahåller inte stöd för att distribuera, konfiguration, felsökning Metodtips om dem. En gång korrekt konfigurerat integrationen med i SAML 2.0 identitetsleverantör kan testas för korrekt konfiguration med hjälp av Microsoft Analyzer anslutningsverktyget, som beskrivs i detalj nedan. Mer information om SAML 2.0 SP-Lite profil-baserade identitetsprovider ber du den organisation som angetts för den.

>[!IMPORTANT]
>Endast en mindre uppsättning klienter är tillgängliga i det här scenariot inloggning med SAML 2.0 identitetsleverantörer, bland annat:

>- Webbaserade klienter, till exempel Outlook Web Access och SharePoint Online
- E-post-RTF-klienter som använder grundläggande autentisering och en Exchange åtkomstmetod som stöds till exempel IMAP-, POP, Active Sync, MAPI, etc. (förbättrad klientprotokoll slutpunkten krävs för distribution), inklusive:
    - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (olika versioner av iOS)
    - Olika Google Android-enheter
    - Windows Phone 7, Windows Phone 7,8 och Windows Phone 8.0
    - E-postklient för Windows 8 och Windows 8.1 e-postklient
    - Windows 10-e-postklient

Alla andra klienter är inte tillgängliga i det här scenariot inloggning med SAML 2.0 identitetsprovider. Till exempel kan Lync 2010 skrivbord klienten inte logga in på tjänsten med SAML 2.0 identitetsprovider konfigurerats för enkel inloggning.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Krav för Azure AD SAML 2.0-protokollet
Det här dokumentet innehåller detaljerade krav på protokoll och meddelandet formatering som identitetsprovider SAML 2.0 måste implementera om du vill federera med Azure AD för att aktivera inloggning till en eller flera Microsoft-molntjänster (till exempel Office 365). SAML 2.0 beroende part (SP-STS) för en Microsoft-molntjänst som används i det här scenariot är Azure AD.

Vi rekommenderar att du säkerställer identitetsprovider SAML 2.0 utgående meddelanden vara så likt angivna exempel spår som möjligt. Använd attributvärden från den angivna Azure AD-metadata om möjligt. När du är nöjd med utgående meddelanden, kan du testa med Microsoft anslutningen Analyzer som beskrivs nedan.

Azure AD-metadata kan hämtas från denna URL: [ https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml ](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
För kunder i Kina i Kina-specifika instansen av Office 365 följande federation slutpunkten ska användas: [ https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml ](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Krav för SAML-protokoll
Det här avsnittet information hur par för förfrågan och svar meddelande placeras tillsammans i för att hjälpa dig att formatera dina meddelanden på rätt sätt.

Azure AD kan konfigureras för att fungera med identitetsleverantörer som använder SAML 2.0 SP Lite profilen med vissa krav enligt nedan. Med exempel SAML förfrågan och svar meddelanden tillsammans med automatisk och manuell testning kan arbeta du för att uppnå kompatibilitet med Azure AD.

## <a name="signature-block-requirements"></a>Krav på signatur block
Noden signaturen innehåller information om den digitala signaturen för själva meddelandet i SAML-svarsmeddelandet. Signaturblocket har följande krav:

1. Noden assertion måste signeras.
2.  Algoritmen RSA-sha1 måste användas som DigestMethod. Andra algoritmer för digital signatur godkänns inte.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Du kan också registrera XML-dokumentet. 
4.  Transformera algoritmen måste matcha värden i följande exempel:    `<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Algoritmen SignatureMethod måste matcha i följande exempel:   `<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Stöds bindningar
Bindningar är transportrelaterade kommunikation parametrar som krävs. Följande krav gäller för bindningar

1. HTTPS är obligatoriska transport.
2.  Azure AD kräver HTTP POST för token som skickas automatiskt vid inloggning
3.  Azure AD använder HTTP POST för autentiseringsbegäran till identitetsleverantör och OMDIRIGERING för utloggning meddelandet till identitetsleverantören.

## <a name="required-attributes"></a>Obligatoriska attribut
Den här tabellen visas kraven för specifika attribut i SAML 2.0-meddelandet.
 
|Attribut|Beskrivning|
| ----- | ----- |
|NameID|Värdet för den här kontrollen måste vara samma som Azure AD-användare ImmutableID. Det kan vara upp till 64 alfanumeriska tecken. Säker tecken icke-HTML-måste kodas, till exempel tecknet ”+” visas som ”.2b”.|
|IDPEmail|Den UPN (User Principal Name) anges i SAML-svaret som ett element med namnet IDPEmail användarens UserPrincipalName (UPN) i Azure AD/Office 365. UPN-namnet är i formatet för e-postadress. UPN-värdet i Windows Office 365 (Azure Active Directory).|
|Utfärdare|Måste vara en URI: N för identitetsleverantören. Återanvänd inte utfärdaren från exemplet meddelanden. Om du har flera domäner på toppnivå i Azure AD-klienter måste utfärdaren matcha den angivna URI-inställningar som konfigureras per domän.|

>[!IMPORTANT]
>Azure AD för närvarande stöder följande NameID Format URI: N för SAML-2.0:urn:oasis:names:tc:SAML:2.0:nameid-format: beständig.

## <a name="sample-saml-request-and-response-messages"></a>Exempel SAML förfrågan och svar-meddelanden
Paret förfrågan och svar meddelandet visas för inloggning meddelandet exchange.
Följande är ett meddelande om begäran exempel som skickas från Azure AD till en exempel SAML 2.0-identitetsleverantör. Identitetsleverantören som exempel SAML 2.0 är Active Directory Federation Services (AD FS) konfigurerats för att använda protokollet SAML-P. Interoperabiliteten är också klar med andra identitetsleverantörer SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Följande är ett exempel svarsmeddelande som skickas från identitetsleverantören exempel SAML 2.0 kompatibla till Azure AD / Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
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

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurera din SAML 2.0 kompatibla identitetsleverantören.
Det här avsnittet innehåller riktlinjer för hur du konfigurerar din SAML 2.0 identitetsleverantör vill federera med Azure AD för att aktivera enkel inloggning-åtkomst till en eller flera Microsoft-molntjänster (till exempel Office 365) med SAML 2.0-protokollet. I SAML 2.0 förlitande part för en Microsoft-molntjänst som används i det här scenariot är Azure AD.

## <a name="add-azure-ad-metadata"></a>Lägga till Azure AD-metadata
Identitetsprovider SAML 2.0 måste följa information om Azure AD förlitande part. Azure AD publicerar metadata på https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Vi rekommenderar att du alltid importera den senaste Azure AD-metadata när du konfigurerar din SAML 2.0-identitetsleverantör.

>[!NOTE]
>Azure AD läser inte metadata från identitetsleverantören.

## <a name="add-azure-ad-as-a-relying-party"></a>Lägg till Azure AD som en förlitande part
Du måste aktivera kommunikationen mellan SAML 2.0 identitetsleverantör och Azure AD. Den här konfigurationen är beroende av specifika identitetsprovider och du bör se dokumentationen för den. Du skulle vanligtvis ange det förlitande part-ID: T till samma som det ID för entiteterna från Azure AD-metadata.

>[!NOTE]
>Kontrollera att klockan på serverns SAML 2.0 identitet providern synkroniseras till en korrekt tidskälla. En felaktig systemtid kan orsaka federerade inloggningar misslyckas.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Installera Windows PowerShell för inloggning med SAML 2.0 identitetsleverantören.
När du har konfigurerat identitetsprovider SAML 2.0 för användning med Azure AD-inloggning, är nästa steg att ladda ned och installera Azure Active Directory-modulen för Windows PowerShell. När du har installerat ska du använda dessa cmdletar för att konfigurera Azure AD-domäner som externa domäner.

Azure Active Directory-modulen för Windows PowerShell är ett hämtningsbart program för att hantera organisationens data i Azure AD. Den här modulen installerar en uppsättning cmdlets i Windows PowerShell; du kör dessa cmdletar för att konfigurera enkel inloggning åtkomst till Azure AD och i sin tur till alla molntjänster som du prenumererar på. Instruktioner om hur du hämtar och installerar cmdletarna finns i [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Skapa ett förtroende mellan SAML-identitetsprovider och Azure AD
Innan du konfigurerar federation på en Azure AD-domän, måste den ha en anpassad domän som har konfigurerats. Du kan federera standarddomänen som tillhandahålls av Microsoft. Standarddomän från Microsoft som slutar med ”onmicrosoft.com”.
Du köra en uppsättning cmdlets i Windows PowerShell-kommandoradsgränssnittet att lägga till eller konvertera domäner för enkel inloggning.

Varje Azure Active Directory-domän som du vill federera med din identitetsleverantör SAML 2.0 måste vara antingen läggas till som en enda domän inloggning eller konverteras en inloggning domän från en standarddomän. Att lägga till eller konvertera en domän anger du ett förtroende mellan din identitetsleverantör SAML 2.0 och Azure AD.

Följande procedur vägleder dig genom att konvertera en befintlig domän som standard till en federerad domän med SAML 2.0 SP-Lite. 

>[!NOTE]
>Din domän kan det uppstå ett avbrott som påverkar användare upp till 2 timmar efter att du har utfört det här steget.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurera en domän i din Azure AD-katalog för federation


1. Ansluta till din Azure AD-katalog som en Innehavaradministratör: ansluta MsolService.
2.  Konfigurera din önskade Office 365-domän för att använda federation med SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Du kan hämta signering certifikat base64-kodad sträng från din IDP-metadatafil. Ett exempel på den här platsen har angetts men kan avvika något baserat på din implementering.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Läs mer om ”Set-MsolDomainAuthentication”: [ http://technet.microsoft.com/library/dn194112.aspx ](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Du måste köra användning ”$ecpUrl =”https://WS2012R2-0.contoso.com/PAOS”” om du ställer in ett ECP-tillägg för din identitetsleverantör. Exchange Online-klienter, exklusive Outlook Web Application (OWA) förlitar sig på en POST baserat aktiv slutpunkt. Om din SAML 2.0 STS implementerar en aktiv slutpunkt som liknar Shibboleth's ECP implementering av en aktiv slutpunkt kan det vara möjligt för att omfattande klienterna ska interagera med Exchange Online-tjänsten.

När federationstjänsten har konfigurerats du kan gå tillbaka till ”ej federerad” (eller ”hanterad”), men den här ändringen tar upp till två timmar att slutföra och det kräver att tilldela nya slumpmässigt lösenord för molnbaserade inloggning till varje användare. Växla tillbaka till ”hanterad” kan krävas i vissa scenarier för att återställa ett fel i inställningarna. Läs mer på domänen konvertering: [ http://msdn.microsoft.com/library/windowsazure/dn194122.aspx ](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Etablera användare säkerhetsobjekt till Azure AD / Office 365
Innan du kan autentisera användarna på Office 365, måste du etablera Azure AD med användare säkerhetsobjekt som motsvarar kontrollen i SAML 2.0-anspråket. Om dessa användare säkerhetsobjekt inte är känt att Azure AD i förväg, kan sedan de inte användas för federerad inloggning. Azure AD Connect eller Windows PowerShell kan användas för att etablera användare säkerhetsobjekt.

Azure AD Connect kan användas för att etablera säkerhetsobjekt till dina domäner i din Azure AD-katalog från lokala Active Directory. Mer information finns i [integrera dina lokala kataloger med Azure Active Directory](active-directory-aadconnect.md).

Windows PowerShell kan också användas för att automatiskt lägga till nya användare till Azure AD och synkronisera ändringar i den lokala katalogen. Om du vill använda Windows PowerShell-cmdlets, måste du hämta den [Azure Active Directory-moduler](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Den här proceduren visar hur du lägger till en enskild användare till Azure AD.


1. Ansluta till din Azure AD-katalog som en Innehavaradministratör: ansluta MsolService.
2.  Skapa en ny UPN: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Mer information om ”New MsolUser” utcheckning [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>”UserPrinciplName”-värdet måste matcha värdet som du skickar för ”IDPEmail” i SAML 2.0-anspråk och ”ImmutableID”-värdet måste matcha värdet som skickas i ”NameID”-kontrollen.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Kontrollera enkel inloggning med din SAML 2.0 IDP
Granska informationen och utföra stegen i följande artiklar för att konfigurera enkel inloggning med SAML 2.0 SP-Lite baserat identitetsprovider som administratör innan du verifiera och hantera enkel inloggning (kallas även identitetsfederation):


1.  Du har granskat krav för Azure AD SAML 2.0-protokollet
2.  Du har konfigurerat din SAML 2.0 identitetsleverantören.
3.  Installera Windows PowerShell för enkel inloggning med SAML 2.0 identitetsleverantören.
4.  Skapa ett förtroende mellan SAML 2.0 identitetsleverantör och Azure AD
5.  Etableras en känd test UPN i Azure Active Directory (Office 365) via Windows PowerShell eller Azure AD Connect.
6.  Konfigurera directory synkronisering med hjälp av [Azure AD Connect](active-directory-aadconnect.md).

När du ställer in enkel inloggning med din SAML 2.0 SP-Lite baserat identitet Provider, bör du kontrollera att den fungerar korrekt.

>[!NOTE]
>Om du har konverterat en domän i stället för att lägga till en kan det ta upp till 24 timmar att konfigurera enkel inloggning.
Innan du verifiera enkel inloggning ska du slutföra konfigurationen av Active Directory-synkronisering, synkronisera dina kataloger och aktivera synkroniserade användare.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verktyget för att kontrollera att enkel inloggning har ställts in korrekt
Du kan utföra följande procedur för att bekräfta att du ska kunna logga in på Molntjänsten med företagets autentiseringsuppgifter för att verifiera att enkel inloggning har ställts in korrekt.

Microsoft tillhandahåller ett verktyg som du kan använda för att testa SAML 2.0 baserat identitetsprovider. Innan du kör testverktyget, måste du ha konfigurerat en Azure AD-klient för att federera med din identitetsprovider.

>[!NOTE]
>Anslutningen Analyzer kräver Internet Explorer 10 eller senare.



1. Hämta anslutning Analyzer från [ https://testconnectivity.microsoft.com/?tabid=Client ](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Klicka på Installera nu att börja hämta och installera verktyget.
3.  Välj ”det går inte att konfigurera federation med Office 365, Azure eller andra tjänster som använder Azure Active Directory”.
4.  När verktyget har hämtats och körs, visas fönstret anslutning diagnostik. Verktyget vägleds du genom att testa federation-anslutning.
5.  Anslutningen Analyzer öppnas din SAML 2.0 IDP att logga in, ange autentiseringsuppgifterna för användarens huvudnamn du testar: ![SAML](media/active-directory-aadconnect-federation-saml-idp/saml1.png)
6.  I fönstret Federation test inloggning ska du ange ett kontonamn och lösenord för Azure AD-klient som är konfigurerad att bli federerad med SAML 2.0 identitetsprovider. Verktyget kommer att försöka logga in med autentiseringsuppgifterna och kommer att tillhandahållas på detaljerade resultat för tester som utfördes under försök logga in som utdata.
![SAML](media/active-directory-aadconnect-federation-saml-idp/saml2.png)
7. I fönstret visas en misslyckad resultatet tester. Klicka på granska detaljerade resultat att visa information om resultaten för varje test som har utförts. Du kan också spara resultaten till disk för att kunna dela dem.
 
>[!NOTE]
>Testar anslutningen analyzer också Active Federation med WS *-baserade och ECP/PAOS protokoll. Om du inte använder dessa kan du bortse från följande fel: testa Active inloggning flödet med hjälp av din identitetsleverantör Active federation slutpunkt.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verifiera manuellt att enkel inloggning har ställts in korrekt
Manuell kontroll ger ytterligare steg som du kan vidta för att säkerställa att din SAML 2.0-identitet providern fungerar i många scenarier.
Utför följande steg för att verifiera att enkel inloggning har konfigurerats på rätt sätt:


1. På en domänansluten dator, logga in på Molntjänsten med samma inloggningsnamn som du använder för företagets autentiseringsuppgifter.
2.  Klicka i lösenordsrutan. Om enkel inloggning har ställts in lösenord kommer att skuggad och följande meddelande visas ”: nu måste du logga in på &lt;företaget&gt;”.
3.  Klicka på Logga in på &lt;företaget&gt; länk. Om du ska kunna har logga in, sedan enkel inloggning ställts in.

## <a name="next-steps"></a>Nästa steg


- [Active Directory Federation Services-hantering och anpassning med Azure AD Connect](active-directory-aadconnect-federation-management.md)
- [Kompatibilitetslista för Azure AD-federation](active-directory-aadconnect-federation-compatibility.md)
- [Azure AD Connect Anpassad Installation](active-directory-aadconnect-get-started-custom.md)
