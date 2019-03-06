---
title: Distribuera Azure AD-lösenordsskydd förhandsversion
description: Distribuera förhandsversionen av Azure AD-lösenordsskydd om du vill förbjuda felaktiga lösenord på plats
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
ms.openlocfilehash: 4f0a009d4bea8a0f0075337d6fcfb9de31b7c7a6
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409660"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Förhandsversion: Distribuera Azure AD-lösenordsskydd

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Nu när vi har en förståelse för [för tvingande Azure AD lösenord skydd för Windows Server Active Directory](concept-password-ban-bad-on-premises.md), nästa steg är att planera och köra distributionen.

## <a name="deployment-strategy"></a>Distributionsstrategi

Föreslår vi att alla distributioner startar i granskningsläge. Granskningsläget är den första standardinställningen där lösenord kan fortsätta att ställas in och något som skulle blockeras skapa poster i händelseloggen. När proxyservrar och DC agenter distribueras helt i granskningsläge, regular övervakning ska göras för att avgöra vilken inverkan lösenordsprincip tvingande skulle ha på användare och miljön om principen har tvingats fram.

Under fasen audit hitta många organisationer:

* De behöver för att förbättra befintliga operativa processer för att använda fler säkra lösenord.
* Användarna är vana vid att regelbundet välja oskyddat lösenord
* De behöver för att informera användarna om den kommande ändringen i security tvingande, hur det kan ha på dem och hjälpa dem att bättre förstå hur de kan välja säkrare lösenord.

När funktionen har körts i granskningsläge i rimlig tid, tvingande konfiguration kan bläddra från **granska** till **tvinga** vilket kräver mer säkra lösenord. Fokuserade övervakning under denna tid är en bra idé.

## <a name="deployment-requirements"></a>Krav för distribution

