---
title: Ställ in direkt federation med en AD FS för B2B - Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar AD FS som en identitetsleverantör för direkta federation så att gäster kan logga in på din Azure AD-appar
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544327"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exempel: Direct federation med Active Directory Federation Services (AD FS) (förhandsversion)
|     |
| --- |
| Direct federation är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Den här artikeln beskriver hur du ställer in [dirigera federation](direct-federation.md) med hjälp av Active Directory Federation Services (AD FS) som en SAML 2.0 eller WS-Fed identitetsprovider. För att stödja direkt federation, måste vissa attribut och anspråk konfigureras hos identitetsprovidern. För att visa hur du konfigurerar en identitetsprovider för direkta federation kan använder vi Active Directory Federation Services (AD FS) som ett exempel. Vi visar hur du konfigurerar AD FS både som en SAML-identitetsprovider och som en identitetsprovider för WS-Fed.

> [!NOTE]
> Den här artikeln beskriver hur du konfigurerar AD FS för både SAML och WS-Fed för tydlighetens skull. För direkt federation integrationer där identitetsprovidern är AD FS, bör du använda WS-Fed som protokoll. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurera AD FS för SAML 2.0 direkt federation
Azure AD B2B kan konfigureras för att federera med identitetsleverantörer som använder SAML-protokoll med specifika krav som anges nedan. Det här avsnittet visas hur du konfigurerar AD FS för SAML 2.0 för att illustrera SAML-konfigurationssteg. 

