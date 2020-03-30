---
title: Anpassade domäner i Azure AD Application Proxy | Microsoft-dokument
description: Konfigurera och hantera anpassade domäner i Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481389"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Konfigurera anpassade domäner med Azure AD Application Proxy

När du publicerar ett program via Azure Active Directory Application Proxy skapar du en extern URL för användarna. Den här URL:en hämtar standarddomänen *yourtenant.msappproxy.net*. Om du till exempel publicerar en app med namnet *Utgifter* i din klient med namnet *Contoso*är den externa webbadressen *\/https: /expenses-contoso.msappproxy.net*. Om du vill använda ditt eget domännamn i stället för *msappproxy.net*kan du konfigurera en anpassad domän för ditt program. 

## <a name="benefits-of-custom-domains"></a>Fördelar med anpassade domäner

Det är en bra idé att konfigurera anpassade domäner för dina appar när det är möjligt. Några orsaker till att använda anpassade domäner är:

- Länkar mellan appar fungerar även utanför företagsnätverket. Om appen har hårdkodade interna länkar till mål utanför programproxyn och länkarna inte kan lösas externt bryts den utan en anpassad domän. När dina interna och externa webbadresser är desamma undviker du det här problemet. Om du inte kan använda anpassade domäner läser du [Omdirigera hårdkodade länkar för appar som publicerats med Azure AD Application Proxy](../application-proxy-link-translation.md) för andra sätt att lösa problemet. 
  
- Användarna får en enklare upplevelse eftersom de kan komma åt appen med samma webbadress inifrån eller utanför nätverket. De behöver inte lära sig olika interna och externa webbadresser eller spåra sin aktuella plats. 

- Du kan styra ditt varumärke och skapa de webbadresser du vill ha. En anpassad domän kan hjälpa till att bygga upp användarnas förtroende, eftersom användarna ser och använder ett välbekant namn i stället för *msappproxy.net*.

- Vissa konfigurationer fungerar bara med anpassade domäner. Du behöver till exempel anpassade domäner för appar som använder SAML (Security Assertion Markup Language), till exempel när du använder Ad FS (Active Directory Federation Services), men som inte kan använda WS-Federation. Mer information finns i [Arbeta med anspråksmedvetna appar i Programproxy](application-proxy-configure-for-claims-aware-applications.md). 

Om du inte kan matcha de interna och externa webbadresserna är det inte lika viktigt att använda anpassade domäner, men du kan ändå dra nytta av de andra fördelarna. 

## <a name="dns-configuration-options"></a>DNS-konfigurationsalternativ

Det finns flera alternativ för att konfigurera DNS-konfigurationen, beroende på dina krav:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Samma interna och externa URL, olika interna och externa beteenden 

Om du inte vill att dina interna användare ska dirigeras via programproxyn kan du konfigurera en DNS med *delad hjärna*. En delad DNS-infrastruktur dirigerar interna värdar till en intern domännamnsserver och externa värdar till en extern domännamnsserver för namnmatchning. 

