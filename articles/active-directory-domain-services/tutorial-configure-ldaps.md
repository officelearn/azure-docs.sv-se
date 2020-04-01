---
title: Självstudiekurs – Konfigurera LDAPS för Azure Active Directory Domain Services | Microsoft-dokument
description: I den här självstudien får du lära dig hur du konfigurerar LDAPS (Secure Lightweight Directory Access Protocol) för en hanterad Azure Active Directory Domain Services-domän.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 636f2e6139ad081d1e2fc67462a74cb7e18e3ff0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475834"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Självstudiekurs: Konfigurera säker LDAP för en hanterad Azure Active Directory Domain Services-domän

För att kommunicera med din Azure Active Directory Domain Services (Azure AD DS) hanterad domän används LDAP (Lightweight Directory Access Protocol). Som standard är LDAP-trafiken inte krypterad, vilket är ett säkerhetsproblem för många miljöer. Med Azure AD DS kan du konfigurera den hanterade domänen så att den använder LDAPS (Secure Lightweight Directory Access Protocol). När du använder säker LDAP krypteras trafiken. Secure LDAP är också känd som LDAP over Secure Sockets Layer (SSL) / Transport Layer Security (TLS).

Den här självstudien visar hur du konfigurerar LDAPS för en Azure AD DS-hanterad domän.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett digitalt certifikat för användning med Azure AD DS
> * Aktivera säker LDAP för Azure AD DS
> * Konfigurera säker LDAP för användning över det offentliga internet
> * Binda och testa säker LDAP för en Azure AD DS-hanterad domän

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs [kan du skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* *Verktyget LDP.exe* är installerat på datorn.
    * Installera vid behov [RSAT (Remote Server Administration Tools)][rsat] för *Active Directory Domain Services och LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien konfigurerar du säker LDAP för Azure AD DS-hanterad domän med Azure-portalen. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Skapa ett certifikat för säker LDAP

För att använda säker LDAP används ett digitalt certifikat för att kryptera kommunikationen. Det här digitala certifikatet tillämpas på din Azure AD DS-hanterade domän och låter verktyg som *LDP.exe* använda säker krypterad kommunikation när du frågar data. Det finns två sätt att skapa ett certifikat för säker LDAP-åtkomst till den hanterade domänen:

* Ett certifikat från en offentlig certifikatutfärdarmyndighet eller en företagscertifikatutfärdaren.
    * Om din organisation får certifikat från en offentlig certifikatutfärdare hämtar du det säkra LDAP-certifikatet från den offentliga certifikatutfärdaren. Om du använder en företagscertifikatutfärdaren i organisationen hämtar du det säkra LDAP-certifikatet från företagscertifikatutfärdaren.
    * En offentlig certifikatutfärdare fungerar bara när du använder ett anpassat DNS-namn med din Azure AD DS-hanterade domän. Om DNS-domännamnet för den hanterade domänen slutar på *.onmicrosoft.com*kan du inte skapa ett digitalt certifikat för att skydda anslutningen till den här standarddomänen. Microsoft äger *domänen .onmicrosoft.com,* så en offentlig certifikatutfärdaren utfärdar inget certifikat. Skapa i det här fallet ett självsignerat certifikat och använda det för att konfigurera säker LDAP.
* Ett självsignerat certifikat som du skapar själv.
    * Denna metod är bra för testning ändamål, och är vad den här guiden visar.

Certifikatet som du begär eller skapar måste uppfylla följande krav. Din hanterade domän stöter på problem om du aktiverar säker LDAP med ett ogiltigt certifikat:

* **Betrodd utfärdare** - Certifikatet måste utfärdas av en myndighet som är betrodd av datorer som ansluter till den hanterade domänen med hjälp av säker LDAP. Den här myndigheten kan vara en offentlig certifikatutfärdar eller en företags-certifikatutfärdaren som är betrodd av dessa datorer.
* **Livstid** - Intyget måste vara giltigt i minst 3-6 månader. Säker LDAP-åtkomst till din hanterade domän störs när certifikatet upphör att gälla.
* **Ämnesnamn** - Ämnesnamnet på certifikatet måste vara din hanterade domän. Om domänen till exempel heter *aaddscontoso.com*måste certifikatets ämnesnamn vara **.aaddscontoso.com*.
    * DNS-namnet eller det alternativa namnet på certifikatet måste vara ett jokerteckencertifikat för att säkerställa att den säkra LDAP fungerar korrekt med Azure AD Domain Services. Domänkontrollanter använder slumpmässiga namn och kan tas bort eller läggas till för att säkerställa att tjänsten förblir tillgänglig.
* **Nyckelanvändning** - Certifikatet måste konfigureras för *digitala signaturer* och *nyckelincisfärer*.
* **Certifikatsyfte** - Certifikatet måste vara giltigt för TLS-serverautentisering.

Det finns flera verktyg för att skapa självsignerade certifikat som OpenSSL, Keytool, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet etc. I den här självstudien ska vi skapa ett självsignerat certifikat för säker LDAP med cmdleten [New-SelfSignedCertificate.][New-SelfSignedCertificate] Öppna ett PowerShell-fönster som **administratör** och kör följande kommandon. Ersätt *$dnsName* variabeln med det DNS-namn som används av din egen hanterade domän, till exempel *aaddscontoso.com:*

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

Följande exempelutdata visar att certifikatet har genererats och lagras i det lokala certifikatarkivet *(LocalMachine\MY):*

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Förstå och exportera nödvändiga certifikat

Om du vill använda säker LDAP krypteras nätverkstrafiken med hjälp av infrastruktur för offentliga nycklar (PKI).

* En **privat** nyckel tillämpas på azure AD DS-hanterad domän.
    * Den här privata nyckeln används för att *dekryptera* den säkra LDAP-trafiken. Den privata nyckeln bör endast tillämpas på Azure AD DS-hanterad domän och inte distribueras i stor utsträckning till klientdatorer.
    * Ett certifikat som innehåller den privata nyckeln använder *. PFX-filformat.*
* En **offentlig** nyckel tillämpas på klientdatorerna.
    * Den här offentliga nyckeln används för att *kryptera* den säkra LDAP-trafiken. Den offentliga nyckeln kan distribueras till klientdatorer.
    * Certifikat utan den privata nyckeln använder *. *CER-filformat.

Dessa två nycklar, de *privata* och *offentliga* nycklarna, se till att endast rätt datorer kan kommunicera med varandra. Om du använder en offentlig certifikatutfärdare eller företagscertifikatutfärdare får du ett certifikat som innehåller den privata nyckeln och som kan tillämpas på en Azure AD DS-hanterad domän. Den offentliga nyckeln bör redan vara känd och betrodd av klientdatorer. I den här självstudien skapade du ett självsignerat certifikat med den privata nyckeln, så du måste exportera lämpliga privata och offentliga komponenter.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Exportera ett certifikat för Azure AD DS

Innan du kan använda det digitala certifikat som skapats i föregående steg med din Azure AD DS-hanterade domän exporterar du certifikatet till en *. PFX-certifikatfil* som innehåller den privata nyckeln.

1. Om du vill öppna dialogrutan *Kör* markerar du **Windows** + **R-tangenterna.**
1. Öppna Microsoft Management Console (MMC) genom att ange **mmc** i dialogrutan *Kör* och välj sedan **OK**.
1. I prompten Kontroll av **användarkonto** väljer du **Ja** för att starta MMC som administratör.
1. Välj **Lägg till/ta bort snapin-moduler på Arkiv-menyn...** **File**
1. I **snapin-guiden Certifikat** väljer du **Datorkonto**och väljer sedan **Nästa**.
1. På sidan **Välj dator** väljer du **Lokal dator: (datorn som konsolen körs på)** och välj sedan **Slutför**.
1. I dialogrutan **Lägg till eller ta bort snapin-moduler** väljer du **OK** för att lägga till snapin-modulen certifikat i MMC.
1. Expandera **Konsolrot**i MMC-fönstret . Välj **Certifikat (lokal dator)** och expandera sedan noden **Personlig,** följt av noden **Certifikat.**

    ![Öppna det personliga certifikatarkivet i Microsoft Management Console](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Det självsignerade certifikat som skapades i föregående steg visas, till exempel *aaddscontoso.com*. Högerklicka på det här certifikatet och välj sedan **Alla aktiviteter > Exportera...**

    ![Exportera certifikat i Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. Välj **Nästa**i **guiden Exportera certifikat**.
1. Den privata nyckeln för certifikatet måste exporteras. Om den privata nyckeln inte ingår i det exporterade certifikatet misslyckas åtgärden för att aktivera säker LDAP för den hanterade domänen.

    På sidan **Exportera privat nyckel** väljer du **Ja, exporterar den privata nyckeln**och väljer sedan **Nästa**.
1. Azure AD DS-hanterade domäner stöder bara *. PFX-certifikatfilformat* som innehåller den privata nyckeln. Exportera inte certifikatet som *. *CER-certifikatfilformat utan den privata nyckeln.

    På sidan **Exportera filformat** väljer du **Utbyte av personlig information - PKCS #12 (. PFX)** som filformat för det exporterade certifikatet. Markera kryssrutan för *Inkludera alla certifikat i certifieringssökvägen om möjligt:*

    ![Välj alternativet för att exportera certifikatet i PKCS 12 (. PFX) filformat](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Eftersom det här certifikatet används för att dekryptera data bör du noggrant kontrollera åtkomsten. Ett lösenord kan användas för att skydda användningen av certifikatet. Utan rätt lösenord kan certifikatet inte tillämpas på en tjänst.

    På sidan **Säkerhet** väljer du alternativet **lösenord** för att skydda *. PFX-certifikatfil.* Ange och bekräfta ett lösenord och välj sedan **Nästa**. Det här lösenordet används i nästa avsnitt för att aktivera säker LDAP för din Azure AD DS-hanterade domän.
1. På sidan **Fil att exportera** anger du filnamnet och platsen där du vill exportera certifikatet, till exempel *C:\Users\accountname\azure-ad-ds.pfx*. Föra en anteckning om lösenordet och platsen för *. PFX-filen* eftersom den här informationen skulle krävas i nästa steg.
1. På granskningssidan väljer du **Slutför** om du vill exportera certifikatet till en *. PFX-certifikatfil.* En bekräftelsedialogruta visas när certifikatet har exporterats.
1. Låt MMC:erna vara öppna för användning i följande avsnitt.

### <a name="export-a-certificate-for-client-computers"></a>Exportera ett certifikat för klientdatorer

Klientdatorer måste lita på att utfärdaren av det säkra LDAP-certifikatet kan anslutas till den hanterade domänen med LDAPS. Klientdatorerna behöver ett certifikat för att kryptera data som dekrypteras av Azure AD DS. Om du använder en offentlig certifikatutfärdare bör datorn automatiskt lita på dessa certifikatutfärdare och ha ett motsvarande certifikat. I den här självstudien använder du ett självsignerat certifikat och genererade ett certifikat som innehåller den privata nyckeln i föregående steg. Nu ska vi exportera och sedan installera det självsignerade certifikatet i det betrodda certifikatarkivet på klientdatorn:

1. Gå tillbaka till MMC för *certifikat (lokal dator) > arkivet för personliga > certifikat.* Det självsignerade certifikat som skapats i ett tidigare steg visas, till exempel *aaddscontoso.com*. Högerklicka på det här certifikatet och välj sedan **Alla aktiviteter > Exportera...**
1. Välj **Nästa**i **guiden Exportera certifikat**.
1. Eftersom du inte behöver den privata nyckeln för klienter väljer du Nej på sidan **Exportera privat nyckel,** **exportera inte den privata nyckeln**och välj sedan **Nästa**.
1. På sidan **Exportera filformat** väljer du **Base-64 kodad X.509 (. CER)** som filformat för det exporterade certifikatet:

    ![Välj alternativet för att exportera certifikatet i base-64-kodade X.509 (. CER) filformat](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. På sidan **Fil att exportera** anger du filnamnet och platsen där du vill exportera certifikatet, till exempel *C:\Users\accountname\azure-ad-ds-client.cer*.
1. På granskningssidan väljer du **Slutför** om du vill exportera certifikatet till en *. *CER-certifikatfil. En bekräftelsedialogruta visas när certifikatet har exporterats.

*Den . CER-certifikatfilen* kan nu distribueras till klientdatorer som behöver lita på den säkra LDAP-anslutningen till Azure AD DS-hanterad domän. Nu ska vi installera certifikatet på den lokala datorn.

1. Öppna Utforskaren och bläddra till den plats där du sparade *. CER-certifikatfil,* till exempel *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Höger-välj *. CER-certifikatfil* och välj sedan **Installera certifikat**.
1. I **guiden Importera certifikat**väljer du att lagra certifikatet på den *lokala datorn*och väljer sedan **Nästa:**

    ![Välj alternativet för att importera certifikatet till det lokala datorarkivet](./media/tutorial-configure-ldaps/import-cer-file.png)

1. När du uppmanas till det väljer du **Ja** om du vill att datorn ska kunna göra ändringar.
1. Välj att **automatiskt välja certifikatarkivet baserat på typen av certifikat**och välj sedan **Nästa**.
1. På granskningssidan väljer du **Slutför** för att importera *. CER-certifikat.* fil En bekräftelsedialogruta visas när certifikatet har importerats.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Aktivera säker LDAP för Azure AD DS

Med ett digitalt certifikat som skapats och exporterats och som innehåller den privata nyckeln och klientdatorn inställd på att lita på anslutningen aktiverar du nu säker LDAP på din Azure AD DS-hanterade domän. Så här aktiverar du säker LDAP på en Azure AD DS-hanterad domän:

1. Ange *domäntjänster* i rutan **Sökresurser** i [Azure-portalen.](https://portal.azure.com) Välj **Azure AD Domain Services** i sökresultatet.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **Säker LDAP**till vänster i Azure AD DS-fönstret .
1. Som standard är säker LDAP-åtkomst till din hanterade domän inaktiverad. Växla **säker LDAP** för att **aktivera**.
1. Säker LDAP-åtkomst till din hanterade domän via internet är inaktiverad som standard. När du aktiverar offentlig säker LDAP-åtkomst är domänen känslig för angrepp av brute force-lösenord över internet. I nästa steg är en nätverkssäkerhetsgrupp konfigurerad för att låsa åtkomsten till endast de nödvändiga käll-IP-adressintervallen.

    Växla **Tillåt säker LDAP-åtkomst via internet** för att **aktivera**.

1. Markera mappikonen bredvid **. PFX-fil med säkert LDAP-certifikat**. Bläddra till sökvägen till *. PFX-filen* väljer du sedan det certifikat som skapats i ett tidigare steg som innehåller den privata nyckeln.

    Som anges i föregående avsnitt om certifikatkrav kan du inte använda ett certifikat från en offentlig certifikatutfärdar med *standarddomänen .onmicrosoft.com.* Microsoft äger *domänen .onmicrosoft.com,* så en offentlig certifikatutfärdaren utfärdar inget certifikat. Kontrollera att certifikatet är i rätt format. Om det inte är det genererar Azure-plattformen certifikatverifieringsfel när du aktiverar säker LDAP.

1. Ange **lösenordet för att dekryptera . PFX-fil** som anges i ett tidigare steg när certifikatet exporterades till en *. PFX-fil.*
1. Välj **Spara** för att aktivera säker LDAP.

    ![Aktivera säker LDAP för en Azure AD DS-hanterad domän i Azure-portalen](./media/tutorial-configure-ldaps/enable-ldaps.png)

Ett meddelande visas om att säker LDAP konfigureras för den hanterade domänen. Du kan inte ändra andra inställningar för den hanterade domänen förrän åtgärden är klar.

Det tar några minuter att aktivera säker LDAP för din hanterade domän. Om det säkra LDAP-certifikat som du anger inte matchar de obligatoriska kriterierna misslyckas åtgärden för att aktivera säker LDAP för den hanterade domänen. Några vanliga orsaker till fel är om domännamnet är felaktigt eller om certifikatet upphör snart eller redan har upphört att gälla. Du kan återskapa certifikatet med giltiga parametrar och sedan aktivera säker LDAP med det här uppdaterade certifikatet.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Låsa säker LDAP-åtkomst via internet

När du aktiverar säker LDAP-åtkomst via internet till din Azure AD DS-hanterade domän skapas ett säkerhetshot. Den hanterade domänen kan nås från internet på TCP-port 636. Vi rekommenderar att du begränsar åtkomsten till den hanterade domänen till specifika kända IP-adresser för din miljö. En Azure-nätverkssäkerhetsgruppregel kan användas för att begränsa åtkomsten till säker LDAP.

Nu ska vi skapa en regel som tillåter inkommande säker LDAP-åtkomst över TCP-port 636 från en angiven uppsättning IP-adresser. En *standardregel För DenyAll* med lägre prioritet gäller för all annan inkommande trafik från internet, så endast de angivna adresserna kan nå din Azure AD DS-hanterade domän med säker LDAP.

1. I Azure-portalen väljer du *Resursgrupper* på vänster navigering.
1. Välj resursgrupp, till exempel *myResourceGroup*, och välj sedan din nätverkssäkerhetsgrupp, till exempel *aaads-nsg*.
1. Listan över befintliga regler för inkommande och utgående säkerhetsregler visas. Välj **Inställningar > Inkommande säkerhetsregler till**vänster i nätverkssäkerhetsgruppsfönstren .
1. Välj **Lägg till**och skapa sedan en regel som tillåter *TCP-port* *636*. För förbättrad säkerhet väljer du källan som *IP-adresser* och anger sedan din egen giltiga IP-adress eller ditt eget giltiga IP-adress eller intervall för din organisation.

    | Inställning                           | Värde        |
    |-----------------------------------|--------------|
    | Källa                            | IP-adresser |
    | Käll-IP-adresser / CIDR-intervall | En giltig IP-adress eller ett giltigt intervall för din miljö |
    | Källportintervall                | *            |
    | Mål                       | Alla          |
    | Målportintervall           | 636          |
    | Protokoll                          | TCP          |
    | Åtgärd                            | Tillåt        |
    | Prioritet                          | 401          |
    | Namn                              | AllowLDAPS   |

1. När du är klar väljer du **Lägg** till för att spara och tillämpa regeln.

    ![Skapa en regel för nätverkssäkerhetsgrupp för att skydda LDAPS-åtkomst via internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Konfigurera DNS-zon för extern åtkomst

Med säker LDAP-åtkomst aktiverad via internet uppdaterar du DNS-zonen så att klientdatorer kan hitta den här hanterade domänen. Den *externa IP-adressen för säker LDAP* visas på fliken **Egenskaper** för din Azure AD DS-hanterade domän:

![Visa den säkra LDAP-externa IP-adressen för din Azure AD DS-hanterade domän i Azure-portalen](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Konfigurera den externa DNS-providern för att skapa en värdpost, till exempel *ldaps,* för att lösa den här externa IP-adressen. Om du vill testa lokalt på datorn först kan du skapa en post i Windows-värdfilen. Om du vill redigera hosts-filen på den lokala datorn öppnar du *Anteckningar* som administratör och öppnar sedan filen *C:\Windows\System32\drivers\etc*

I följande exempel DNS-post, antingen med din externa DNS-leverantör eller i den lokala hosts-filen, matchas trafiken för *ldaps.aaddscontoso.com* till den externa IP-adressen *168.62.205.103:*

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testa frågor till den hanterade domänen

Om du vill ansluta och binda till din Azure AD DS-hanterade domän och söka över LDAP använder du verktyget *LDP.exe.* Det här verktyget ingår i RSAT-paketet (Remote Server Administration Tools). Mer information finns i [installera Administrationsverktyg för Fjärrserver][rsat].

1. Öppna *LDP.exe* och anslut till den hanterade domänen. Välj **Anslutning**och välj sedan **Anslut...**.
1. Ange det säkra LDAP DNS-domännamnet för den hanterade domänen som skapades i föregående steg, till exempel *ldaps.aaddscontoso.com*. Om du vill använda säker LDAP ställer du in **Port** till *636*och markerar sedan kryssrutan för **SSL**.
1. Välj **OK** om du vill ansluta till den hanterade domänen.

Bind sedan till din Azure AD DS-hanterade domän. Användare (och tjänstkonton) kan inte utföra LDAP-enkla bindningar om du har inaktiverat NTLM-lösenordsh hash-synkronisering på din Azure AD DS-instans. Mer information om hur du inaktiverar NTLM-synkronisering av lösenordshage finns i [Skydda din Azure AD DS-hanterade domän][secure-domain].

1. Välj alternativet **Anslutningsmeny** och välj sedan **Bind...**.
1. Ange autentiseringsuppgifter för ett användarkonto som tillhör gruppen *AAD DC-administratörer,* till exempel *contosoadmin*. Ange användarkontots lösenord och ange sedan domänen, till exempel *aaddscontoso.com*.
1. För **bindningstyp**väljer du alternativet för *Bindning med autentiseringsuppgifter*.
1. Välj **OK** om du vill binda till din Azure AD DS-hanterade domän.

Så här ser du vilka objekt som lagras i din Azure AD DS-hanterade domän:

1. Välj alternativet **Visa-menyn** och välj sedan **Träd**.
1. Lämna *fältet BaseDN* tomt och välj sedan **OK**.
1. Välj en behållare, till exempel *AADDC-användare,* högerklicka sedan på behållaren och välj **Sök**.
1. Lämna de förifyllda fälten inställda och välj sedan **Kör**. Resultatet av frågan visas i det högra fönstret, vilket visas i följande exempelutdata:

    ![Sök efter objekt i din Azure AD DS-hanterade domän med LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Om du vill fråga en viss behållare direkt kan du från **menyn Visa > träd** ange ett **BaseDN** som *OU=AADDC-användare,DC=AADDSCONTOSO,DC=COM* eller *OU=AADDC-datorer,DC=AADDSCONTOSO,DC=COM*. Mer information om hur du formaterar och skapar frågor finns i grunderna för [LDAP-frågor][ldap-query-basics].

## <a name="clean-up-resources"></a>Rensa resurser

Om du har lagt till en DNS-post i den lokala hosts-filen på datorn för att testa anslutningen för den här självstudien tar du bort den här posten och lägger till en formell post i DNS-zonen. Så här tar du bort posten från den lokala hosts-filen:

1. Öppna *Anteckningar* som administratör på den lokala datorn
1. Bläddra till och öppna filen *C:\Windows\System32\drivers\etc*
1. Ta bort raden för posten som du har lagt till, till exempel`168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett digitalt certifikat för användning med Azure AD DS
> * Aktivera säker LDAP för Azure AD DS
> * Konfigurera säker LDAP för användning över det offentliga internet
> * Binda och testa säker LDAP för en Azure AD DS-hanterad domän

> [!div class="nextstepaction"]
> [Konfigurera synkronisering av lösenord hash-synkronisering för en hybrid Azure AD-miljö](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
