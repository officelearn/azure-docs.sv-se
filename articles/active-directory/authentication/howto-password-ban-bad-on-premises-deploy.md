---
title: Distribuera Azure AD-lösenordsskydd – Azure Active Directory
description: Distribuera Azure AD-lösenordsskydd om du vill förbjuda felaktiga lösenord på plats
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
ms.openlocfilehash: 3be702d1f75b0a96e22ea03602c924be580b0968
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499258"
---
# <a name="deploy-azure-ad-password-protection"></a>Distribuera Azure AD-lösenordsskydd

Nu när du förstår [för tvingande Azure AD-lösenordsskydd för Windows Server Active Directory](concept-password-ban-bad-on-premises.md), nästa steg är att planera och köra din distribution.

## <a name="deployment-strategy"></a>Distributionsstrategi

Vi rekommenderar att du börjar distributioner i granskningsläge. Granskningsläget är inledande standardinställningen, där lösenord kan fortsätta att ställas in. Lösenord som blockeras registreras i händelseloggen. När du har distribuerat proxyservrar och DC-agenter i granskningsläge bör du övervaka effekten som lösenordsprincipen har på användare och miljön när principen tillämpas.

Under fasen audit ta många organisationer reda på som:

* De behöver för att förbättra befintliga operativa processer för att använda fler säkra lösenord.
* Användare använder ofta inte säkert lösenord.
* De behöver för att informera användarna om de kommande ändringarna security tvingande, eventuell påverkan på dem och hur du väljer säkrare lösenord.

När funktionen har körts i granskningsläge i rimlig tid, kan du byta konfigurationen från *granska* till *tvinga* att kräva mer säkra lösenord. Fokuserade övervakning under denna tid är en bra idé.

## <a name="deployment-requirements"></a>Krav för distribution