![DNS med delad hjärna](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Olika interna och externa webbadresser 

Om de interna och externa webbadresserna är olika behöver du inte konfigurera beteende med delad hjärna, eftersom användarroutning bestäms av WEBBADRESSEN. I det här fallet ändrar du bara den externa DNS:en och dirigerar den externa URL:en till slutpunkten Programproxy. 

När du väljer en anpassad domän för en extern URL visar ett informationsfält den CNAME-post som du behöver lägga till i den externa DNS-providern. Du kan alltid se den här informationen genom att gå till appens **programproxysida.**

## <a name="set-up-and-use-custom-domains"></a>Konfigurera och använda anpassade domäner

Om du vill konfigurera en lokal app för att använda en anpassad domän behöver du en verifierad azure Active Directory-anpassad domän, ett PFX-certifikat för den anpassade domänen och en lokal app för att konfigurera. 

### <a name="create-and-verify-a-custom-domain"></a>Skapa och verifiera en anpassad domän

Så här skapar och verifierar du en anpassad domän:

1. I Azure Active Directory väljer du **Anpassade domännamn** i den vänstra navigeringen och väljer sedan Lägg till **anpassad domän**. 
1. Ange ditt eget domännamn och välj **Lägg till domän**. 
1. Kopiera TXT-postinformationen för domänen på domänsidan. 
1. Gå till domänregistraren och skapa en ny TXT-post för din domän, baserat på din kopierade DNS-information.
1. När du har registrerat domänen väljer du **Verifiera**på domänens sida i Azure Active Directory . När domänstatusen har **verifierats**kan du använda domänen i alla dina Azure AD-konfigurationer, inklusive Programproxy. 

Mer detaljerade instruktioner finns i [Lägga till ditt eget domännamn med Azure Active Directory-portalen](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Konfigurera en app för att använda en anpassad domän

Så här publicerar du appen via Programproxy med en anpassad domän:

1. För en ny app i Azure Active Directory väljer du **Enterprise-program** i den vänstra navigeringen. Välj **Nytt program**. I avsnittet **Lokala program** väljer du Lägg till ett **lokalt program**. 
   
   För en app som redan finns i **Enterprise-program**väljer du den i listan och väljer sedan **Programproxy** i den vänstra navigeringen. 

2. På sidan Inställningar för programproxy anger du ett **namn** om du lägger till ett eget lokalt program.

3.  Ange den interna webbadressen för appen i fältet **Intern webbadress.**
   
4. I fältet **Extern url** kan du lista listan och välja den anpassade domän som du vill använda.
   
5. Välj **Lägg till**.
   
   ![Välj anpassad domän](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Om domänen redan har ett certifikat visas certifikatinformationen i fältet **Certifikat.** Annars väljer du fältet **Certifikat.** 
   
   ![Klicka här om du vill ladda upp ett certifikat](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. På **SSL-certifikatsidan** bläddrar du till och väljer din PFX-certifikatfil. Ange lösenordet för certifikatet och välj **Ladda upp certifikat .** Mer information om certifikat finns i avsnittet [Certifikat för anpassade domäner.](#certificates-for-custom-domains)
   
   ![Ladda upp certifikat](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > En anpassad domän behöver bara sitt certifikat laddas upp en gång. Därefter används det uppladdade certifikatet automatiskt när du använder den anpassade domänen för andra appar.
   
8. Om du har lagt till ett certifikat väljer du **Spara**på sidan **Programproxy** . 
   
9. I informationsfältet på **sidan Programproxy** bör du notera den CNAME-post som du behöver lägga till i DNS-zonen. 
   
   ![Lägg till CNAME DNS-post](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Följ instruktionerna på [Hantera DNS-poster och postuppsättningar med hjälp av Azure-portalen](../../dns/dns-operations-recordsets-portal.md) för att lägga till en DNS-post som omdirigerar den nya externa URL:en till *msappproxy.net* domänen.
   
11. Om du vill kontrollera att DNS-posten är korrekt konfigurerad använder du kommandot [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) för att bekräfta att den externa URL:en kan nås och *att msapproxy.net* domänen visas som ett alias.

Programmet har nu konfigurerats för att använda den anpassade domänen. Var noga med att tilldela användare till ditt program innan du testar eller släpper det. 

Om du vill ändra domänen för en app väljer du en annan domän i listrutan i **Extern URL** på appens **programproxysida.** Ladda upp ett certifikat för den uppdaterade domänen om det behövs och uppdatera DNS-posten. Om du inte ser den anpassade domän som du vill använda i listrutan i **Extern URL**kanske den inte verifieras.

Mer detaljerade instruktioner för programproxy finns i [Självstudiekurs: Lägg till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certifikat för anpassade domäner

Ett certifikat skapar den säkra TLS-anslutningen för din anpassade domän. 

### <a name="certificate-formats"></a>Certifikatformat

Du måste använda ett PFX-certifikat för att säkerställa att alla obligatoriska mellanliggande certifikat inkluderas. Certifikatet måste innehålla den privata nyckeln.

Det finns ingen begränsning för metoderna för certifikatsignatur. Elliptic Curve Cryptography (ECC), Subject Alternative Name (SAN) och andra vanliga certifikattyper stöds. 

Du kan använda jokerteckencertifikat så länge jokertecknet matchar den externa URL:en. Du måste använda jokerteckencertifikat för [jokerteckenprogram](application-proxy-wildcard.md). Om du vill använda certifikatet för att även komma åt underdomäner måste du lägga till underdomänens jokertecken som ämnesalternativnamn i samma certifikat. Ett certifikat för * \*.adventure-works.com* fungerar till exempel inte för * \*.apps.adventure-works.com* om du inte lägger till * \*.apps.adventure-works.com* som ett alternativt ämnesnamn. 

Du kan använda certifikat som utfärdats av din egen infrastruktur för offentliga nycklar (PKI) om certifikatkedjan är installerad på dina klientenheter. Intune kan distribuera dessa certifikat till hanterade enheter. För icke-hanterade enheter måste du installera dessa certifikat manuellt. 

Vi rekommenderar inte att du använder en privat rotcertifikatutfärdare eftersom den privata rotcertifikatutfärdaren också skulle behöva skickas till klientdatorer, vilket kan innebära många utmaningar.

### <a name="certificate-management"></a>Certifikathantering

All certifikathantering sker via de enskilda programsidorna. Gå till programmets **programproxysida** för att komma åt **fältet Certifikat.**

Du kan använda samma certifikat för flera program. Om ett uppladdat certifikat fungerar med ett annat program tillämpas det automatiskt. Du uppmanas inte att ladda upp den igen när du lägger till eller konfigurerar appen. 

När ett certifikat går ut får du en varning om att du ska ladda upp ett annat certifikat. Om certifikatet återkallas kan användarna se en säkerhetsvarning när de öppnar appen. Om du vill uppdatera certifikatet för en app navigerar du till **programproxysidan** för appen, väljer **Certifikat**och överför ett nytt certifikat. Om det gamla certifikatet inte används av andra appar tas det bort automatiskt. 

## <a name="next-steps"></a>Nästa steg
* [Aktivera enkel inloggning till](application-proxy-configure-single-sign-on-with-kcd.md) dina publicerade appar med Azure AD-autentisering.
* [Aktivera villkorlig åtkomst](../conditional-access/overview.md) till dina publicerade appar.

