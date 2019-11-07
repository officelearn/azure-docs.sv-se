---
title: Distribuera Azure AD Password Protection – Azure Active Directory
description: Distribuera Azure AD Password Protection för att förbjuda Felaktiga lösen ord lokalt
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695bd461ae7e979c0a803cd2d6cb450003a6bcee
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603004"
---
# <a name="deploy-azure-ad-password-protection"></a>Distribuera Azure AD-lösenordsskydd

Nu när du förstår [hur du tillämpar Azure AD Password Protection för Windows Server Active Directory](concept-password-ban-bad-on-premises.md), är nästa steg att planera och köra distributionen.

## <a name="deployment-strategy"></a>Distributions strategi

Vi rekommenderar att du startar distributioner i gransknings läge. Gransknings läget är standard inställningen för första, där lösen ord kan fortsätta att anges. Lösen ord som skulle blockeras registreras i händelse loggen. När du har distribuerat proxyservrarna och DC-agenterna i gransknings läge, bör du övervaka den effekt som lösen ords principen kommer att ha på användare och miljön när principen tillämpas.

I gransknings fasen tar många organisationer reda på följande:

* De behöver förbättra befintliga operativa processer för att använda säkrare lösen ord.
* Användarna använder ofta oskyddade lösen ord.
* De måste informera användarna om den kommande ändringen av säkerheten, eventuellt påverka dem och hur man väljer säkrare lösen ord.

Det är också möjligt att använda starkare lösen ords verifiering för att påverka din befintliga Active Directory distribution av domänkontrollantens distributions automatisering. Vi rekommenderar att minst en befordran av DOMÄNKONTROLLANTen och en degradering av en DOMÄNKONTROLLANT sker under utvärderings perioden för gransknings perioden för att hjälpa till att få ut sådana problem i förväg.  Mer information finns i:

* [Ntdsutil. exe kan inte ange ett svagt lösen ord för reparations läge för katalog tjänster](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Befordran av replik på domänkontrollant Miss lyckas på grund av ett svagt lösen ord för reparations läge för katalog tjänster](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Degradering av domänkontrollanten Miss lyckas på grund av ett svagt lokalt administratörs lösen ord](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

När funktionen har körts i gransknings läge under en rimlig period, kan du växla konfigurationen från *granskning* *till att kräva säkrare* lösen ord. Fokuserad övervakning under den här tiden är en bra idé.

## <a name="deployment-requirements"></a>Distributions krav

* Licensierings krav för Azure AD Password Protection finns i artikeln [eliminera Felaktiga lösen ord i din organisation](concept-password-ban-bad.md#license-requirements).
* Alla datorer där Azure AD Password Protection DC Agent-programvaran ska installeras måste köra Windows Server 2012 eller senare. Detta krav innebär inte att den Active Directory domänen eller skogen måste finnas på Windows Server 2012-domän eller skogens funktions nivå. Som vi nämnt i [design principer](concept-password-ban-bad-on-premises.md#design-principles)finns det ingen minsta DFL eller FFL som krävs för att antingen DC-agenten eller proxy-programvaran ska kunna köras.
* Alla datorer som hämtar DC-agenttjänsten måste ha .NET 4,5 installerat.
* Alla datorer där Azure AD Password Protection proxy service ska installeras måste köra Windows Server 2012 R2 eller senare.
   > [!NOTE]
   > Det krävs ett obligatoriskt krav för att distribuera Azure AD-lösenord även om domänkontrollanten kan ha utgående direkt Internet anslutning. 
   >
* Alla datorer där Azure AD Password Protection-proxytjänsten ska installeras måste ha .NET 4,7 installerat.
  .NET 4,7 bör redan vara installerat på en helt uppdaterad Windows Server. Vid behov kan du hämta och köra installations programmet som finns i [.NET Framework 4,7 Offline Installer för Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Alla datorer, inklusive domänkontrollanter, som har Azure AD Password Protection-komponenter installerade måste ha Universal C-körningsmiljön installerat. Du kan få körnings miljön genom att se till att du har alla uppdateringar från Windows Update. Eller så kan du hämta det i ett OS-enskilt uppdaterings paket. Mer information finns i [Uppdatera för Universal C runtime i Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Nätverks anslutningen måste finnas mellan minst en domänkontrollant i varje domän och minst en server som är värd för-proxyservern för lösen ords skydd. Den här anslutningen måste tillåta att domänkontrollanten får åtkomst till RPC Endpoint Mapper port 135 och RPC-serverport på proxyservern. Som standard är RPC-Server porten en dynamisk RPC-port, men den kan konfigureras att [använda en statisk port](#static).
* Alla datorer där Azure AD Password Protection-proxytjänsten ska installeras måste ha nätverks åtkomst till följande slut punkter:

    |**Endpoint**|**Syfte**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Autentiseringsbegäranden|
    |`https://enterpriseregistration.windows.net`|Funktioner för lösen ords skydd i Azure AD|
 
* Krav för Microsoft Azure AD Connect agent Updater

  Uppdaterings tjänsten för Microsoft Azure AD Connect Agent installeras sida vid sida med Azure AD-proxyn för lösen ords skydd. Ytterligare konfiguration krävs för att Microsoft Azure AD Connect agent Updater-tjänsten ska kunna fungera:

  Om din miljö använder en HTTP-proxyserver måste du följa rikt linjerna som anges i [arbeta med befintliga lokala proxyservrar](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).

  Tjänsten Microsoft Azure AD Connect agent uppdaterar också de TLS 1,2-steg som anges i [TLS-krav](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

  Nätverks åtkomst måste vara aktiverat för den uppsättning portar och URL: er som anges i [installations procedurerna för programproxy-miljön](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

  > [!WARNING]
  > Azure AD-proxy för lösen ords skydd och programproxy installera olika versioner av tjänsten Microsoft Azure AD Connect agent Updater, vilket är orsaken till innehållet i programproxyn. Dessa olika versioner är inkompatibla när de installeras sida vid sida, så vi rekommenderar att du inte installerar Azure AD Password Protection proxy och Application Proxy sida vid sida på samma dator.

* Alla datorer som är värdar för proxyservern för lösen ords skydd måste konfigureras för att ge domänkontrollanter möjlighet att logga in på proxy-tjänsten. Den här funktionen styrs via privilegie tilldelningen "åtkomst till den här datorn från nätverket".
* Alla datorer som är värdar för proxy-tjänsten för lösen ords skydd måste konfigureras för att tillåta utgående TLS 1,2 HTTP-trafik.
* Ett globalt administratörs konto för att registrera-proxyservern för lösen ords skydd och-skogar med Azure AD.
* Ett konto som har Active Directory domän administratörs behörighet i skogs rots domänen för att registrera Windows Server Active Directory-skogen med Azure AD.
* Alla Active Directory domäner som kör DC agent service-programvaran måste använda Distributed File System replikering (DFSR) för SYSVOL-replikering.

  Om din domän inte redan använder DFSR måste du migrera den till att använda DFSR innan du installerar lösen ords skyddet för Azure AD. Mer information finns i följande länk:

  [Migreringsguiden för SYSVOL-replikering: FRS till DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Azure AD Password Protection DC Agent-programvaran installeras för närvarande på domänkontrollanter i domäner som fortfarande använder FRS (den föregående tekniken till DFSR) för SYSVOL-replikering, men program varan fungerar inte korrekt i den här miljön. Ytterligare negativa sido effekter är enskilda filer som Miss lyckas att replikera, och SYSVOL-återställnings procedurer visas som slutförda men tyst Miss lyckas för att replikera alla filer. Du bör migrera din domän så att DFSR kan använda DFSR så snart som möjligt, både för DFSR: s inbyggda fördelar och även för att avblockera distributionen av lösen ords skydd i Azure AD. Framtida versioner av program varan kommer att inaktive ras automatiskt när de körs i en domän som fortfarande använder FRS.

* Key Distribution Service måste vara aktive rad på alla domänkontrollanter i domänen som kör Windows Server 2012. Som standard är den här tjänsten aktive rad via manuell utlösare start.

## <a name="single-forest-deployment"></a>Distribution med en skog

Följande diagram visar hur de grundläggande komponenterna i Azure AD Password Protection fungerar tillsammans i en lokal Active Directorys miljö.

![Så här fungerar Azure AD Password Protection-komponenter tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Det är en bra idé att granska hur program varan fungerar innan du distribuerar den. Se [Konceptuell översikt över lösen ords skydd i Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Ladda ned programvaran

Det finns två installations program som krävs för lösen ords skydd i Azure AD. De är tillgängliga från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Installera och konfigurera proxyservern för lösen ords skydd

1. Välj en eller flera servrar som ska vara värdar för proxyservern för lösen ords skydd.
   * Varje sådan tjänst kan bara tillhandahålla lösen ords principer för en enda skog. Värd datorn måste vara ansluten till en domän i den skogen. Både rot-och underordnade domäner stöds. Du behöver en nätverks anslutning mellan minst en DOMÄNKONTROLLANT i varje domän i skogen och på datorn med lösen ords skydd.
   * Du kan köra proxy-tjänsten på en domänkontrollant för testning. Men domänkontrollanten kräver sedan Internet anslutning, vilket kan vara en säkerhets risk. Vi rekommenderar den här konfigurationen endast för testning.
   * Vi rekommenderar minst två proxyservrar för redundans. Se [hög tillgänglighet](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Installera Proxy-tjänsten för lösen ords skydd för Azure AD med hjälp av installations programmet för `AzureADPasswordProtectionProxySetup.exe`.
   * Program varu installationen kräver ingen omstart. Program varu installationen kan automatiseras med hjälp av standard-MSI-procedurer, till exempel:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Tjänsten Windows-brandväggen måste köras innan du installerar AzureADPasswordProtectionProxySetup. MSI-paketet för att undvika ett installations fel. Om Windows-brandväggen har kon figurer ATS för att inte köras, är lösningen att tillfälligt aktivera och köra brand Väggs tjänsten under installationen. Proxy-programvaran har inget speciellt beroende av Windows-brandväggen efter installationen. Om du använder en brand vägg från en tredje part måste den fortfarande konfigureras för att uppfylla distributions kraven. Detta inkluderar att tillåta inkommande åtkomst till port 135 och proxy RPC-Server porten. Se [distributions krav](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Öppna ett PowerShell-fönster som administratör.
   * Proxy-programvaran för lösen ords skydd innehåller en ny PowerShell-modul, *AzureADPasswordProtection*. Följande steg kör olika cmdlets från den här PowerShell-modulen. Importera den nya modulen enligt följande:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Använd följande PowerShell-kommando för att kontrol lera att tjänsten körs:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Resultatet bör visa **statusen** "körs".

1. Registrera proxyservern.
   * När steg 3 har slutförts körs proxy-tjänsten på datorn. Men tjänsten har ännu inte de autentiseringsuppgifter som krävs för att kommunicera med Azure AD. Registrering med Azure AD krävs:

     `Register-AzureADPasswordProtectionProxy`

     Denna cmdlet kräver globala administratörsautentiseringsuppgifter för din Azure-klient. Du måste också ha lokala Active Directory domän administratörs behörighet i skogs rots domänen. När det här kommandot har slutförts en gång för en proxyserver kommer ytterligare anrop att lyckas, men är onödigt.

      `Register-AzureADPasswordProtectionProxy`-cmdleten stöder följande tre autentiseringsläge. De två första lägena har stöd för Azure Multi-Factor Authentication men det tredje läget fungerar inte. Mer information finns i kommentarerna nedan.

     * Interaktivt autentiseringsläge:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Det här läget fungerar inte på Server Core-operativsystem. Använd i stället något av följande autentiseringsläge. Dessutom kan det här läget Miss förväntas om förbättrad säkerhets konfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera den konfigurationen, registrera proxyservern och sedan aktivera den igen.

     * Autentiseringsläge för enhets kod:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Sedan slutför du autentiseringen genom att följa de instruktioner som visas på en annan enhet.

     * Tyst (lösenordsbaserad) autentiseringsläge:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Det här läget fungerar inte om Azure Multi-Factor Authentication krävs för ditt konto. I så fall använder du något av de föregående två autentiseringsläget eller använder i stället ett annat konto som inte kräver MFA.
        >
        > Du kan också se MFA krävs om Azure Device Registration (som används under försättsblad av Azure AD Password Protection) har kon figurer ATS för globalt Kräv MFA. För att undvika detta kan du använda ett annat konto som har stöd för MFA med något av de tidigare två autentiseringsläget, eller så kan du också tillfälligt minska kraven för MFA-kravet för Azure Device Registration. Det gör du genom att gå till hanterings portalen för Azure, gå till Azure Active Directory, sedan enheter, sedan enhets inställningar och sedan ange "Kräv Multi-factor auth för att ansluta enheter" till Nej. Se till att konfigurera om den här inställningen igen till Ja när registreringen är klar.
        >
        > Vi rekommenderar att MFA-kraven kringgås endast i test syfte.

       Du behöver för närvarande inte ange parametern *-ForestCredential* , som är reserverad för framtida funktioner.

   Registrering av proxyservern för lösen ords skydd krävs bara en gång under tjänstens livs längd. Därefter utför proxy-tjänsten automatiskt alla andra nödvändiga underhåll.

   > [!TIP]
   > Det kan finnas en märkbar fördröjning före slut för ande första gången den här cmdleten körs för en viss Azure-klient. Om inte ett haveri rapporteras behöver du inte bekymra dig om den här fördröjningen.

1. Registrera skogen.
   * Du måste initiera den lokala Active Directory skogen med de autentiseringsuppgifter som krävs för att kommunicera med Azure med hjälp av `Register-AzureADPasswordProtectionForest` PowerShell-cmdleten. Cmdleten kräver autentiseringsuppgifter för global administratör för din Azure-klient. Det kräver också lokala Active Directory företags administratörs behörighet. Det här steget körs en gång per skog.

      `Register-AzureADPasswordProtectionForest`-cmdleten stöder följande tre autentiseringsläge. De två första lägena har stöd för Azure Multi-Factor Authentication men det tredje läget fungerar inte. Mer information finns i kommentarerna nedan.

     * Interaktivt autentiseringsläge:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Det här läget fungerar inte på Server Core-operativsystem. Använd i stället något av följande två autentiseringsläge. Dessutom kan det här läget Miss förväntas om förbättrad säkerhets konfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera den konfigurationen, registrera skogen och sedan aktivera den igen.  

     * Autentiseringsläge för enhets kod:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Sedan slutför du autentiseringen genom att följa de instruktioner som visas på en annan enhet.

     * Tyst (lösenordsbaserad) autentiseringsläge:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Det här läget fungerar inte om Azure Multi-Factor Authentication krävs för ditt konto. I så fall använder du något av de föregående två autentiseringsläget eller använder i stället ett annat konto som inte kräver MFA.
        >
        > Du kan också se MFA krävs om Azure Device Registration (som används under försättsblad av Azure AD Password Protection) har kon figurer ATS för globalt Kräv MFA. För att undvika detta kan du använda ett annat konto som har stöd för MFA med något av de tidigare två autentiseringsläget, eller så kan du också tillfälligt minska kraven för MFA-kravet för Azure Device Registration. Det gör du genom att gå till hanterings portalen för Azure, gå till Azure Active Directory, sedan enheter, sedan enhets inställningar och sedan ange "Kräv Multi-factor auth för att ansluta enheter" till Nej. Se till att konfigurera om den här inställningen igen till Ja när registreringen är klar.
        >
        > Vi rekommenderar att MFA-kraven kringgås endast i test syfte.

       De här exemplen fungerar bara om den för tillfället inloggade användaren också är en Active Directory domän administratör för rot domänen. Om detta inte är fallet kan du ange alternativa domänautentiseringsuppgifter via parametern *-ForestCredential* .

   > [!NOTE]
   > Om flera proxyservrar är installerade i din miljö spelar det ingen roll vilken proxyserver du använder för att registrera skogen.
   >
   > [!TIP]
   > Det kan finnas en märkbar fördröjning före slut för ande första gången den här cmdleten körs för en viss Azure-klient. Om inte ett haveri rapporteras behöver du inte bekymra dig om den här fördröjningen.

   Registrering av Active Directorys skogen krävs bara en gång i skogens livs längd. Därefter utför domänkontrollantens agenter i skogen automatiskt alla andra nödvändiga underhåll. När `Register-AzureADPasswordProtectionForest` har körts för en skog, lyckas ytterligare anrop av cmdleten men är onödigt.

   För att `Register-AzureADPasswordProtectionForest` ska lyckas måste minst en domänkontrollant som kör Windows Server 2012 eller senare vara tillgänglig i proxy serverns domän. Program varan för DC-agenten behöver inte installeras på några domänkontrollanter innan det här steget.

1. Konfigurera proxy-tjänsten för lösen ords skydd för att kommunicera via en HTTP-proxy.

   Om din miljö kräver att en särskild HTTP-proxy används för att kommunicera med Azure använder du den här metoden: skapa en *AzureADPasswordProtectionProxy. exe. config* -fil i mappen%ProgramFiles%\Azure AD Password Protection Proxy\Service. Ta med följande innehåll:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Om HTTP-proxyn kräver autentisering lägger du till *useDefaultCredentials* -taggen:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   I båda fallen ersätter du `http://yourhttpproxy.com:8080` med adressen och porten för din aktuella HTTP-proxyserver.

   Om din HTTP-proxy har kon figurer ATS för att använda en auktoriseringsprincip måste du bevilja åtkomst till Active Directory dator kontot för den dator som är värd för proxyservern för lösen ords skydd.

   Vi rekommenderar att du stoppar och startar om proxy-tjänsten när du har skapat eller uppdaterat filen *AzureADPasswordProtectionProxy. exe. config* .

   Proxy-tjänsten stöder inte användning av autentiseringsuppgifter för att ansluta till en HTTP-proxy.

1. Valfritt: Konfigurera proxy-tjänsten för lösen ords skydd för att lyssna på en angiven port.
   * DC-agentens program vara för lösen ords skydd på domän kontrol Lanterna använder RPC över TCP för att kommunicera med proxy-tjänsten. Som standard lyssnar proxy tjänsten på en tillgänglig dynamisk RPC-slutpunkt. Men du kan konfigurera tjänsten så att den lyssnar på en angiven TCP-port, om detta är nödvändigt på grund av nätverkets topologi eller brand Väggs krav i din miljö.
      * <a id="static" /></a>för att konfigurera tjänsten så att den körs under en statisk port använder du `Set-AzureADPasswordProtectionProxyConfiguration`-cmdleten.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

      * Om du vill konfigurera tjänsten så att den körs under en dynamisk port använder du samma procedur men anger *StaticPort* tillbaka till noll:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

   > [!NOTE]
   > Proxyservern för lösen ords skydd kräver en manuell omstart efter en ändring i Port konfigurationen. Men du behöver inte starta om program varan för DC-agenten på domän kontrol Lanterna när du har gjort dessa konfigurations ändringar.

   * Om du vill fråga efter den aktuella konfigurationen av tjänsten använder du `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Installera DC-agenttjänsten

   Installera DC-agenttjänsten för lösen ords skydd med hjälp av `AzureADPasswordProtectionDCAgentSetup.msi`-paketet.

   Program varu installationen eller avinstallationen kräver en omstart. Detta krav beror på att lösen ords filter-DLL: er bara läses in eller tas bort av en omstart.

   Du kan installera DC-agenttjänsten på en dator som ännu inte är en domänkontrollant. I det här fallet startar och körs tjänsten men förblir inaktiv tills datorn uppgraderas till en domänkontrollant.

   Du kan automatisera program varu installationen med hjälp av standard-MSI-procedurer. Till exempel:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Du kan utelämna `/norestart`-flaggan om du vill att installations programmet ska starta om datorn automatiskt.

Installationen slutförs när DC-agentens program vara har installerats på en domänkontrollant och datorn startas om. Ingen annan konfiguration krävs eller är möjlig.

## <a name="upgrading-the-proxy-agent"></a>Uppgradera proxyagenten

Om det finns en nyare version av proxy-programvaran för lösen ords skydd i Azure AD utförs uppgraderingen genom att den senaste versionen av installations programmet för `AzureADPasswordProtectionProxySetup.exe` körs. Den senaste versionen av program varan finns på [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Du behöver inte avinstallera den aktuella versionen av proxy-programvaran. installations programmet kommer att utföra en uppgradering på plats. Ingen omstart krävs vid uppgradering av proxy-programvaran. Program uppgraderingen kan automatiseras med hjälp av standard-MSI-procedurer, till exempel: `AzureADPasswordProtectionProxySetup.exe /quiet`.

Proxyagenten stöder automatisk uppgradering. Vid automatisk uppgradering används tjänsten Microsoft Azure AD Connect agent Updateer, som installeras sida vid sida med proxy-tjänsten. Automatisk uppgradering är aktiverat som standard och kan aktive ras eller inaktive ras med hjälp av `Set-AzureADPasswordProtectionProxyConfiguration`-cmdleten. Den aktuella inställningen kan frågas med hjälp av `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet. Microsoft rekommenderar att inställningen för automatisk uppgradering alltid är aktive rad.

`Get-AzureADPasswordProtectionProxy` cmdlet kan användas för att fråga program varu versionen för alla installerade proxy agenter i en skog.

## <a name="upgrading-the-dc-agent"></a>Uppgradera DC-agenten

När en nyare version av Azure AD Password Protection DC Agent-programvaran är tillgänglig utförs uppgraderingen genom att den senaste versionen av `AzureADPasswordProtectionDCAgentSetup.msi`-programpaketet körs. Den senaste versionen av program varan finns på [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Du behöver inte avinstallera den aktuella versionen av program varan för DC-agenten. installations programmet kommer att utföra en uppgradering på plats. En omstart krävs alltid vid uppgradering av DC Agent-programvaran – detta krav orsakas av Windows-funktioner. 

Program uppgraderingen kan automatiseras med hjälp av standard-MSI-procedurer, till exempel: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Du kan utelämna `/norestart`-flaggan om du vill att installations programmet ska starta om datorn automatiskt.

`Get-AzureADPasswordProtectionDCAgent` cmdlet kan användas för att fråga program varu versionen av alla installerade DC-agenter i en skog.

## <a name="multiple-forest-deployments"></a>Distributioner av flera skogar

Det finns inga ytterligare krav för att distribuera lösen ords skydd i Azure AD över flera skogar. Varje skog konfigureras oberoende av varandra enligt beskrivningen i avsnittet "distribution med en skog". Varje lösen ords skydds proxy kan bara stödja domänkontrollanter från den skog som den är ansluten till. Lösen ords skydds program varan i alla skogar är inte medvetna om lösen ords skydds program som distribueras i andra skogar, oavsett Active Directory betrodda konfigurationer.

## <a name="read-only-domain-controllers"></a>Skrivskyddade domänkontrollanter

Lösen ords ändringar/uppsättningar bearbetas inte och sparas inte på skrivskyddade domänkontrollanter (RODC). De vidarebefordras till skrivbara domänkontrollanter. Så du behöver inte installera program varan för DC-agenten på RODC.

## <a name="high-availability"></a>Hög tillgänglighet

Det huvudsakliga tillgänglighets skyddet för lösen ords skydd är tillgängligheten till proxyservrar när domän kontrol Lanterna i en skog försöker ladda ned nya principer eller andra data från Azure. Varje DC-Agent använder en enkel algoritm för resursallokering när du bestämmer vilken proxyserver som ska anropas. Agenten hoppar över proxyservrar som inte svarar. För de flesta fullständigt anslutna Active Directory-distributioner som har felfri replikering av både katalog-och SYSVOL-mappar räcker det med två proxyservrar för att säkerställa tillgängligheten. Detta resulterar i en tids nedladdning av nya principer och andra data. Men du kan distribuera ytterligare proxyservrar.

Utformningen av program varan för DC-agenten minskar de vanliga problem som är associerade med hög tillgänglighet. DC-agenten upprätthåller en lokal cache med den senast hämtade lösen ords principen. Även om alla registrerade proxyservrar blir otillgängliga fortsätter DC-agenterna att tillämpa sin cachelagrade lösen ords princip. En rimlig uppdaterings frekvens för lösen ords principer i en stor distribution är vanligt vis dagar, inte timmar eller mindre. Det innebär att proxyservrarna inte kraftigt påverkar lösen ords skyddet i Azure AD.

## <a name="next-steps"></a>Nästa steg

Nu när du har installerat de tjänster som du behöver för Azure AD-lösenordet på dina lokala servrar kan du [utföra konfigurationen för installation och insamling av rapporterings information](howto-password-ban-bad-on-premises-operations.md) för att slutföra distributionen.

[Konceptuell översikt över lösen ords skydd i Azure AD](concept-password-ban-bad-on-premises.md)