Om du vill konfigurera direkt federation tas följande attribut emot i SAML 2.0-svar från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till online säkerhetstokentjänsten XML-fil eller genom att ange dem manuellt. Steg 12 i [skapa en test AD FS-instans](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beskriver hur du hittar AD FS-slutpunkter eller hur du skapar dina Metadata_url, till exempel `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Attribut  |Värde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner IDP: N, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |

Följande anspråk måste konfigureras i SAML 2.0-token som utfärdas av identitetsleverantören.:


|Attribut  |Värde  |
|---------|---------|
|NameID-Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


Nästa avsnitt visar hur du konfigurerar obligatoriska attribut och anspråk med hjälp av AD FS som ett exempel på en SAML 2.0-identitetsprovider.

### <a name="before-you-begin"></a>Innan du börjar

AD FS-servern måste redan vara inställt upp och fungerar innan du påbörjar den här proceduren. Om du vill ha hjälp med att konfigurera AD FS-servern finns i [skapar en test AD FS 3.0-instans på en Azure virtuell dator](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Lägg till kravet beskrivning

1. AD FS-servern, Välj **verktyg** > **AD FS-hantering**.
2. I navigeringsfönstret väljer **Service** > **Anspråksbeskrivningar**.
3. Under **åtgärder**väljer **Lägg till kravet beskrivning**.
4. I den **lägga till en beskrivning av anspråk** fönstret, ange följande värden:

   - **Visningsnamn**: Beständiga identifierare
   - **Anspråk identifierare**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Markera kryssrutan för **publicera beskrivningen anspråket i federationsmetadata som en Anspråkstyp som federationstjänsten kan acceptera**.
   - Markera kryssrutan för **publicera beskrivningen anspråket i federationsmetadata som en Anspråkstyp som federationstjänsten kan skicka**.

5. Klicka på **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Lägg till förtroende för förlitande part och anspråksregler

1. På AD FS-servern går du till **verktyg** > **AD FS-hantering**.
2. I navigeringsfönstret väljer **förtroenden** > **förlitande Partsförtroenden**.
3. Under **åtgärder**väljer **Lägg till förlitande part**. 
4. I guiden Lägg till förlitande part förtroende för **Välj datakälla**, använder du alternativet **importera data om förlitande part publicerats online eller i ett lokalt nätverk**. Ange den här federationsmetadata URL - https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml. Lämna de andra standardinställningarna. Välj **Stäng**.
5. Den **redigera Anspråksregler** guiden öppnas.
6. I den **redigera Anspråksregler** väljer **Lägg till regel**. I **Välj regeltyp**väljer **skicka LDAP-attribut som anspråk**. Välj **Nästa**.
7. I **konfigurera Anspråksregel**, ange följande värden: 

   - **Namn på anspråksregel**: E-postkravregel 
   - **Attributarkiv**: Active Directory 
   - **LDAP-attribut**: E-Mail-Addresses 
   - **Utgående Anspråkstyp**: E-postadress

8. Välj **Slutför**.
9. Den **redigera Anspråksregler** den nya regeln visas i fönstret. Klicka på **Verkställ**. 
10. Klicka på **OK**.  

### <a name="create-an-email-transform-rule"></a>Skapa en regel för e-transformering
1. Gå till **redigera Anspråksregler** och klicka på **Lägg till regel**. I **Välj regeltyp**väljer **transformera ett inkommande anspråk** och klicka på **nästa**. 
2. I **konfigurera Anspråksregel**, ange följande värden: 

   - **Namn på anspråksregel**: E-transformeringsregel 
   - **Typ av inkommande anspråk**: E-postadress 
   - **Utgående Anspråkstyp**: Namn-ID 
   - **Format för utgående namn-ID**: Beständiga identifierare 
   - Välj **Släpp igenom alla anspråksvärden**.

3. Klicka på **Slutför**. 
4. Den **redigera Anspråksregler** fönstret visar nya regler. Klicka på **Verkställ**. 
5. Klicka på **OK**. AD FS-servern har konfigurerats för direkt federation med hjälp av SAML 2.0-protokollet.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurera AD FS för WS-Fed direkt federation 
Azure AD B2B kan konfigureras för att federera med identitetsleverantörer som använder protokollet WS-Fed de särskilda krav som anges nedan. För närvarande har två WS-Fed providrar testats för kompatibilitet med Azure AD inkluderar AD FS och Shibboleth. Här kan använder vi Active Directory Federation Services (AD FS) som ett exempel på WS-Fed identitetsprovider. Ladda ned Azure AD Identity providern kompatibilitet dokumentationen för mer information om hur du upprättar ett förlitande partsförtroende mellan en kompatibel WS-Fed-provider med Azure AD.

Om du vill konfigurera direkt federation tas följande attribut emot i WS-Fed meddelandet från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till online säkerhetstokentjänsten XML-fil eller genom att ange dem manuellt. Steg 12 i [skapa en test AD FS-instans](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beskriver hur du hittar AD FS-slutpunkter eller hur du skapar dina Metadata_url, till exempel `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Attribut  |Värde  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner IDP: N, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |

Begärda anspråk för WS-Fed token som utfärdats av IDP: N:

|Attribut  |Värde  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

Nästa avsnitt visas hur du konfigurerar attributen som krävs och anspråk med hjälp av AD FS som ett exempel på en identitetsprovider för WS-Fed.

### <a name="before-you-begin"></a>Innan du börjar
AD FS-servern måste redan vara inställt upp och fungerar innan du påbörjar den här proceduren. Om du vill ha hjälp med att konfigurera AD FS-servern finns i [skapar en test AD FS 3.0-instans på en Azure virtuell dator](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Lägg till förtroende för förlitande part och anspråksregler 
1. På AD FS-servern går du till **verktyg** > **AD FS-hantering**. 
1. I navigeringsfönstret väljer **förtroenden** > **förlitande Partsförtroenden**. 
1. Under **åtgärder**väljer **Lägg till förlitande part**.  
1. I Lägg till förlitande part förtroende guiden för **Välj datakälla**, använder du alternativet **importera data om förlitande part publicerats online eller i ett lokalt nätverk**. Ange den här URL för federation metadata: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`.  Lämna de andra standardinställningarna. Välj **Stäng**.
1. Den **redigera Anspråksregler** guiden öppnas. 
1. I den **redigera Anspråksregler** väljer **Lägg till regel**. I **Välj regeltyp**väljer **skicka anspråk med en anpassad regel**. Välj *Nästa*. 
1. I **konfigurera Anspråksregel**, ange följande värden:

   - **Namn på anspråksregel**: Problemet oföränderligt Id  
   - **Anpassad regel**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Välj **Slutför**. 
1. Den **redigera Anspråksregler** den nya regeln visas i fönstret. Klicka på **Verkställ**.  
1. I samma **redigera Anspråksregler** väljer **Lägg till regel**. I **Cohose regeltyp**väljer **skicka LDAP-attribut som anspråk**. Välj **Nästa**.
1. I **konfigurera Anspråksregel**, ange följande värden: 

   - **Namn på anspråksregel**: E-postkravregel  
   - **Attributarkiv**: Active Directory  
   - **LDAP-attribut**: E-Mail-Addresses  
   - **Utgående Anspråkstyp**: E-postadress 

1.  Välj **Slutför**. 
1.  Den **redigera Anspråksregler** den nya regeln visas i fönstret. Klicka på **Verkställ**.  
1.  Klicka på **OK**. AD FS-servern har konfigurerats för direkt federation med hjälp av WS-Fed.

## <a name="next-steps"></a>Nästa steg
Nu ska du ska [konfigurera direkt federation i Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) antingen i Azure AD-portalen eller med hjälp av PowerShell. 
