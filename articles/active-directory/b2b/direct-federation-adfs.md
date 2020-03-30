---
title: Konfigurera direkt federation med en AD FS för B2B - Azure AD
description: Lär dig hur du konfigurerar AD FS som identitetsleverantör för direktfederation så att gästerna kan logga in på dina Azure AD-appar
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
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272832"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exempel: Direkt federation med Active Directory Federation Services (AD FS) (förhandsversion)
|     |
| --- |
| Direktfederation är en offentlig förhandsversion av Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

I den här artikeln beskrivs hur du ställer in [direkt federation](direct-federation.md) med Active Directory Federation Services (AD FS) som antingen en SAML 2.0- eller WS-Fed-identitetsprovider. För att stödja direkt federation måste vissa attribut och anspråk konfigureras hos identitetsprovidern. För att illustrera hur du konfigurerar en identitetsprovider för direkt federation använder vi AD FS (Active Directory Federation Services) som exempel. Vi visar hur du konfigurerar AD FS både som EN SAML-identitetsleverantör och som en WS-Fed-identitetsleverantör.

> [!NOTE]
> I den här artikeln beskrivs hur du ställer in AD FS för både SAML och WS-Fed för illustrationsändamål. För direkta federationsintegrationer där identitetsprovidern är AD FS rekommenderar vi att du använder WS-Fed som protokoll. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Konfigurera AD FS för direktfederation för SAML 2.0
Azure AD B2B kan konfigureras för att federera med identitetsleverantörer som använder SAML-protokollet med specifika krav som anges nedan. Om du vill illustrera SAML-konfigurationsstegen visar det här avsnittet hur du konfigurerar AD FS för SAML 2.0. 