* Alla domänkontrollanter som ta DC tjänst för Azure AD-lösenordsskydd installerad måste köra Windows Server 2012 eller senare. Det här kravet innebär inte att Active Directory-domän eller skog måste också vara på Windows Server 2012 domänens eller skogens funktionsnivå. Som vi nämnde i [designprinciper](concept-password-ban-bad-on-premises.md#design-principles), det finns ingen minsta DFL eller FFL som krävs för antingen DC-agenten eller proxy programvaran ska köras.
* Alla datorer som får proxyn tjänst för Azure AD-lösenordsskydd installerad måste köra Windows Server 2012 R2 eller senare.
* Alla datorer där Azure AD-lösenord Protection proxytjänsten installeras måste ha .NET 4.7 installerad.
  .NET 4.7 bör vara installerad på en helt uppdaterade Windows-Server. Om detta inte är fallet, hämta och kör installationsprogrammet som finns på [The .NET Framework 4.7 offline installationsprogrammet för Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Alla datorer, inklusive domänkontrollanter, som får Azure AD lösenord protection komponenter som är installerade måste ha den universella C som är installerad. Du kan hämta körningen genom att kontrollera att du har alla uppdateringar från Windows Update. Eller du kan hämta den i ett uppdateringspaket för OS-specifika. Mer information finns i [uppdatering för Universal C-körning i Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Nätverksanslutning måste det finnas mellan minst en domänkontrollant i varje domän och minst en server som är värd för proxytjänsten för lösenordsskydd. Den här anslutningen måste tillåta domänkontrollanten för att få åtkomst till RPC-slutpunkt mapper port 135 och RPC-server-porten i proxy-tjänsten. RPC-serverport är som standard en dynamisk RPC-port, men den kan konfigureras att [använda en statisk port](#static).
* Alla datorer som är värdar för proxy-tjänsten måste ha nätverksåtkomst till följande slutpunkter:

    |**Slutpunkt**|**Syfte**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Begäranden om autentisering|
    |`https://enterpriseregistration.windows.net`|Skyddsfunktioner för Azure AD-lösenord|

* Alla datorer som är värdar för proxytjänst lösenordsskydd måste konfigureras för att tillåta utgående TLS 1.2 HTTP-trafik.
* Ett globalt administratörskonto för att registrera proxytjänsten för lösenordsskydd och skog med Azure AD.
* Ett konto som har administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen för att registrera Windows Server Active Directory-skog med Azure AD.
* Alla Active Directory-domän som kör tjänsten DC Agentprogrammet måste använda Distributed File System (DFSR Replication) för sysvol-replikering.
* Key Distribution Service måste aktiveras på alla domänkontrollanter i domänen som kör Windows Server 2012. Som standard aktiveras den här tjänsten via manuella utlösaren start.

## <a name="single-forest-deployment"></a>Sammanhållna distribution

Följande diagram visar hur grundkomponenterna i Azure AD-lösenordsskydd tillsammans i en lokal Active Directory-miljö.

![Hur Azure AD-lösenord protection komponenter fungerar tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Det är en bra idé att granska hur programvaran fungerar innan du distribuerar den. Se [konceptuell översikt över Azure AD-lösenordsskydd](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Ladda ned programvaran

Det finns två nödvändiga installationsprogram för Azure AD-lösenordsskydd. De är tillgängliga från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Installera och konfigurera proxytjänsten lösenordsskydd

1. Välj en eller flera servrar som värd för proxytjänsten lösenordsskydd.
   * Varje sådan tjänst kan endast ange lösenordsprinciper för en enda skog. Värddatorn måste vara ansluten till en domän i skogen. Rot- och underordnade domäner stöds både. Du behöver nätverksanslutning mellan minst en Domänkontrollant i varje domän i skogen och datorn för skydd av lösenord.
   * Du kan köra proxy-tjänsten på en domänkontrollant för testning. Men domänkontrollanten sedan kräver anslutning till internet, vilket kan vara en säkerhetsfunktion. Vi rekommenderar den här konfigurationen endast för testning.
   * Vi rekommenderar minst två proxyservrar för redundans. Se [hög tillgänglighet](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Installera Azure AD lösenord Protection Proxy-tjänsten med den `AzureADPasswordProtectionProxySetup.exe` installationsprogram för programvara.
   * Installationen av programmet kräver inte en omstart. Programinstallationen kan automatiseras med hjälp av MSI standardprocedurerna, till exempel:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Tjänsten Windows-brandväggen måste köras innan du installerar AzureADPasswordProtectionProxySetup.msi-paketet för att undvika ett fel vid installation. Om Windows-brandväggen är konfigurerad för att inte köra kan är lösningen att tillfälligt aktivera och köra tjänsten brandväggen under installationen. Proxy-programvara har inga särskilda beroenden på Windows-brandväggen efter installationen. Om du använder en brandvägg från tredje part, måste den konfigureras för att uppfylla kraven för en distribution. Dessa inkluderar så att inkommande åtkomst till port 135 och proxy RPC-server-port. Se [distributionskrav](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Öppna ett PowerShell-fönster som administratör.
   * Proxy-programvaran lösenord protection innehåller en ny PowerShell-modul *AzureADPasswordProtection*. Följande steg för att köra olika cmdletar från PowerShell-modulen. Importera den nya modulen enligt följande:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Kontrollera att tjänsten körs genom att använda följande PowerShell-kommando:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Resultatet ska visa en **Status** ”körs”.

1. Registrera proxyn.
   * När steg 3 är slutfört, körs proxytjänsten på datorn. Men tjänsten ännu har inte de behörighet som krävs för att kommunicera med Azure AD. Det krävs registrering med Azure AD:

     `Register-AzureADPasswordProtectionProxy`

     Denna cmdlet kräver autentiseringsuppgifter som global administratör för din Azure-klient. Du måste också domänadministratörsbehörighet i den lokala Active Directory i rotdomänen i skogen. När det här kommandot lyckas när för en proxytjänst, ytterligare anrop av den lyckas men är onödiga.

      Den `Register-AzureADPasswordProtectionProxy` cmdlet har stöd för följande tre autentiseringslägen.

     * Läge för interaktiv autentisering:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Det här läget fungerar inte på Server Core-operativsystem. Använd i stället något av följande autentiseringslägen. Det här läget kan också misslyckas om Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera den konfigurationen, registrera proxyn och sedan aktivera det igen.

     * Autentiseringsläge för enhet-kod:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Slutför autentiseringen genom att följa anvisningarna som visas på en annan enhet.

     * Tyst () lösenordsbaserade läge:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Det går inte att det här läget om Azure Multi-Factor Authentication krävs. I så fall kan använda en av de föregående två autentiseringslägena.

       Du för närvarande inte behöver ange den *- ForestCredential* parametern, som är reserverat för framtida funktioner.

   
   Registreringen av proxytjänsten lösenordsskydd krävs bara en gång av tjänsten. Efter det utför proxytjänsten automatiskt andra nödvändiga underhåll.

   > [!TIP]
   > Det kan finnas en märkbar fördröjning innan slutförande första gången som den här cmdleten körs för en specifik Azure-klient. Om inte ett fel rapporteras, oroa dig inte om den här fördröjningen.

1. Registrera skogen.
   * Du måste initiera en lokal Active Directory-skog med autentiseringsuppgifterna som krävs för att kommunicera med Azure med hjälp av den `Register-AzureADPasswordProtectionForest` PowerShell-cmdlet. Cmdlet: en kräver autentiseringsuppgifter som global administratör för din Azure-klient. Det kräver också en lokal Active Directory domänadministratörsbehörighet i rotdomänen i skogen. Det här steget körs en gång per skog.

      Den `Register-AzureADPasswordProtectionForest` cmdlet har stöd för följande tre autentiseringslägen.

     * Läge för interaktiv autentisering:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Det här läget fungerar inte på Server Core-operativsystem. I stället använda en av följande två autentiseringslägen. Det här läget kan också misslyckas om Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera den konfigurationen, registrera proxyn och sedan aktivera det igen.  

     * Autentiseringsläge för enhet-kod:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Slutför autentiseringen genom att följa anvisningarna som visas på en annan enhet.

     * Tyst () lösenordsbaserade läge:
        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Det går inte att det här läget om Azure Multi-Factor Authentication krävs. I så fall kan använda en av de föregående två autentiseringslägena.

       De här exemplen lyckas bara om den inloggade användaren också är administratör för Active Directory-domän för rotdomänen. Om detta inte är fallet, kan du ange alternativa domänautentiseringsuppgifter via den *- ForestCredential* parametern.

   > [!NOTE]
   > Om flera proxyservrar är installerade i din miljö, spelar vilken proxyserver som du använder för att registrera skogen.

   > [!TIP]
   > Det kan finnas en märkbar fördröjning innan slutförande första gången som den här cmdleten körs för en specifik Azure-klient. Om inte ett fel rapporteras, oroa dig inte om den här fördröjningen.

   Registrering av Active Directory-skogen krävs bara en gång i livslängden för skogen. Efter det utför Domain Controller agenter i skogen automatiskt alla nödvändiga underhåll. Efter `Register-AzureADPasswordProtectionForest` körningar har för en skog, ytterligare anrop-cmdlet: ens lyckas men är onödiga.

   För `Register-AzureADPasswordProtectionForest` ska lyckas, minst en domänkontrollant som kör Windows Server 2012 eller senare måste vara tillgängliga i proxyserverns domän. Men DC-agentprogramvaran behöver inte installeras på alla domänkontrollanter innan det här steget.

1. Konfigurera proxytjänsten lösenordsskydd kommunicera via en HTTP-proxy.

   Om din miljö kräver användning av en specifik HTTP-proxy för att kommunicera med Azure kan du använda den här metoden: Skapa en *AzureADPasswordProtectionProxy.exe.config* filen i mappen %ProgramFiles%\Azure AD Proxy\Service för skydd av lösenord. Inkludera följande innehåll:

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

   Om HTTP-proxy kräver autentisering, lägger du till den *useDefaultCredentials* tagg:

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

   I båda fallen ersätter `http://yourhttpproxy.com:8080` med adressen och porten för din specifika HTTP-proxyserver.

   Om HTTP-proxy har konfigurerats för oss en auktoriseringsprincip måste du ge åtkomst till Active Directory-datorkontot på den dator som är värd för proxytjänsten lösenordsskydd.

   Vi rekommenderar att du stoppa och starta om proxytjänsten när du skapar eller uppdaterar den *AzureADPasswordProtectionProxy.exe.config* fil.

   Proxy-tjänsten stöder inte användning av autentiseringsuppgifter för att ansluta till en HTTP-proxy.

1. Valfritt: Konfigurera proxytjänsten lösenordsskydd att lyssna på en viss port.
   * DC-agentprogramvaran lösenordsskydd på domänkontrollanter använder RPC över TCP kan kommunicera med proxytjänsten. Proxy-tjänsten lyssnar på alla tillgängliga dynamiska RPC-slutpunkt som standard. Men du kan konfigurera tjänsten för att lyssna på en specifik TCP-port, om detta krävs på grund av nätverkets topologi eller krav på brandvägg i miljön.
      * <a id="static" /></a>För att konfigurera tjänsten körs under en statisk port, använda den `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.
         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

      * Använd samma procedur för att konfigurera tjänsten körs under en dynamisk port, men ange *StaticPort* till noll:
         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

   > [!NOTE]
   > Proxytjänsten lösenordsskydd kräver en manuell omstart efter varje ändring i portkonfiguration. Men du behöver inte starta om programvaran för DC-Agent-tjänsten på domänkontrollanter när du har gjort dessa ändringar i konfigurationen.

   * Om du vill fråga efter den aktuella konfigurationen av tjänsten, använda den `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Installera DC-Agenttjänst

   Installera DC-agenttjänsten lösenordsskydd med hjälp av den `AzureADPasswordProtectionDCAgentSetup.msi` paketet.

   Programvaruinstallation eller avregistrera-installationen kräver en omstart. Detta beror lösenordsfiltret DLL: er endast läsas in eller tas bort från minnet efter en omstart.

   Du kan installera DC-agenttjänsten på en dator som inte ännu är en domänkontrollant. I det här fallet att tjänsten ska starta och köra men vara inaktiv innan datorn tilldelas behörighet för att vara en domänkontrollant.

   Du kan automatisera installationen av programmet med hjälp av MSI standardprocedurerna. Exempel:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > Msiexec kommando här gör att startas om direkt. För att undvika som kan använda den `/norestart` flaggan.

Installationen är klar när DC klientprogrammet har installerats på en domänkontrollant och datorn startas om. Ingen konfiguration är obligatorisk eller möjligt.

## <a name="multiple-forest-deployments"></a>Flera skogar distributioner

Det finns inga ytterligare krav för att distribuera Azure AD-lösenordsskydd i flera skogar. Varje skog konfigureras oberoende enligt beskrivningen i avsnittet ”sammanhållna distribution”. Varje proxy för skydd av lösenord har endast stöd för domänkontrollanter från den skog som den är ansluten till. Lösenord protection-programmet i en skog inte känner till lösenordet programvara som distribueras i andra skogar, oavsett förtroendekonfigurationer i Active Directory.

## <a name="read-only-domain-controllers"></a>Skrivskyddade domänkontrollanter

Lösenord ändringar eller anger bearbetas inte och sparas på skrivskyddade domänkontrollanter (RODC). De vidarebefordras till skrivbara domänkontrollanter. Så behöver du installera klientprogrammet DC på skrivskyddade domänkontrollanter.

## <a name="high-availability"></a>Hög tillgänglighet

Det huvudsakliga tillgänglighet problemet lösenordsskydd är tillgängligheten för proxy-servrar när domänkontrollanter i en skog som försöker hämta nya principer eller andra data från Azure. Varje DC-agenten använder en enkel round-robin-style-algoritm när du bestämmer vilken proxyserver för att anropa. Agenten hoppar över proxyservrar som inte svarar. Två proxyservrar för mest fullständigt anslutna Active Directory-distributioner som har felfri replikering av tillståndet för både directory och sysvol-mappen är tillräckligt för att säkerställa tillgänglighet. Detta resulterar i god tid nedladdningen av nya principer och andra data. Men du kan distribuera ytterligare proxy-servrar.

Design av Agentprogrammet som DC minskar risken för vanliga problem som är associerade med hög tillgänglighet. DC-agentens har ett lokalt cacheminne med den nyligen hämtade lösenordsprincipen. DC-agenter fortsätter att genomdriva sin cachelagrade lösenordsprincip även om alla registrerade proxy-servrar som blir otillgänglig. En rimlig uppdateringsfrekvensen för lösenordsprinciper i en stor distribution är vanligtvis *dagar*, inte timmar eller mindre. Därför påverka kort avbrott av proxy-servrar inte Azure AD-lösenordsskydd.

## <a name="next-steps"></a>Nästa steg

Nu när du har installerat de tjänster som du behöver för Azure AD-lösenordsskydd på dina lokala servrar [utföra konfiguration efter installationen och samla rapportinformation](howto-password-ban-bad-on-premises-operations.md) att slutföra distributionen.

[Översikt över Azure AD-lösenordsskydd](concept-password-ban-bad-on-premises.md)
