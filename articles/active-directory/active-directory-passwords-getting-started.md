---
title: "Kom igång: Azure AD-lösenordshantering | Microsoft Docs"
description: "Låt användare återställa sina egna lösenord, identifiera kraven för lösenordsåterställning och aktivera tillbakaskrivning av lösenord i Active Directory."
services: active-directory
keywords: "Active Directory-lösenordshantering, lösenordshantering, återställa Azure AD-lösenord"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: c40fca54b02f2673194ab16c41314f1e50be12be
ms.lasthandoff: 03/06/2017


---
# <a name="getting-started-with-password-management"></a>Komma igång med lösenordshantering
> [!IMPORTANT]
> **Är du här eftersom du har problem med att logga in?** I så fall är det [här som du ser hur du kan ändra och återställa ditt eget lösenord](active-directory-passwords-update-your-own-password.md).
>
>

Konfigurera systemet i några få enkla steg så att användarna kan hantera sina Azure Active Directory-lösenord i molnet eller sina lokala Active Directory-lösenord. När du har kontrollerat att du uppfyller några enkla krav kan du snabbt implementera funktioner för lösenordsändring och lösenordsåterställning för hela organisationen. I den här artikeln går vi igenom följande:

* [**Hur du konfigurerar systemet så att användarna kan återställa sina Azure Active Directory-lösenord**](#enable-users-to-reset-their-azure-ad-passwords)
  * [Krav för lösenordsåterställning via självbetjäning](#prerequisites)
  * [Steg 1: Konfigurera en princip för återställning av lösenord](#step-1-configure-password-reset-policy)
  * [Steg 2: Lägga till kontaktdata för testanvändaren](#step-2-add-contact-data-for-your-test-user)
  * [Steg 3: Återställa ditt lösenord som en användare](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Konfigurera systemet så att användarna kan återställa eller ändra sina lokala Active Directory-lösenord**](#enable-users-to-reset-or-change-their-ad-passwords)
  * [Krav för tillbakaskrivning av lösenord](#writeback-prerequisites)
  * [Steg 1: Ladda ned den senaste versionen av Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
  * [Steg 2: Aktivera tillbakaskrivning av lösenord i Azure AD Connect via användargränssnittet eller PowerShell och verifiera](#step-2-enable-password-writeback-in-azure-ad-connect)
  * [Steg 3: Konfigurera brandväggen](#step-3-configure-your-firewall)
  * [Steg 4: Konfigurera lämpliga behörigheter](#step-4-set-up-the-appropriate-active-directory-permissions)
  * [Steg 5: Återställa ditt AD-lösenord som en användare och verifiera](#step-5-reset-your-ad-password-as-a-user)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>Konfigurera systemet så att användarna kan återställa sina Azure AD-lösenord
Det här avsnittet beskriver hur du aktiverar lösenordsåterställning via självbetjäning för din AAD-molnkatalog, hur du registrerar användare för lösenordsåterställning via självbetjäning och hur du testar en lösenordsåterställning via självbetjäning som en användare.

* [Krav för lösenordsåterställning via självbetjäning](#prerequisites)
* [Steg 1: Konfigurera en princip för återställning av lösenord](#step-1-configure-password-reset-policy)
* [Steg 2: Lägga till kontaktdata för testanvändaren](#step-2-add-contact-data-for-your-test-user)
* [Steg 3: Återställa ditt lösenord som en användare](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>Krav
Innan du kan aktivera och använda lösenordsåterställning via självbetjäning måste du uppfylla följande krav:

* Skapa en AAD-klient. Mer information finns i [Komma igång med Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
* Skaffa en Azure-prenumeration. Mer information finns i [Vad är en Azure AD-klient?](active-directory-administer.md#what-is-an-azure-ad-tenant).
* Koppla din AAD-klient till din Azure-prenumeration. Mer information finns i [Hur Azure-prenumerationer är associerade med Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
* Uppgradera till Azure AD Premium eller Basic eller använd en O365-betallicens. Mer information finns i [Azure Active Directory-versioner](https://azure.microsoft.com/pricing/details/active-directory/).

  > [!NOTE]
  > Om du vill aktivera återställning av lösenord via självbetjäning för dina molnanvändare måste du uppgradera till Azure AD Premium, Azure AD Basic eller en betald O365-licens.  Om du vill aktivera återställning av lösenord via självbetjäning för dina lokala användare måste du uppgradera till Azure AD Premium. Mer information finns i [Azure Active Directory-versioner](https://azure.microsoft.com/pricing/details/active-directory/). Den här informationen innehåller detaljerade anvisningar för hur du registrerar dig för Azure AD Premium eller Basic, hur du aktiverar din licensplan och din åtkomst till Azure AD samt hur du tilldelar åtkomst till administratörs- och användarkonton.
  >
  >
* Skapa minst ett administratörskonto och ett användarkonto i AAD-katalogen.
* Tilldela en licens för AAD Premium eller Basic eller en O365-betallicens till administratörs- och användarkontot som du skapat.

### <a name="step-1-configure-password-reset-policy"></a>Steg 1: Konfigurera en princip för återställning av lösenord
Konfigurera en princip för lösenordsåterställning för användare genom att följa dessa steg:

1. Öppna valfri webbläsare och gå till den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. I den [klassiska Azure-portalen](https://manage.windowsazure.com), letar du upp **Active Directory-tillägget** i navigeringsfältet till vänster.

   ![Lösenordshantering i Azure AD][001]
3. Under fliken **Katalog** klickar du på den katalog där du vill konfigurera principen för lösenordsåterställning för användare, till exempel Wingtip Toys.

    ![][002]
4. Klicka på fliken **Konfigurera**.

   ![][003]

5. Under fliken **Konfigurera** bläddrar du ned till avsnittet **Princip för lösenordsåterställning för användare**.  Här konfigurerar du alla aspekter av principen för lösenordsåterställning för användare för en viss katalog. *Om du inte ser fliken Konfigurera kontrollerar du att du har registrerat dig för Azure Active Directory Premium eller Basic och har __tilldelat en licens__ till administratörskontot som konfigurerar den här funktionen.*  

   > [!NOTE]
   > **Principen du konfigurerar gäller bara för slutanvändare i din organisation, inte administratörer**. Av säkerhetsskäl kontrollerar Microsoft principen för lösenordsåterställning för administratörer. Den aktuella principen för administratörer kräver två utmaningar – mobiltelefon och e-postadress.

   ![][004]
6. Du konfigurerar principen för lösenordsåterställning för användare genom att dra växlingsknappen **Användare som har aktiverats för lösenordsåterställning** till inställningen **Ja**.  Nu visas fler kontroller som du kan använda för att konfigurera hur den här funktionen fungerar i din katalog.  Anpassa lösenordsåterställningen som det passar dig.  Om du vill ha mer information om vad de olika kontrollerna för principer för lösenordsåterställning gör läser du [Anpassa: Lösenordshantering i Azure AD](active-directory-passwords-customize.md).

   ![][005]
7. När du har konfigurerat principen för lösenordsåterställning för användare för din klient klickar du på **Spara** längst ned på skärmen.

   > [!NOTE]
   > En princip för lösenordsåterställning med dubbel kontroll rekommenderas så att du ser hur funktionen fungerar i det mest komplexa fallet.
   >
   >

   ![][006]

   > [!NOTE]
   > **Principen du konfigurerar gäller bara för slutanvändare i din organisation, inte administratörer**. Av säkerhetsskäl kontrollerar Microsoft principen för lösenordsåterställning för administratörer. Den aktuella principen för administratörer kräver två utmaningar – mobiltelefon och e-postadress.
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>Steg 2: Lägga till kontaktdata för testanvändaren
Du kan välja mellan flera alternativ när du ska lägga till data för användare i organisationen som ska användas för lösenordsåterställning.

* Redigera användare i den [klassiska Azure-portalen](https://manage.windowsazure.com) eller [Office 365-administrationsportalen](https://portal.microsoftonline.com)
* Använd AAD Connect för att synkronisera användaregenskaper till Azure AD från en lokal Active Directory-domän.
* Redigera användaregenskaper med hjälp av Windows PowerShell.
* Tillåt att användare registrerar sina egna data genom att uppmana dem att gå till registreringsportalen på [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).
* Kräv att användarna registrerar sig för lösenordsåterställning när de loggar in på åtkomstpanelen på [http://myapps.microsoft.com](http://myapps.microsoft.com) genom att ändra SSPR-konfigurationsalternativet **Kräv att användare registrerar sig när de loggar in på åtkomstpanelen** till **Ja**.

Om du vill lära dig mer om vilka data som används för lösenordsåterställning, och eventuella formateringskrav för dessa data, läser du [Vilka data används av lösenordsåterställning?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>Så här lägger du till kontaktdata för användare via användarregistreringsportalen
1. Om användarna i din organisation ska kunna använda registreringsportalen för lösenordsåterställning måste du ge dem en länk till den här sidan ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) eller aktivera alternativet som kräver att användarna registrerar sig.  När användarna klickar på den här länken uppmanas de att logga in med organisationskontot.  När de har gjort det visas följande sida:

   ![][007]
2. Här kan användarna uppge och verifiera sina mobiltelefoner, alternativa e-postadresser eller säkerhetsfrågor.  Så här ser det ut när en användare verifierar sin mobiltelefon.

   ![][008]
3. När en användare har uppgett den här informationen uppdateras sidan för att ange att informationen är giltig (den har dolts nedan).  När användaren klickar på **Slutför** eller **Avbryt** öppnas åtkomstpanelen.

   ![][009]
4. När en användare har verifierat båda dessa uppgifter uppdateras användarens profil med de data som han eller hon har angett.  Eftersom **Arbetstelefon** har angetts manuellt i det här exemplet så kan även detta användas för att återställa användarens lösenord.

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>Steg 3: Återställa ditt Azure AD-lösenord som en användare
Nu när du har konfigurerat en återställningsprincip för användare och angett kontaktuppgifterna för användaren kan användaren utföra en lösenordsåterställning via självbetjäning.

#### <a name="to-perform-a-self-service-password-reset"></a>Så här utför du en lösenordsåterställning via självbetjäning
1. Om du går till en webbplats som [**portal.microsoftonline.com**](http://portal.microsoftonline.com) visas en inloggningsskärm som den nedan.  Klicka på länken **Kan du inte komma åt ditt konto?** för att testa användargränssnittet för lösenordsåterställning.

   ![][011]
2. När du klickar på **Kan du inte komma åt ditt konto?** kommer du till en ny sida där du uppmanas att ange ett **användar-ID** som du vill återställa ett lösenord för.  Ange ditt **testanvändar-ID** här, utför captcha-verifieringen och klicka på **Nästa**.

   ![][012]
3. Eftersom användaren har angett en **arbetstelefon**, en **mobiltelefon** och en **alternativ e-postadress** i vårt exempel så visas alla dessa uppgifter som alternativ för den första kontrollen.

   ![][013]
4. I vårt exempel väljer du att **ringa upp** **arbetstelefonen** först.  När användare väljer en telefonbaserad metod uppmanas de att **verifiera sina telefonnummer** innan de kan återställa sina lösenord.  Avsikten med detta är att förhindra att obehöriga användare spammar telefonnumren till användare i din organisation.

   ![][014]
5. När användaren bekräftar sitt telefonnummer och klickar på Ring upp visas en rotationsruta och hans eller hennes telefon ringer.  Ett meddelande spelas upp när användaren tar upp luren och uppmanar **användaren att trycka på ”#”** för att verifiera sitt konto.  När användaren trycker på den här knappen verifieras användaren i den första kontrollen och tas automatiskt till nästa verifieringssteg.

   ![][015]
6. När användaren har klarat den första kontrollen uppdateras användargränssnittet automatiskt och kontrollen tas bort från listan med tillgängliga alternativ.  I vårt exempel, eftersom du valde **Arbetstelefon** först, finns bara **Mobiltelefon** och **Alternativ e-postadress** kvar som giltiga alternativ för verifiering i det andra verifieringssteget.  Klicka på e-postalternativet **Skicka e-post till min alternativa e-postadress **.  När du har gjort det och trycker på Skicka-knappen skickas ett e-postmeddelande till den registrerade alternativa e-postadressen.

   ![][016]
7. Här är ett exempel på ett e-postmeddelande som skickas till användaren – observera företagsanpassningen:

   ![][017]
8. När e-postmeddelandet tas emot uppdateras sidan och du kan ange verifieringskoden från e-postmeddelandet i inmatningsrutan som visas nedan.  När rätt kod har angetts börjar nästa knapp lysa och du kan slutföra det andra verifieringssteg.

   ![][018]
9. När du har uppfyllt kraven för organisationens lösenordsprincip kan du välja ett nytt lösenord.  Lösenordet godkänns beroende på om det uppfyller kraven för ”starka” AAD-lösenord (se [Lösenordsprincip i Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)). En kontroll för lösenordsstyrka visas och anger om det angivna lösenordet uppfyller kraven i principen eller inte.

   ![][019]
10. När du har angett matchande lösenord som uppfyller organisationens princip återställs ditt lösenord. Du kan logga in med det nya lösenordet direkt.

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>Konfigurera systemet så att användarna kan återställa eller ändra sina AD-lösenord
Det här avsnittet beskriver hur du konfigurerar lösenordsåterställning för tillbakaskrivning av lösenord till en lokal Active Directory.

* [Krav för tillbakaskrivning av lösenord](#writeback-prerequisites)
* [Steg 1: Ladda ned den senaste versionen av Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
* [Steg 2: Aktivera tillbakaskrivning av lösenord i Azure AD Connect via användargränssnittet eller PowerShell och verifiera](#step-2-enable-password-writeback-in-azure-ad-connect)
* [Steg 3: Konfigurera brandväggen](#step-3-configure-your-firewall)
* [Steg 4: Konfigurera lämpliga behörigheter](#step-4-set-up-the-appropriate-active-directory-permissions)
* [Steg 5: Återställa ditt AD-lösenord som en användare och verifiera](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>Krav för tillbakaskrivning
Innan du kan aktivera och använda tillbakaskrivning av lösenord måste du kontrollera att du uppfyller följande krav:

* Du har en Azure AD-klient med Azure AD Premium aktiverat.  Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).
* Återställning av lösenord har konfigurerats och aktiverats i din klient.  Mer information finns i [Konfigurera systemet så att användarna kan återställa sina Azure AD-lösenord](#enable-users-to-reset-their-azure-ad-passwords)
* Du har minst ett administratörskonto och ett testanvändarkonto med en Azure AD Premium-licens som du kan använda för att testa den här funktionen.  Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).

  > [!NOTE]
  > Se till att det administratörskonto som du använder för att aktivera tillbakaskrivning av lösenord är ett molnadministratörskonto (skapat i Azure AD), inte ett federerat konto (skapat i lokala AD och synkroniserat till Azure AD).
  >
  >
* Du har en lokal AD-distribution med en enda eller flera skogar som kör Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 eller Windows Server 2012 R2 med de senaste service pack-versionerna installerade.

  > [!NOTE]
  > Om du kör en äldre version av Windows Server 2008 och 2008 R2 kan du fortfarande använda den här funktionen, men du måste [ladda ned och installera KB 2386717](https://support.microsoft.com/kb/2386717) innan du kan genomdriva din lokala AD-lösenordsprincip i molnet.
  >
  >
* Azure AD Connect-verktyget är installerat och du har förberett AD-miljön för synkronisering till molnet.  Mer information finns i [Använda din lokala identitetsinfrastruktur i molnet](connect/active-directory-aadconnect.md).

  > [!NOTE]
  > Innan du testar tillbakaskrivning av lösenord bör du slutföra en fullständig import och en fullständig synkronisering från både AD och Azure AD i Azure AD Connect.
  >
  >
* Om du använder Azure AD Sync eller Azure AD Connect måste **TCP 443** för utgående trafik (och i vissa fall **TCP 9350 9354**) vara öppen.  Mer information finns i [Steg 3: Konfigurera brandväggen](#step-3-configure-your-firewall). Du kan inte längre använda DirSync för det här scenariot.  Om du fortfarande använder DirSync uppgraderar du till den senaste versionen av Azure AD Connect innan du distribuerar tillbakaskrivning av lösenord.

  > [!NOTE]
  > Vi rekommenderar starkt att alla som använder Azure AD Sync eller DirSync uppgraderar till den senaste versionen av Azure AD Connect för att få bästa möjliga upplevelse och tillgång till nya funktioner efter hand som de ges ut.
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>Steg 1: Ladda ned den senaste versionen av Azure AD Connect
Tillbakaskrivning av lösenord är tillgängligt i versioner av Azure AD Connect eller i Azure AD Sync med versionsnummer **1.0.0419.0911** eller högre.  Tillbakaskrivning av lösenord med automatisk kontoupplåsning är tillgängligt i versioner av Azure AD Connect eller i Azure AD Sync med versionsnummer **1.0.0485.0222** eller högre. Om du kör en äldre version uppgraderar du till minst den här versionen innan du fortsätter. [Klicka här om du vill ladda ned den senaste versionen av Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Så här kontrollerar du versionen av Azure AD Sync
1. Gå till **%ProgramFiles%\Azure Active Directory Sync\**.
2. Leta upp den körbara filen **ConfigWizard.exe**.
3. Högerklicka på den körbara filen och välj alternativet **Egenskaper** på snabbmenyn.
4. Klicka på fliken **Information**.
5. Leta upp fältet **Filversion**.

   ![][021]

Om det här numret är större än eller lika med **1.0.0419.0911**, eller om du installerar Azure AD Connect så kan du gå vidare till [Steg 2: Aktivera tillbakaskrivning av lösenord i Azure AD Connect via användargränssnittet eller PowerShell och verifiera](#step-2-enable-password-writeback-in-azure-ad-connect).

> [!NOTE]
> Om det här är första gången som du installerar verktyget Azure AD Connect rekommenderar vi att du följer några metodtips för att förbereda din miljö för katalogsynkronisering.  Innan du installerar Azure AD Connect-verktyget så måste du aktivera katalogsynkronisering antingen i [Office 365-administrationsportalen](https://portal.microsoftonline.com) eller den [klassiska Azure-portalen](https://manage.windowsazure.com).  Mer information finns i [Hantera Azure AD Connect](active-directory-aadconnect-whats-next.md).
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>Steg 2: Aktivera tillbakaskrivning av lösenord i Azure AD Connect
Nu när du har laddat ned verktyget Azure AD Connect kan du aktivera tillbakaskrivning av lösenord.  Du kan göra det på två sätt.  Du kan antingen aktivera tillbakaskrivning av lösenord på skärmen för valfria funktioner i Azure AD Connect-konfigurationsguiden eller via Windows PowerShell.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>Så här aktivera du tillbakaskrivning av lösenord i konfigurationsguiden
1. Öppna konfigurationsguiden för **Azure AD Connect** på **datorn för katalogsynkronisering**.
2. Klicka dig igenom stegen tills du kommer till konfigurationsskärmen för **valfria funktioner**.
3. Markera alternativet **Tillbakaskrivning av lösenord**.

   ![][022]
4. Slutför guiden. På den sista sidan sammanfattas ändringarna, inklusive konfigurationsändringen för tillbakaskrivning av lösenord.

> [!NOTE]
> Du kan inaktivera tillbakaskrivning av lösenord när som helst genom att antingen köra guiden igen och avmarkera funktionen eller genom att ändra inställningen **Skriv tillbaka lösenord till lokal katalog** till **Nej** i avsnittet **Princip för lösenordsåterställning för användare** på fliken **Konfigurera** för din katalog på den [klassiska Azure-portalen](https://manage.windowsazure.com).  Mer information om hur du anpassar lösenordsåterställningsmiljön finns i [Anpassa: Lösenordshantering i Azure AD](active-directory-passwords-customize.md).
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Så här aktiverar du tillbakaskrivning av lösenord med Windows PowerShell
1. Öppna ett nytt **upphöjt Windows PowerShell-fönster** på **datorn för katalogsynkronisering**.
2. Om modulen inte redan har lästs in skriver du `import-module ADSync`-kommandot för att läsa in modulen med Azure AD Connect-cmdlets i den aktuella sessionen.
3. Hämta listan med Azure AD-anslutningsappar i systemet genom att köra `Get-ADSyncConnector`-cmdleten och spara resultaten i `$aadConnectorName`, som `$connectors = Get-ADSyncConnector|where-object {$\_.name -like "\*AAD"}`
4. Hämta tillbakaskrivningsstatusen för den aktuella anslutningen genom att köra följande cmdlet:`Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. Aktivera tillbakaskrivning av lösenord genom att köra cmdleten:`Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> Om du uppmanas att ange autentiseringsuppgifter ser du till att administratörskontot som du anger för AzureADCredential är ett **molnadministratörskonto (som skapats i Azure AD)**, inte ett federerat konto (som skapats i lokala AD och som synkroniseras med Azure AD).
>
> [!NOTE]
> Du kan inaktivera tillbakaskrivning av lösenord via PowerShell genom att upprepa samma instruktioner som ovan men i stället skicka `$false` i steget eller genom att ändra inställningen **Skriv tillbaka lösenord till lokal katalog** till **Nej** i avsnittet **Princip för lösenordsåterställning för användare** på fliken **Konfigurera** för din katalog på den [klassiska Azure-portalen](https://manage.windowsazure.com).
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>Kontrollera att konfigurationen lyckades
Om konfigurationen lyckades visas ett meddelande i Windows PowerShell-fönstret eller i konfigurationsgränssnittet som anger att tillbakaskrivning av lösenord har aktiverats eller att åtgärden lyckades.

Du kan också kontrollera att tjänsten har installerats korrekt genom att öppna Loggboken, navigera till programmets händelselogg och leta upp händelsen **31005 – OnboardingEventSuccess** från källan **PasswordResetService**.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>Steg 3: Konfigurera brandväggen
När du har aktiverat tillbakaskrivning av lösenord måste du kontrollera att den dator som kör Azure AD Connect kan nå Microsofts molntjänster och ta emot förfrågningar för tillbakaskrivning av lösenord. Det här steget inbegriper uppgradering av anslutningsreglerna för dina nätverksinstallationer (proxyservrar, brandväggar osv.), vilka tillåter utgående anslutningar till vissa [webbadresser och IP-adresser som ägs av Microsoft](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US) via vissa specifika nätverksportar. Dessa ändringar kan variera beroende på vilken version av Azure AD Connect-verktyget som du använder. Om du vill ha mer sammanhang kan du läsa [Hur tillbakaskrivning av lösenord fungerar](active-directory-passwords-learn-more.md#how-password-writeback-works) och [Säkerhetsmodell för tillbakaskrivning av lösenord](active-directory-passwords-learn-more.md#password-writeback-security-model).

#### <a name="why-do-i-need-to-do-this"></a>Varför måste jag göra detta?

För att tillbakaskrivning av lösenord ska fungera korrekt måste datorn som kör Azure AD Connect kunna upprätta utgående HTTPS-anslutningar till **. servicebus.windows.net* och en specifik IP-adress som används av Azure, enligt definitionen i [IP-intervallslistan för Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

För version **1.1.439.0** (senaste) och senare verktyget Azure AD Connect:

- Den senaste versionen av verktyget Azure AD Connect behöver åtkomst till **utgående HTTPS** till:
    - *passwordreset.microsoftonline.com*
    - *servicbus.Windows.NET*

För versionerna **1.0.8667.0** till **1.1.380.0** av verktyget Azure AD Connect:

- **Alternativ 1:** Tillåt utgående HTTPS-anslutningar över port 443 (med URL-adress eller IP-adress).
    - När du ska använda detta:
        - Använd det här alternativet om du vill ha den enklaste konfiguration som inte behöver uppdateras i takt med att Azure Datacenter IP-intervallen ändras över tid.
    - Nödvändiga steg:
        - Tillåt utgående HTTPS-anslutningar över port 443 (med URL-adress eller IP-adress).
<br><br>
- **Alternativ 2:** Tillåt utgående HTTPS-anslutningar till specifika IP-intervall och URL: er
    - När du ska använda detta:
        - Använd det här alternativet om du befinner dig i en begränsad nätverksmiljö, eller av någon annan anledning inte känner dig bekväm med att tillåta utgående anslutningar.
        - Om du vill att tillbakaskrivning av lösenord ska fortsätta att fungera i den här konfigurationen, måste du se till att dina nätverksresurser hålls uppdaterade varje vecka med de senaste IP-adresserna från listan över IP-intervall i Microsoft Azure Datacenter. Dessa IP-intervall som är tillgängliga som en XML-fil som uppdateras varje onsdag (Stillahavstid) och träder i kraft följande måndag (Stillahavstid).
    - Nödvändiga steg:
        - Tillåt alla utgående HTTPS-anslutningar till *. servicebus.windows.net
        - Tillåt alla utgående HTTPS-anslutningar till alla IP-adresser i listan över IP-intervall i Microsoft Azure Datacenter och håll den här konfigurationen uppdaterad varje vecka.

> [!NOTE]
> Om du har konfigurerat tillbakaskrivning av lösenord genom att följa anvisningarna ovan och inte ser några fel i händelseloggen för Azure AD Connect, men får anslutningsfel när du testar, så kan det t.ex. bero på att någon nätverksutrustning i din miljö blockerar HTTPS-anslutningar till IP-adresser. Samtidigt som t.ex. en anslutning till *https://*. servicebus.windows.net* tillåts, så kan en anslutning till en specifik IP-adress inom intervallet blockeras. Om du ska kunna lösa det här problemet måste du antingen konfigurera din nätverksmiljö så att den tillåter alla utgående HTTPS-anslutningar via port 443 till alla URL-eller IP-adresser (alternativ 1 ovan) eller så måste du arbeta med ditt nätverksteam och explicit tillåta HTTPS-anslutningar till vissa specifika IP-adresser (alternativ 2 ovan).

**För äldre versioner:**

- Tillåt utgående TCP-anslutningar via port 443, 9350-9354 och 5671
- Tillåt utgående anslutningar till *https://ssprsbprodncu-sb.accesscontrol.windows.net/*

> [!NOTE]
> Om du har en version av Azure AD Connect som är äldre än 1.0.8667.0 så rekommenderar Microsoft starkt att du uppgraderar till den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). Den innehåller ett antal förbättringar vad gäller nätverkstillbakaskrivning, vilket underlättar konfigurationen.

När nätverksutrustningen har konfigurerats kan du starta om den dator där Azure AD Connect-verktyget körs.

#### <a name="idle-connections-on-azure-ad-connect-114390-and-up"></a>Inaktiva anslutningar i Azure AD Connect (1.1.439.0 och senare)
Verktyget Azure AD Connect skickar regelbundet pingar/keepalive-överföringar till ServiceBus-slutpunkterna så att anslutningarna förblir aktiva. Om verktyget identifierar att för många anslutningar avslutas så ökas automatiskt pingfrekvensen till slutpunkten. Det kortaste pingintervallet är 1 ping var 60:e sekund, men **vi rekommenderar att proxyservrar/brandväggar tillåter inaktiva anslutningar i minst 2–3 minuter.** \*För äldre versioner rekommenderar vi 4 minuter eller mer.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>Steg 4: Konfigurera Active Directory-behörigheter
För varje skog som innehåller användare vars lösenord ska återställas, om X är det konto som angavs för skogen i konfigurationsguiden (vid den ursprungliga konfigurationen), så måste X tilldelas följande utökade behörigheter för rotobjektet i varje domän i skogen: **Återställ lösenord**, **Ändra lösenord**, **Skrivbehörighet** för `lockoutTime` och **Skrivbehörighet** för `pwdLastSet`. Rättigheten måste konfigureras att ärvas av alla användarobjekt.  

Om du inte är säker på vilket konto som avses ovan öppnar du konfigurationsgränssnittet för Azure Active Directory Connect och klickar på alternativet **Granska din lösning**.  Det konto som du ska lägga till behörigheter till är understruket i rött i skärmbilden nedan.

**<font color="red">Se till att du anger den här behörigheten för varje domän i varje skog i systemet, annars kommer tillbakaskrivning av lösenord inte att fungera korrekt.</font>**

  ![][032]

  När du anger dessa behörigheter kan MA-tjänstkontot för varje skog hantera lösenord för användarkonton i skogen. Om du inte tilldelar dessa behörigheter kanske det ser ut som tillbakaskrivning av lösenord är korrekt konfigurerat, men användarna kommer att få problem när de försöker hantera sina lokala lösenord från molnet. Här följer detaljerade anvisningar för hur du kan göra detta med hjälp av snapin-modulen **Active Directory -- användare och datorer**:

> [!NOTE]
> Det kan ta upp till en timme för behörigheterna att replikeras till alla objekt i katalogen.
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>Så här konfigurerar du rätt behörigheter för att tillbakaskrivningen ska fungera korrekt
1. Öppna **Active Directory -- användare och datorer** med ett konto som har nödvändig behörighet för domänadministration.
2. Kontrollera att **Avancerade funktioner** är aktiverat i alternativet på **Visa-menyn**.
3. Högerklicka på objektet som representerar roten för domänen i den vänstra rutan.
4. Klicka på fliken **Säkerhet**.
5. Klicka på **Avancerat**.

   ![][024]
6. Klicka på **Lägg till** på fliken **Behörighet**.

   ![][025]
7. Välj det konto som du vill lägga till behörigheter för (det är samma konto som angavs när synkroniseringen konfigurerades för skogen).
8. Välj **Underordnade objekt** i listrutan överst.
9. I dialogrutan **Behörighetspost** som visas markerar du kryssrutan för **Återställ lösenord**, **Ändra lösenord**, **Skrivbehörighet** för `lockoutTime` och **Skrivbehörighet** för `pwdLastSet`.

   ![][026]
   ![][027]
   ![][028]
10. Klicka på **Använd/OK** i alla öppna dialogrutor.

### <a name="step-5-reset-your-ad-password-as-a-user"></a>Steg 5: Återställa ditt AD-lösenord som en användare
Nu när tillbakaskrivning av lösenord har aktiverats kan du testa att det fungerar genom att återställa lösenordet för en användare vars konto har synkroniserats med din molnklient.

#### <a name="to-verify-password-writeback-is-working-properly"></a>Så här kontrollerar du att tillbakaskrivning av lösenord fungerar korrekt
1. Gå till [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) eller gå till valfri inloggningssida som kräver ditt organisations-ID och klicka på länken **Kan du inte komma åt ditt konto?**.

   ![][029]
2. Nu öppnas en ny sida som uppmanar dig att ange ett användar-ID vars lösenord du vill återställa. Ange ditt testanvändar-ID och gå igenom stegen för lösenordsåterställning.
3. När du har återställt lösenordet visas en skärm som den nedan. Det betyder att ditt lösenord har återställts i dina lokala kataloger och/eller molnkataloger.

   ![][030]
4. Kontrollera att åtgärden lyckades eller diagnostisera eventuella fel genom att gå till **katalogsynkroniseringsdatorn**, öppna **Loggboken**, navigera till **programhändelseloggen** och leta upp händelsen **31002 – PasswordResetSuccess** från källan **PasswordResetService** för din testanvändare.

   ![][031]



## <a name="next-steps"></a>Nästa steg
Nedan finns länkar till alla sidor med dokumentation om lösenordsåterställning i Azure AD:

* **Är du här eftersom du har problem med att logga in?** I så fall är det [här som du ser hur du kan ändra och återställa ditt eget lösenord](active-directory-passwords-update-your-own-password.md).
* [**Så här fungerar det**](active-directory-passwords-how-it-works.md) – lär dig om de sex olika komponenterna i tjänsten och vad de gör
* [**Anpassa**](active-directory-passwords-customize.md) – lär dig hur du anpassar tjänstens utseende, känsla och beteende efter din organisations behov
* [**Metodtips**](active-directory-passwords-best-practices.md) – lär dig hur du snabbt distribuerar och effektivt hanterar lösenord i din organisation
* [**Få insikter**](active-directory-passwords-get-insights.md) – lär dig mer om våra integrerade rapporteringsfunktioner
* [**Vanliga frågor och svar**](active-directory-passwords-faq.md) – få svar på vanliga frågor
* [**Felsökning**](active-directory-passwords-troubleshoot.md) – lär dig hur du snabbt felsöker problem med tjänsten
* [**Lär dig mer**](active-directory-passwords-learn-more.md) – gräv djupare i de tekniska detaljerna för tjänsten

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