För att konfigurera direkt federation måste följande attribut tas emot i SAML 2.0-svaret från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till XML-filen för onlinesäkerhetstokentjänsten eller genom att ange dem manuellt. Steg 12 i [Skapa en test-AD FS-instans](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beskriver hur du hittar AD FS-slutpunkterna eller hur du genererar din metadata-URL, till exempel `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Attribut  |Värde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Emittenten URI för partner-IdP, till exempel`http://www.example.com/exk10l6w90DHM0yi...`         |

Följande anspråk måste konfigureras i DEN SAML 2.0-token som utfärdats av identitetsprovidern:


|Attribut  |Värde  |
|---------|---------|
|NameID-format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


I nästa avsnitt beskrivs hur du konfigurerar de attribut och anspråk som krävs med AD FS som ett exempel på en SAML 2.0-identitetsprovider.

### <a name="before-you-begin"></a>Innan du börjar

En AD FS-server måste redan vara konfigurerad och fungera innan du påbörjar den här proceduren. Mer information om hur du konfigurerar en AD FS-server finns i [Skapa en AD FS 3.0-instans på en virtuell Azure-dator](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Lägg till anspråksbeskrivningen

1. På AD FS-servern väljer du **Verktyg** > **AD FS-hantering**.
2. Välj **Beskrivningar** > av**serviceanspråk i navigeringsfönstret**.
3. Under **Åtgärder**väljer du **Lägg till anspråksbeskrivning**.
4. I fönstret **Lägg till en anspråksbeskrivning** anger du följande värden:

   - **Visningsnamn**: Beständig identifierare
   - **Skadeidentifierare:**`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Markera kryssrutan för **Publicera den här anspråksbeskrivningen i federationsmetadata som en anspråkstyp som den här federationstjänsten kan acceptera**.
   - Markera kryssrutan för **Publicera den här anspråksbeskrivningen i federationsmetadata som en anspråkstyp som den här federationstjänsten kan skicka**.

5. Klicka på **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Lägg till förtroende- och anspråksregler för den förlitande parten

1. Gå till **Verktyg** > **AD FS-hantering**på AD FS-servern .
2. Välj Förtroende för förlitande part förtroenden för **betrodda förtroenden för** > **betrodda betrodda betrodda betrodda för betrodda parter**i navigeringsfönstret.
3. Under **Åtgärder**väljer du **Lägg till förtroende för förlitande part**. 
4. I guiden Lägg till förlitande part förtroende för **Välj datakälla**använder du alternativet **Importera data om den förlitande parten som publiceras online eller i ett lokalt nätverk**. Ange url:en https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlför federationsmetadata. Lämna andra standardval. Välj **Stäng**.
5. Guiden **Redigera anspråksregler** öppnas.
6. Välj **Lägg till regel**i guiden Redigera **anspråksregler** . I **Välj regeltyp**väljer du **Skicka LDAP-attribut som anspråk**. Välj **Nästa**.
7. Ange följande värden i **Konfigurera anspråksregel:** 

   - **Anspråksregelnamn**: Regel för anspråk för e-post 
   - **Attributarkiv**: Active Directory 
   - **LDAP-attribut**: E-postadresser 
   - **Typ av utgående anspråk**: E-postadress

8. Välj **Slutför**.
9. Fönstret **Redigera anspråksregler** visar den nya regeln. Klicka på **Använd**. 
10. Klicka på **OK**.  

### <a name="create-an-email-transform-rule"></a>Skapa en regel för transformering av e-post
1. Gå till **Redigera anspråksregler** och klicka på **Lägg till regel**. I **Välj regeltyp**väljer du **Omvandla ett inkommande anspråk** och klickar på **Nästa**. 
2. Ange följande värden i **Konfigurera anspråksregel:** 

   - **Namn på anspråksregel**: Regeln för transformering av e-post 
   - **Typ av inkommande anspråk**: E-postadress 
   - **Typ av utgående anspråk**: Namn-ID 
   - **Format för utgående namn- ID:** Beständig identifierare 
   - Välj **Släpp igenom alla anspråksvärden**.

3. Klicka på **Slutför**. 
4. Fönstret **Redigera anspråksregler** visar de nya reglerna. Klicka på **Använd**. 
5. Klicka på **OK**. AD FS-servern har nu konfigurerats för direkt federation med SAML 2.0-protokollet.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Konfigurera AD FS för WS-Fed direkt federation 
Azure AD B2B kan konfigureras för att federera med identitetsleverantörer som använder WS-Fed-protokollet med de specifika kraven nedan. För närvarande har de två WS-Fed-leverantörerna testats för kompatibilitet med Azure AD inkluderar AD FS och Shibboleth. Här använder vi ACTIVE Directory Federation Services (AD FS) som ett exempel på WS-Fed-identitetsleverantören. Om du vill ha mer information om hur du upprättar ett förtroende för en förlitande part mellan en WS-Fed-kompatibel leverantör med Azure AD kan du hämta kompatibilitetsdokumenten för Azure AD Identity Provider.

För att konfigurera direkt federation måste följande attribut tas emot i WS-Fed-meddelandet från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till XML-filen för onlinesäkerhetstokentjänsten eller genom att ange dem manuellt. Steg 12 i [Skapa en test-AD FS-instans](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beskriver hur du hittar AD FS-slutpunkterna eller hur du genererar din metadata-URL, till exempel `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Attribut  |Värde  |
|---------|---------|
|PassivRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Emittenten URI för partner-IdP, till exempel`http://www.example.com/exk10l6w90DHM0yi...`         |

Obligatoriska anspråk för WS-Fed-token som utfärdats av IdP:

|Attribut  |Värde  |
|---------|---------|
|OföränderligtID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

I nästa avsnitt beskrivs hur du konfigurerar de attribut och anspråk som krävs med AD FS som ett exempel på en WS-Fed-identitetsprovider.

### <a name="before-you-begin"></a>Innan du börjar
En AD FS-server måste redan vara konfigurerad och fungera innan du påbörjar den här proceduren. Mer information om hur du konfigurerar en AD FS-server finns i [Skapa en AD FS 3.0-instans på en virtuell Azure-dator](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Lägg till förtroende- och anspråksregler för den förlitande parten 
1. Gå till **Verktyg** > **AD FS-hantering**på AD FS-servern . 
1. Välj Förtroende för förlitande part förtroenden för **betrodda förtroenden för** > **betrodda betrodda betrodda betrodda för betrodda parter**i navigeringsfönstret. 
1. Under **Åtgärder**väljer du **Lägg till förtroende för förlitande part**.  
1. I guiden Lägg till förlitande part, använd alternativet **Importera data om den förlitande parten som publiceras online eller i ett lokalt nätverk**för Select Data **Source**. Ange url:en `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`för federationsmetadata: .  Lämna andra standardval. Välj **Stäng**.
1. Guiden **Redigera anspråksregler** öppnas. 
1. Välj **Lägg till regel**i guiden Redigera **anspråksregler** . Välj Skicka anspråk **med hjälp av en anpassad regel**i Välj **regeltyp**. Välj *Nästa*. 
1. Ange följande värden i **Konfigurera anspråksregel:**

   - **Namn på anspråksregel**: Issue Immutable Id  
   - **Anpassad regel:**`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Välj **Slutför**. 
1. Fönstret **Redigera anspråksregler** visar den nya regeln. Klicka på **Använd**.  
1. I samma guiden **Redigera anspråksregler** väljer du **Lägg till regel**. I **Cohose Rule Type**väljer du Skicka **LDAP-attribut som anspråk**. Välj **Nästa**.
1. Ange följande värden i **Konfigurera anspråksregel:** 

   - **Anspråksregelnamn**: Regel för anspråk för e-post  
   - **Attributarkiv**: Active Directory  
   - **LDAP-attribut**: E-postadresser  
   - **Typ av utgående anspråk**: E-postadress 

1.  Välj **Slutför**. 
1.  Fönstret **Redigera anspråksregler** visar den nya regeln. Klicka på **Använd**.  
1.  Klicka på **OK**. AD FS-servern är nu konfigurerad för direkt federation med WS-Fed.

## <a name="next-steps"></a>Nästa steg
Därefter konfigurerar du [direktfederation i Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) antingen i Azure AD-portalen eller med PowerShell. 