* Alla domänkontrollanter där Azure AD lösenord DC skyddsagenttjänsten ska installeras måste köra Windows Server 2012 eller senare.
* Alla datorer där Azure AD-lösenord Protection proxytjänsten installeras måste köra Windows Server 2012 R2 eller senare.
* Alla datorer där Azure AD-lösenordsskydd komponenter är installerade, inklusive domänkontrollanter måste ha den universella C som är installerad.
Detta åstadkoms helst genom att helt åtgärda datorn via Windows Update. I annat fall ett lämpligt operativsystemspecifika uppdateringspaketet vara installerat – Se [uppdatera för Universal C-körning i Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Nätverksanslutning måste det finnas mellan minst en domänkontrollant i varje domän och minst en server som värd för Azure AD-lösenord Protection Proxy-tjänsten. Den här anslutningen måste tillåta domänkontrollanten för att få åtkomst till RPC-mapper slutpunktsport (135) och RPC-server-porten i proxy-tjänsten. RPC-serverport är som standard en dynamisk RPC-port, men kan konfigureras (se nedan) för att använda en statisk port.
* Alla datorer som är värd för tjänsten Azure AD-lösenord Protection Proxy måste ha nätverksåtkomst till följande slutpunkter:

    |Slutpunkt |Syfte|
    | --- | --- |
    |`https://login.microsoftonline.com`|Begäranden om autentisering|
    |`https://enterpriseregistration.windows.net`|Azure AD-lösenordsskydd-funktioner|

* Alla datorer som är värd för tjänsten Azure AD-lösenord Protection Proxy måste konfigureras för att tillåta utgående TLS 1.2 HTTP-trafik.
* Ett globalt administratörskonto för att registrera Azure AD-lösenord Protection Proxy-tjänst och skog med Azure AD.
* Ett konto med administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen för att registrera Windows Server Active Directory-skog med Azure AD.
* Alla Active Directory-domän som kör domänkontrollanten agentprogramvaran för tjänsten måste använda DFSR för sysvol-replikering.
* Microsoft Key Distribution Service måste aktiveras på alla Windows Server 2012 och senare domänkontrollanter i domänen (aktiverat via manuella utlösaren start är standardläget för den här tjänsten).

## <a name="single-forest-deployment"></a>Enkel skog distribution

Följande diagram visar hur grundkomponenterna i Azure AD-lösenordsskydd tillsammans i en lokal Active Directory-miljö.

![Hur Azure AD-lösenordsskydd komponenter fungerar tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Innan du distribuerar är det en bra idé att granska hur programvaran fungerar; Se [konceptuell översikt över Azure AD-lösenordsskydd](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Ladda ned programvaran

Det finns två nödvändiga installationsprogram för Azure AD-lösenord skydd som kan laddas ned från den [Microsoft download center](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Installera och konfigurera tjänsten för Azure AD-lösenordsskydd-proxy

1. Välj en eller flera servrar som värd för Azure AD-lösenord Protection Proxy-tjänsten.
   * Varje sådan tjänst kan endast ange lösenordsprinciper för en enda skog och värddatorn måste vara domänansluten till en domän (rot- och underordnade domäner både stöds) i den skogen. Azure AD-lösenord Protection Proxy-tjänsten uppfyller sitt uppdrag måste, det finnas nätverksanslutning mellan minst en Domänkontrollant i varje domän i skogen och den Azure AD-lösenord Protection Proxy-datorn.
   * Det går att installera och köra Azure AD-lösenord Protection Proxy-tjänsten på en domänkontrollant i testsyfte; Nackdelen är att domänkontrollanten och kräver en Internetanslutning som kan vara en säkerhetsfunktion. Microsoft rekommenderar att den här konfigurationen bara kan användas för testning.
   * Minst två proxyservrar rekommenderas för. [Se hög tillgänglighet](howto-password-ban-bad-on-premises-deploy.md#high-availability)

2. Installera Azure AD-lösenord Protection Proxy-tjänsten med hjälp av AzureADPasswordProtectionProxySetup.msi MSI-paketet.
   * Installationen av programmet kräver inte en omstart. Programinstallationen kan automatiseras med hjälp av MSI standardprocedurerna, till exempel: `msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`

      > [!NOTE]
      > Tjänsten Windows-brandväggen måste köras innan du installerar AzureADPasswordProtectionProxySetup.msi MSI-paketet. Annars uppstår ett installationsfel. Om Windows-brandväggen är konfigurerad för att inte köra kan är lösningen att tillfälligt aktivera och starta Windows-brandväggen under installationen. Proxy-programvara har inga särskilda beroenden för Windows-brandväggen programvaran efter installationen. Om du använder en brandvägg från tredje part kan den fortfarande konfigureras för att uppfylla kraven för en distribution (Tillåt inkommande åtkomst till port 135 och proxy RPC-serverport om dynamisk eller statisk). [Se distributionskrav](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Öppna ett PowerShell-fönster som administratör.
   * Azure AD-lösenord Protection proxyprogrammet innehåller en ny PowerShell-modul med namnet AzureADPasswordProtection. Följande steg är baserade på kör olika cmdletar från PowerShell-modulen och förutsätter att du har öppnat ett nytt PowerShell-fönster och att du har importerat den nya modulen enligt följande:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Kontrollera att tjänsten körs med hjälp av följande PowerShell-kommando: `Get-Service AzureADPasswordProtectionProxy | fl`.
     Resultatet bör producera ett resultat med den **Status** ”körs” resultatet returneras.

4. Registrera proxyn.
   * När steg 3 har slutförts Azure AD-lösenord Protection Proxy-tjänsten körs på datorn, men ännu har inte de behörighet som krävs för att kommunicera med Azure AD. Registrering med Azure AD som krävs för att aktivera den möjligheten med hjälp av den `Register-AzureADPasswordProtectionProxy` PowerShell-cmdlet. Cmdlet: en kräver global administratör autentiseringsuppgifter för din Azure-klient som en lokal administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen. När den har slutförts för en viss proxy-tjänst, ytterligare funktionsanrop i `Register-AzureADPasswordProtectionProxy` fortsätta att lyckas men är onödiga.

      Cmdleten Register-AzureADPasswordProtectionProxy stöder tre olika autentiseringslägen på följande sätt.

      * Läge för interaktiv autentisering:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Det här läget fungerar inte på Server Core-operativsystem. I stället använda en av de alternativa autentiseringslägena som beskrivs nedan.

         > [!NOTE]
         > Det här läget kan misslyckas om Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera IESC, registrera proxy, sedan återaktivera IESC.

      * Autentiseringsläge för enhet-kod:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Du kan sedan Slutför autentiseringen genom att följa anvisningarna som visas på en annan enhet.

      * Tyst () lösenordsbaserade läge:

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Det här läget misslyckas om autentiseringen kräver MFA av någon anledning. Om så är fallet, Använd en av de föregående två lägena för att utföra en MFA-baserad autentisering.

      Det krävs inte för närvarande att ange parametern - ForestCredential som är reserverad för framtida funktioner.

   > [!NOTE]
   > Registreringen av Azure AD-lösenordsskydd proxy-tjänsten förväntas vara ett enstaka steg av tjänsten. Proxy-tjänsten utför automatiskt andra nödvändiga Underhåll från och med nu och senare. När den har slutförts för en viss proxy ytterligare anrop för ”registrera AzureADPasswordProtectionProxy' fortsätta att lyckas men är onödiga.

   > [!TIP]
   > Det kan finnas en avsevärd fördröjning (antal sekunder) första gången den här cmdleten körs för en viss Azure-klient innan cmdleten är slutfört. Om inte ett fel rapporteras ska inte den här fördröjningen betraktas oroväckande.

5. Registrera skogen.
   * Den lokala Active Directory-skogen måste initieras med autentiseringsuppgifterna som krävs för att kommunicera med Azure med hjälp av den `Register-AzureADPasswordProtectionForest` PowerShell-cmdlet. Cmdlet: en kräver global administratör autentiseringsuppgifter för din Azure-klient som en lokal administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen. Det här steget körs en gång per skog.

      Cmdleten Register-AzureADPasswordProtectionForest stöder tre olika autentiseringslägen på följande sätt.

      * Läge för interaktiv autentisering:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Det här läget fungerar inte på Server Core-operativsystem. I stället använda en av de alternativa autentiseringslägena som beskrivs nedan.

         > [!NOTE]
         > Det här läget kan misslyckas om Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera IESC, registrera proxy, sedan återaktivera IESC.  

      * Autentiseringsläge för enhet-kod:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Du kan sedan Slutför autentiseringen genom att följa anvisningarna som visas på en annan enhet.

      * Tyst () lösenordsbaserade läge:
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Det här läget misslyckas om autentiseringen kräver MFA. Om så är fallet, Använd en av de föregående två lägena för att utföra en MFA-baserad autentisering.

      I exemplen ovan lyckas bara om den inloggade användaren också är administratör för Active Directory-domän för rotdomänen. Om detta inte är fallet kan du ange alternativa domänautentiseringsuppgifter via parametern - ForestCredential.

   > [!NOTE]
   > Om flera proxyservrar är installerade i din miljö, spelar ingen roll vilken proxyserver används för att registrera skogen.

   > [!TIP]
   > Det kan finnas en avsevärd fördröjning (antal sekunder) första gången den här cmdleten körs för en viss Azure-klient innan cmdleten är slutfört. Om inte ett fel rapporteras ska inte den här fördröjningen betraktas oroväckande.

   > [!NOTE]
   > Registrering av Active Directory-skogen förväntas vara ett enstaka steg i livslängden för skogen. Domain controller-agenter som körs i skogen utför automatiskt andra nödvändiga maintainenance från och med nu och senare. När den har slutförts för en viss skog ytterligare funktionsanrop i `Register-AzureADPasswordProtectionForest` fortsätta att lyckas men är onödiga.

   > [!NOTE]
   > För att `Register-AzureADPasswordProtectionForest` ska lyckas minst en Windows Server 2012 eller senare domain controller måste finnas i proxyserverns domän. Det finns dock inget krav på att DC klientprogrammet installeras på alla domänkontrollanter före det här steget.

6. Konfigurera Azure AD-lösenord Protection Proxy-tjänsten att kommunicera via en HTTP-proxy

   Om din miljö kräver användning av en specifik HTTP-proxy för att kommunicera med Azure, kan detta åstadkommas på följande sätt.

   Skapa en fil med namnet `proxyservice.exe.config` fil i den `%ProgramFiles%\Azure AD Password Protection Proxy\Service` mapp med följande innehåll:

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

   Om HTTP-proxy kräver autentisering, lägger du till taggen useDefaultCredentials på följande sätt:

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

   I båda fallen ska du ersätta `http://yourhttpproxy.com:8080` med adressen och porten för din specifika HTTP-proxyserver.

   Om HTTP-proxy har konfigurerats med en auktoriseringsprincip, måste du bevilja åtkomst till Active Directory-datorkontot för den datorn som är värd för tjänsten Azure AD-lösenord Protection Proxy.

   Du bör stoppa och starta om tjänsten Proxy för skydd av Azure AD-lösenord när du skapar eller uppdaterar den `proxyservice.exe.config` filen.

   Azure AD-lösenord Protection Proxy-tjänsten stöder inte användning av autentiseringsuppgifter för att ansluta till en HTTP-proxy.

7. Valfritt: Konfigurera Azure AD-lösenord Protection Proxy-tjänsten för att lyssna på en specifik port.
   * RPC via TCP används av Azure AD-lösenord Protection DC-agentprogramvaran på domänkontrollanterna för att kommunicera med Azure AD-lösenord Protection Proxy-tjänsten. Azure AD-lösenord Protection Proxy-tjänsten lyssnar på alla tillgängliga dynamiska RPC-slutpunkt som standard. Om det behövs på grund av nätverkets topologi eller krav på Brandvägg för konfigureras tjänsten i stället för att lyssna på en specifik TCP-port.
      * För att konfigurera tjänsten körs under en statisk port, använda den `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

      * För att konfigurera tjänsten körs under en dynamisk port, Använd samma procedur men nollställs StaticPort, t.ex:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

   > [!NOTE]
   > Azure AD-lösenord Protection Proxy-tjänsten kräver en manuell omstart efter varje ändring i portkonfiguration. Du behöver inte starta om den DC service-agentprogramvaran som körs på domänkontrollanter när du har gjort ändringar i konfigurationen av den här typen.

   * Den aktuella konfigurationen av tjänsten kan efterfrågas med hjälp av den `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet enligt följande exempel:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Installera Azure AD-lösenordsskydd DC-Agenttjänst

   Installera Azure AD-lösenord Protection DC agent service programvara med hjälp av den `AzureADPasswordProtectionDCAgent.msi` MSI-paketet

   Programvaruinstallationen kräver en omstart vid installation och avinstallera på grund av kravet på operativsystem att lösenord filter DLL: er endast läsa in eller tas bort från minnet vid en omstart.

   Det går för att installera tjänsten DC-agent på en dator som inte ännu är en domänkontrollant. I det här fallet tjänsten startar och kör men ska annars inaktiveras tills när datorn är tillfrågas för att vara en domänkontrollant.

   Programinstallationen kan automatiseras med hjälp av MSI standardprocedurerna, till exempel:

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Ovanstående exempel msiexec kommando kommer resultera i startas om direkt; Detta kan undvikas genom att ange den `/norestart` flaggan.

När installerad på en domänkontrollant och startas om kan har installationen av Azure AD-lösenord Protection DC-agenten slutförts. Ingen konfiguration är obligatorisk eller möjligt.

## <a name="multiple-forest-deployments"></a>Flera skogar distributioner

Det finns inga ytterligare krav för att distribuera Azure AD-lösenordsskydd i flera skogar. Varje skog konfigureras oberoende enligt beskrivningen i avsnittet skog distribution. Varje Proxy för skydd av Azure AD-lösenord har endast stöd för domänkontrollanter i skogen som den är ansluten till. Azure AD-lösenordsskydd programvaran i en viss skog är inte medveten om av Azure AD-lösenordsskydd program som distribueras i en annan skog oavsett förtroendekonfigurationer i Active Directory.

## <a name="read-only-domain-controllers"></a>Skrivskyddade domänkontrollanter

Lösenord changes\sets aldrig bearbetas och sparas på skrivskyddade domänkontrollanter (RODC); i stället vidarebefordras de till skrivbara domänkontrollanter. Det finns därför behöver inte installera DC-agentprogramvaran på skrivskyddade domänkontrollanter.

## <a name="high-availability"></a>Hög tillgänglighet

Det största problemet för att säkerställa hög tillgänglighet för Azure AD-lösenordsskydd är tillgängligheten för proxy-servrar när domänkontrollanter i en skog försöker hämta nya principer eller andra data från Azure. Varje DC-agenten använder en algoritm för enkelt resursallokering format när du bestämmer vilken proxyserver för att anropa och hoppar över över proxyservrar som inte svarar. Två (2)-proxyservrar ska vara tillräckliga för att säkerställa tillgänglighet och därför tid nedladdningar av nya principer och andra data för flesta av fullständigt anslutna Active Directory-distributioner med felfri replikering (av både directory och sysvol-tillstånd). Ytterligare proxy-servrar kan distribueras som önskas.

Vanliga problem med hög tillgänglighet begränsas av utformningen av DC-agentprogramvaran. DC-agentens har ett lokalt cacheminne med den nyligen hämtade lösenordsprincipen. DC-agenter fortsätter att genomdriva sin cachelagrade lösenordsprincip även om alla registrerade proxy-servrar som blir otillgänglig av någon anledning. En rimlig uppdateringsfrekvensen för lösenordsprinciper i en stor distribution är vanligtvis på för dagar, inte timmar eller mindre. Därför orsakar kort avbrott av proxy-servrar inte betydande påverkan på driften av Azure AD-lösenord skyddsfunktionen eller dess säkerhetsfördelarna.

## <a name="next-steps"></a>Nästa steg

Nu när du har installerat de tjänster som krävs för skydd av Azure AD-lösenord på dina lokala servrar kan du slutföra de [efter installera konfigurations- och samla rapportinformation](howto-password-ban-bad-on-premises-operations.md) att slutföra distributionen.

[Översikt över Azure AD-lösenordsskydd](concept-password-ban-bad-on-premises.md)
