---
title: Distribuera lokalt Azure AD-lösenordsskydd
description: Lär dig hur du planerar och distribuerar Azure AD-lösenordsskydd i en lokal Active Directory Domain Services-miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671762"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planera och distribuera lokalt Azure Active Directory-lösenordsskydd

Användare skapar ofta lösenord som använder vanliga lokala ord som en skola, ett idrottslag eller en känd person. Dessa lösenord är lätta att gissa, och svaga mot ordlistebaserade attacker. För att framtvinga starka lösenord i din organisation ger Azure Active Directory (Azure AD) lösenordsskydd en global och anpassad lista över förbjudna lösenord. En begäran om lösenordsändring misslyckas om det finns en matchning i listan över förbjudna lösenord.

För att skydda din lokala AD DS-miljö (Active Directory Domain Services) kan du installera och konfigurera Azure AD Password Protection så att den fungerar med din on-prem DC. Den här artikeln visar hur du installerar och registrerar proxytjänsten azure AD Password Protection och Azure AD Password Protection DC-agenten i din lokala miljö.

Mer information om hur Azure AD-lösenordsskydd fungerar i en lokal miljö finns i [Så här framtvingar du Azure AD-lösenordsskydd för Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Strategi för distribution

Följande diagram visar hur de grundläggande komponenterna i Azure AD Password Protection fungerar tillsammans i en lokal Active Directory-miljö:

![Så här fungerar Azure AD Password Protection-komponenter](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Det är en bra idé att granska hur programvaran fungerar innan du distribuerar den. Mer information finns i [Begreppsmässig översikt över Azure AD Password Protection](concept-password-ban-bad-on-premises.md).

Vi rekommenderar att du *audit* startar distributioner i granskningsläge. Granskningsläge är standardinställningen, där lösenord kan fortsätta att ställas in. Lösenord som skulle blockeras registreras i händelseloggen. När du har distribuerat proxyservrarna och DC-agenterna i granskningsläge övervakar du den inverkan som lösenordsprincipen kommer att ha på användarna när principen tillämpas.

Under granskningsfasen anser många organisationer att följande situationer gäller:

* De måste förbättra befintliga operativa processer för att använda säkrare lösenord.
* Användare använder ofta osäkra lösenord.
* De måste informera användarna om den kommande ändringen av säkerhetskontrollen, eventuell påverkan på dem och hur du väljer säkrare lösenord.

Det är också möjligt att validera ett starkare lösenord för att påverka din befintliga automatisering av distribution av Active Directory-domänkontrollanter. Vi rekommenderar att minst en DC-befordran och en DC-degradering sker under utvärderingen av revisionsperioden för att hjälpa till att upptäcka sådana problem. Mer information finns i följande artiklar:

* [Ntdsutil.exe kan inte ange ett svagt lösenord för reparationsläge för katalogtjänster](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Replikkampanjen för domänkontrollant misslyckas på grund av ett svagt lösenord för reparationsläge för katalogtjänster](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Degraderingen av domänkontrollanten misslyckas på grund av ett svagt lokalt administratörslösenord](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

När funktionen har körts i granskningsläge under en rimlig period kan du växla konfigurationen från *Granskning* till *Tvinga* till att kräva säkrare lösenord. Ytterligare övervakning under denna tid är en bra idé.

### <a name="multiple-forest-considerations"></a>Flera skogsöverväganden

Det finns inga ytterligare krav för att distribuera Azure AD-lösenordsskydd över flera skogar.

Varje skog är oberoende konfigurerad enligt beskrivningen i följande avsnitt för att [distribuera azure AD-lösenordsskydd för prem](#download-required-software). Varje Azure AD Password Protection proxy kan bara stödja domänkontrollanter från skogen som den är ansluten till.

Azure AD Password Protection-programvaran i alla skogar är omedvetna om lösenordsskyddsprogram som distribueras i andra skogar, oavsett Active Directory-förtroendekonfigurationer.

### <a name="read-only-domain-controller-considerations"></a>Hänsyn till skrivskyddad domänkontrollant

Lösenordsändring eller ange händelser bearbetas inte och sparas på skrivskyddade domänkontrollanter (RODC). I stället vidarebefordras de till skrivbara domänkontrollanter. Du behöver inte installera Azure AD Password Protection DC agent programvara på RODCs.

Vidare stöds det inte att köra proxytjänsten Azure AD Password Protection på en skrivskyddad domänkontrollant.

### <a name="high-availability-considerations"></a>Överväganden om hög tillgänglighet

Det viktigaste problemet med lösenordsskydd är tillgängligheten för Azure AD Password Protection proxyservrar när datorerna i en skog försöker hämta nya principer eller andra data från Azure. Varje Azure AD Password Protection DC-agent använder en enkel algoritm för round-robin-stil när du bestämmer vilken proxyserver som ska anropas. Agenten hoppar över proxyservrar som inte svarar.

För de flesta fullständigt anslutna Active Directory-distributioner som har felfri replikering av både katalog- och sysvol-mapptillstånd räcker det med två Azure AD Password Protection-proxyservrar för att säkerställa tillgänglighet. Den här konfigurationen resulterar i snabb hämtning av nya principer och andra data. Du kan distribuera ytterligare Azure AD Password Protection proxyservrar om så önskas.

Utformningen av Azure AD Password Protection DC-agentprogramvaran minskar de vanliga problemen som är associerade med hög tillgänglighet. Dc-agenten för Azure AD Password Protection upprätthåller en lokal cache med den senast nedladdade lösenordsprincipen. Även om alla registrerade proxyservrar blir otillgängliga fortsätter AZURE AD Password Protection DC-agenterna att tillämpa sin cachelagrade lösenordsprincip.

En rimlig uppdateringsfrekvens för lösenordsprinciper i en stor distribution är vanligtvis dagar, inte timmar eller mindre. Korta avbrott på proxyservrarna påverkar därför inte Azure AD Password Protection nämnvärt.

## <a name="deployment-requirements"></a>Distributionskrav

Information om licensiering finns i [licenskraven för Azure AD Password Protection](concept-password-ban-bad.md#license-requirements).

Följande grundläggande krav gäller:

* Alla datorer, inklusive domänkontrollanter, som har Azure AD Password Protection-komponenter installerade måste ha Universal C Runtime installerat.
    * Du kan få körningen genom att se till att du har alla uppdateringar från Windows Update. Eller så kan du få det i ett OS-specifikt uppdateringspaket. Mer information finns i [Uppdatera för Universell C Runtime i Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Du behöver ett konto som har active directory-domänadministratörsbehörighet i skogens rotdomän för att registrera Active Directory-skogen i Windows Server med Azure AD.
* Nyckeldistributionstjänsten måste vara aktiverad på alla domänkontrollanter i domänen som kör Windows Server 2012. Som standard aktiveras den här tjänsten via manuell utlösarstart.
* Nätverksanslutningen måste finnas mellan minst en domänkontrollant i varje domän och minst en server som är värd för proxytjänsten för Azure AD Password Protection. Den här anslutningen måste göra det möjligt för domänkontrollanten att komma åt RPC-slutpunktsmappningsport 135 och RPC-serverporten på proxytjänsten.
    * Som standard är RPC-serverporten en dynamisk RPC-port, men den kan konfigureras för att [använda en statisk port](#static).
* Alla datorer där Azure AD Password Protection Proxy-tjänsten installeras måste ha nätverksåtkomst till följande slutpunkter:

    |**Slutpunkt**|**Syfte**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Autentiseringsbegäranden|
    |`https://enterpriseregistration.windows.net`|Azure AD-lösenordsskydd|

### <a name="azure-ad-password-protection-dc-agent"></a>DC-agent för Azure AD-lösenordsskydd

Följande krav gäller för DC-agenten för lösenordsskydd för Azure AD:

* Alla datorer där Azure AD Password Protection DC-agentprogramvaran installeras måste köra Windows Server 2012 eller senare.
    * Active Directory-domänen eller skogen behöver inte finnas på Windows Server 2012-domänens funktionalitetsnivå (DFL) eller skogens funktionalitetsnivå (FFL). Som nämnts i [Design Principles](concept-password-ban-bad-on-premises.md#design-principles), det finns ingen minsta DFL eller FFL krävs för antingen DC agent eller proxy programvara för att köra.
* Alla datorer som kör DC-agenten för Azure AD Password Protection måste ha .NET 4.5 installerat.
* Alla Active Directory-domäner som kör DC-agenttjänsten för skydd av Azure AD-lösenord måste använda DFSR (Distributed File System Replication) för sysvol-replikering.
   * Om din domän inte redan använder DFSR måste du migrera innan du installerar Azure AD Password Protection. Mer information finns i [SYSVOL Replication Migration Guide: FRS till DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Dc-agentprogram för Azure AD-lösenordsskydd installeras för närvarande på domänkontrollanter i domäner som fortfarande använder FRS (den föregående tekniken till DFSR) för sysvol-replikering, men programvaran fungerar INTE korrekt i den här miljön.
    >
    > Ytterligare negativa biverkningar inkluderar enskilda filer som inte replikeras, och sysvol återställningsprocedurer som verkar lyckas men tyst misslyckas med att replikera alla filer.
    >
    > Migrera din domän för att använda DFSR så snart som möjligt, både för DFSR:s inneboende fördelar och för att häva blockeringen av Azure AD Password Protection. Framtida versioner av programvaran inaktiveras automatiskt när du kör i en domän som fortfarande använder FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD-proxytjänst för lösenordsskydd

Följande krav gäller för proxytjänsten för Azure AD-lösenordsskydd:

* Alla datorer där proxytjänsten Azure AD Password Protection installeras måste köra Windows Server 2012 R2 eller senare.

    > [!NOTE]
    > Azure AD Password Protection proxy tjänstdistribution är ett obligatoriskt krav för att distribuera Azure AD lösenordsskydd även om domänkontrollanten kan ha utgående direkt internetanslutning.

* Alla datorer där proxytjänsten Azure AD Password Protection installeras måste ha .NET 4.7 installerat.
    * .NET 4.7 bör redan installeras på en helt uppdaterad Windows Server. Om det behövs hämtar och kör du installationsprogrammet som finns i [offlineinstallationsprogrammet för Windows .NET Framework 4.7](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Alla datorer som är värdar för proxytjänsten för Azure AD-lösenordsskydd måste konfigureras för att ge domänkontrollanter möjlighet att logga in på proxytjänsten. Den här möjligheten styrs via behörighetstilldelningen "Åtkomst till den här datorn från nätverket".
* Alla datorer som är värdar för proxytjänsten för Azure AD-lösenordsskydd måste konfigureras för att tillåta utgående TLS 1.2 HTTP-trafik.
* Ett *globalt administratörskonto* för att registrera proxytjänsten Azure AD Password Protection och skogen med Azure AD.
* Nätverksåtkomst måste vara aktiverat för den uppsättning portar och webbadresser som anges i [inställningsprocedurerna för programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Förutsättningar för Microsoft Azure AD Connect Agent Updater

Microsoft Azure AD Connect Agent Updater-tjänsten installeras sida vid sida med Azure AD Password Protection Proxy-tjänsten. Ytterligare konfiguration krävs för att tjänsten Microsoft Azure AD Connect Agent Updater ska kunna fungera:

* Om din miljö använder en HTTP-proxyserver följer du riktlinjerna i [Arbeta med befintliga lokala proxyservrar](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).
* Microsoft Azure AD Connect Agent Updater-tjänsten kräver också de TLS 1.2-steg som anges i [TLS-kraven](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

> [!WARNING]
> Azure AD Password Protection proxy och Azure AD Application Proxy installerar olika versioner av Microsoft Azure AD Connect Agent Updater-tjänsten, vilket är anledningen till att instruktionerna refererar till Application Proxy-innehåll. Dessa olika versioner är inkompatibla när de installeras sida vid sida, så det rekommenderas inte att installera Azure AD Password Protection Proxy och Application Proxy på samma dator.

## <a name="download-required-software"></a>Ladda ner nödvändig programvara

Det finns två nödvändiga installationsprogram för en lokal Azure AD-distribution för lösenordsskydd:

* AZURE AD Password Protection DC-agent (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD-lösenordsskydd proxy (*AzureADPasswordProtectionProxySetup.exe*)

Hämta båda installationerna från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Installera och konfigurera proxytjänsten

Proxytjänsten för Azure AD-lösenordsskydd finns vanligtvis på en medlemsserver i din lokala AD DS-miljö. När azure AD-proxytjänsten har installerats kommuniceras med Azure AD för att underhålla en kopia av de globala och kund förbjudna lösenordslistorna för din Azure AD-klientorganisation.

I nästa avsnitt installerar du DC-agenterna för Azure AD-lösenordsskydd på domänkontrollanter i din lokala AD DS-miljö. Dessa DC-agenter kommunicerar med proxytjänsten för att få de senaste förbjudna lösenordslistorna för användning vid bearbetning av lösenordsändringshändelser inom domänen.

Välj en eller flera servrar som ska vara värdar för proxytjänsten för Azure AD-lösenordsskydd. Följande överväganden gäller för servern/servrarna:

* Varje sådan tjänst kan bara tillhandahålla lösenordsprinciper för en enskild skog. Värddatorn måste anslutas till en domän i skogen. Rot- och underordnade domäner stöds båda. Du behöver nätverksanslutning mellan minst en domänkontrollant i varje domän i skogen och lösenordsskyddsdatorn.
* Du kan köra proxytjänsten Azure AD Password Protection på en domänkontrollant för testning, men den domänkontrollanten kräver sedan internetanslutning. Den här anslutningen kan vara ett säkerhetsproblem. Vi rekommenderar den här konfigurationen endast för testning.
* Vi rekommenderar minst två Azure AD Password Protection proxyservrar för redundans, vilket anges i föregående avsnitt om [hög tillgänglighet överväganden](#high-availability-considerations).
* Det stöds inte för att köra proxytjänsten Azure AD Password Protection på en skrivskyddad domänkontrollant.

Så här installerar du proxytjänsten för Azure AD Password Protection:

1. Om du vill installera proxytjänsten för `AzureADPasswordProtectionProxySetup.exe` Azure AD Password Protection kör du programinstallationsprogrammet.

    Programvaruinstallationen kräver ingen omstart och kan automatiseras med hjälp av standard-MSI-procedurer, som i följande exempel:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Windows-brandväggen måste köras innan `AzureADPasswordProtectionProxySetup.exe` du installerar paketet för att undvika ett installationsfel.
    >
    > Om Windows-brandväggen är konfigurerad för att inte köras är lösningen att tillfälligt aktivera och köra brandväggstjänsten under installationen. Proxyprogramvaran har inget specifikt beroende av Windows-brandväggen efter installationen.
    >
    > Om du använder en brandvägg från tredje part måste den fortfarande vara konfigurerad för att uppfylla distributionskraven. Dessa inkluderar att tillåta inkommande åtkomst till port 135 och proxy RPC-serverporten. Mer information finns i föregående avsnitt om [distributionskrav](#deployment-requirements).

1. Azure AD Password Protection proxyprogramvara innehåller `AzureADPasswordProtection`en ny PowerShell-modul, . Följande steg kör olika cmdlets från den här PowerShell-modulen.

    Om du vill använda den här modulen öppnar du ett PowerShell-fönster som administratör och importerar den nya modulen på följande sätt:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Om du vill kontrollera att proxytjänsten för Azure AD Password Protection körs använder du följande PowerShell-kommando:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Resultatet ska visa **status för** att *köra*.

1. Proxytjänsten körs på datorn, men har inga autentiseringsuppgifter för att kommunicera med Azure AD. Registrera proxyservern för Azure AD-lösenordsskydd med Azure AD med cmdleten. `Register-AzureADPasswordProtectionProxy`

    Den här cmdleten kräver globala administratörsbehörighet för din Azure-klient. Du behöver också lokala active directory-domänadministratörsbehörighet i skogens rotdomän. Den här cmdleten måste också köras med ett konto med lokal administratörsbehörighet:

    När det här kommandot lyckas en gång för en Azure AD Password Protection proxytjänst, ytterligare anrop av det lyckas, men är onödiga.

    Cmdlet `Register-AzureADPasswordProtectionProxy` stöder följande tre autentiseringslägen. De två första lägena stöder Azure Multi-Factor Authentication, men det tredje läget gör det inte.

    > [!TIP]
    > Det kan finnas en märkbar fördröjning innan slutförandet första gången som den här cmdleten körs för en specifik Azure-klientorganisation. Om inte ett fel rapporteras, oroa dig inte för denna försening.

     * Interaktivt autentiseringsläge:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Det här läget fungerar inte på Server Core-operativsystem. Använd i stället något av följande autentiseringslägen. Det här läget kan också misslyckas om förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera den konfigurationen, registrera proxyn och sedan återaktivera den.

     * Autentiseringsläge för enhetskod:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        När du uppmanas att följa länken för att öppna en webbläsare och ange autentiseringskoden.

     * Tyst (lösenordsbaserat) autentiseringsläge:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Det här läget misslyckas om Azure Multi-Factor Autentisering krävs för ditt konto. I så fall använder du ett av de två föregående autentiseringslägena eller använder i stället ett annat konto som inte kräver MFA.
        >
        > Du kan också se MFA krävs om Azure Device Registration (som används under täcker av Azure AD Lösenordsskydd) har konfigurerats för att globalt kräva MFA. För att lösa detta krav kan du använda ett annat konto som stöder MFA med ett av de två föregående autentiseringslägena, eller så kan du också tillfälligt inaktivera MFA-kravet för Azure Device Registration.
        >
        > Om du vill göra den här ändringen söker du efter och väljer **Azure Active Directory** i Azure-portalen och väljer sedan Enheter > **Enhetsinställningar**. Ange **Kräv multifaktorauth för att ansluta enheter** till *Nr*. Se till att konfigurera om den här inställningen tillbaka till *Ja* när registreringen är klar.
        >
        > Vi rekommenderar att MFA-kraven kringgås endast för teständamål.

    Du behöver för närvarande inte ange parametern *-ForestCredential,* som är reserverad för framtida funktioner.

    Registrering av proxytjänsten azure AD Password Protection är endast nödvändig en gång under tjänstens livstid. Därefter kommer azure AD Password Protection proxytjänsten automatiskt att utföra allt annat nödvändigt underhåll.

1. Registrera nu den lokala Active Directory-skogen med nödvändiga autentiseringsuppgifter för att kommunicera med Azure med hjälp av `Register-AzureADPasswordProtectionForest` PowerShell-cmdleten.

    > [!NOTE]
    > Om flera Azure AD Password Protection-proxyservrar är installerade i din miljö spelar det ingen roll vilken proxyserver du använder för att registrera skogen.

    Cmdlet kräver globala administratörsbehörighet för din Azure-klient. Du måste också köra den här cmdleten med ett konto med lokal administratörsbehörighet. Det kräver också lokala Active Directory Enterprise Administrator-privilegier. Det här steget körs en gång per skog.

    Cmdlet `Register-AzureADPasswordProtectionForest` stöder följande tre autentiseringslägen. De två första lägena stöder Azure Multi-Factor Authentication, men det tredje läget gör det inte.

    > [!TIP]
    > Det kan finnas en märkbar fördröjning innan slutförandet första gången som den här cmdleten körs för en specifik Azure-klientorganisation. Om inte ett fel rapporteras, oroa dig inte för denna försening.

     * Interaktivt autentiseringsläge:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Det här läget fungerar inte på Server Core-operativsystem. Använd i stället något av följande två autentiseringslägen. Det här läget kan också misslyckas om förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera den konfigurationen, registrera skogen och sedan återaktivera den.  

     * Autentiseringsläge för enhetskod:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        När du uppmanas att följa länken för att öppna en webbläsare och ange autentiseringskoden.

     * Tyst (lösenordsbaserat) autentiseringsläge:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Det här läget misslyckas om Azure Multi-Factor Autentisering krävs för ditt konto. I så fall använder du ett av de två föregående autentiseringslägena eller använder i stället ett annat konto som inte kräver MFA.
        >
        > Du kan också se MFA krävs om Azure Device Registration (som används under täcker av Azure AD Lösenordsskydd) har konfigurerats för att globalt kräva MFA. För att lösa detta krav kan du använda ett annat konto som stöder MFA med ett av de två föregående autentiseringslägena, eller så kan du också tillfälligt inaktivera MFA-kravet för Azure Device Registration.
        >
        > Om du vill göra den här ändringen söker du efter och väljer **Azure Active Directory** i Azure-portalen och väljer sedan Enheter > **Enhetsinställningar**. Ange **Kräv multifaktorauth för att ansluta enheter** till *Nr*. Se till att konfigurera om den här inställningen tillbaka till *Ja* när registreringen är klar.
        >
        > Vi rekommenderar att MFA-kraven kringgås endast för teständamål.

       Dessa exempel lyckas bara om den inloggade användaren också är active directory-domänadministratör för rotdomänen. Om så inte är fallet kan du ange alternativa domänautentiseringsuppgifter via parametern *-ForestCredential.*

    Registrering av Active Directory-skogen är endast nödvändig en gång under skogens livstid. Därefter utför Azure AD Password Protection DC-agenter i skogen automatiskt allt annat nödvändigt underhåll. Efter `Register-AzureADPasswordProtectionForest` körningar framgångsrikt för en skog lyckas ytterligare åkallor av cmdleten, men är onödiga.
    
    För `Register-AzureADPasswordProtectionForest` att lyckas måste minst en DOMÄNKONTROLLANT som kör Windows Server 2012 eller senare vara tillgänglig i proxyserverns domän för Azure AD-lösenordsskydd. Dc-agentprogram för Azure AD-lösenordsskydd behöver inte installeras på några domänkontrollanter före det här steget.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Konfigurera proxytjänsten så att den kommunicerar via en HTTP-proxy

Om din miljö kräver användning av en specifik HTTP-proxy för att kommunicera med Azure, gör du så här för att konfigurera Azure AD Password Protection-tjänsten.

Skapa en *AzureADPasswordProtectionProxy.exe.config-fil* i `%ProgramFiles%\Azure AD Password Protection Proxy\Service` mappen. Inkludera följande innehåll:

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

Om HTTP-proxyn kräver autentisering lägger du till taggen *useDefaultCredentials:*

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

Ersätt i båda `http://yourhttpproxy.com:8080` fallen med adressen och porten till den specifika HTTP-proxyservern.

Om HTTP-proxyn är konfigurerad för att använda en auktoriseringsprincip måste du bevilja åtkomst till Active Directory-datorkontot för den dator som är värd för proxytjänsten för lösenordsskydd.

Vi rekommenderar att du stoppar och startar om proxytjänsten för Azure AD Password Protection när du har skapat eller uppdaterat filen *AzureADPasswordProtectionProxy.exe.config.*

Proxytjänsten stöder inte användning av specifika autentiseringsuppgifter för anslutning till en HTTP-proxy.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Konfigurera proxytjänsten för att lyssna på en viss port

Azure AD Password Protection DC-agentprogramvaran använder RPC över TCP för att kommunicera med proxytjänsten. Som standard lyssnar proxytjänsten för Azure AD Password Protection på alla tillgängliga dynamiska RPC-slutpunkter. Du kan konfigurera tjänsten så att den lyssnar på en viss TCP-port om det behövs på grund av nätverkstopologi eller brandväggskrav i din miljö.

<a id="static" /></a>Om du vill konfigurera tjänsten så att `Set-AzureADPasswordProtectionProxyConfiguration` den körs under en statisk port använder du cmdleten enligt följande:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Du måste stoppa och starta om proxytjänsten för Azure AD-lösenordsskydd för att ändringarna ska börja gälla.

Om du vill konfigurera tjänsten så att den körs under en dynamisk port använder du samma procedur men ställer in *StaticPort* till noll:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Du måste stoppa och starta om proxytjänsten för Azure AD-lösenordsskydd för att ändringarna ska börja gälla.

Proxytjänsten azure AD Password Protection kräver en manuell omstart efter alla ändringar i portkonfigurationen. Du behöver inte starta om dc-agenttjänsten för lösenordsskydd för Azure AD på domänkontrollanter när du har gjort dessa konfigurationsändringar.

Om du vill fråga efter den `Get-AzureADPasswordProtectionProxyConfiguration` aktuella konfigurationen av tjänsten använder du cmdleten som visas i följande exempel

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

Följande exempelutdata visar att proxytjänsten azure AD Password Protection använder en dynamisk port:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Installera dc-agenttjänsten

Om du vill installera DC-agenttjänsten `AzureADPasswordProtectionDCAgentSetup.msi` för Azure AD-lösenordsskydd kör du paketet.

Du kan automatisera programvaruinstallationen med hjälp av vanliga MSI-procedurer, som visas i följande exempel:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

Flaggan `/norestart` kan utelämnas om du föredrar att installera om datorn startas om automatiskt.

Programvaruinstallationen, eller avinstallationen, kräver en omstart. Det här kravet beror på att DLL-filer för lösenordsfilter endast läses in eller tas bort av en omstart.

Installationen av Azure AD Password Protection på prem slutförs när DC-agentprogramvaran har installerats på en domänkontrollant och den datorn startas om. Ingen annan konfiguration krävs eller är möjlig. Lösenordsändringshändelser mot on-prem-datorer använder de konfigurerade förbjudna lösenordslistorna från Azure AD.

Information om hur du aktiverar azure AD-lösenordsskydd för prem från Azure-portalen eller konfigurerar anpassade förbjudna lösenord finns [i Aktivera lokalt Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Du kan installera DC-agenten för Azure AD Password Protection på en dator som ännu inte är en domänkontrollant. I det här fallet startar och körs tjänsten men förblir inaktiv tills datorn befordras till domänkontrollant.

## <a name="upgrading-the-proxy-service"></a>Uppgradera proxytjänsten

Proxytjänsten azure AD Password Protection stöder automatisk uppgradering. Automatisk uppgradering använder tjänsten Microsoft Azure AD Connect Agent Updater, som installeras sida vid sida med proxytjänsten. Automatisk uppgradering är aktiverad som standard och kan `Set-AzureADPasswordProtectionProxyConfiguration` aktiveras eller inaktiveras med hjälp av cmdleten.

Den aktuella inställningen kan `Get-AzureADPasswordProtectionProxyConfiguration` efterfrågas med hjälp av cmdlet. Vi rekommenderar att inställningen för automatisk uppgradering alltid är aktiverad.

Cmdleten `Get-AzureADPasswordProtectionProxy` kan användas för att fråga programvaruversionen av alla installerade Azure AD Password Protection proxyservrar i en skog.

### <a name="manual-upgrade-process"></a>Manuell uppgraderingsprocess

En manuell uppgradering sker genom att köra `AzureADPasswordProtectionProxySetup.exe` den senaste versionen av programinstallationsprogrammet. Den senaste versionen av programvaran finns på [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Det krävs inte att avinstallera den aktuella versionen av proxytjänsten Azure AD Password Protection - installationsprogrammet utför en uppgradering på plats. Ingen omstart bör krävas vid uppgradering av proxytjänsten. Uppgraderingen av programvaran kan automatiseras med `AzureADPasswordProtectionProxySetup.exe /quiet`hjälp av vanliga MSI-procedurer, till exempel .

## <a name="upgrading-the-dc-agent"></a>Uppgradera DC-agenten

När en nyare version av Azure AD Password Protection DC-agentprogramvaran är tillgänglig, utförs uppgraderingen genom att köra den senaste versionen av `AzureADPasswordProtectionDCAgentSetup.msi` programvarupaketet. Den senaste versionen av programvaran finns på [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Det krävs inte att avinstallera den aktuella versionen av DC-agentprogramvaran - installationsprogrammet utför en uppgradering på plats. En omstart krävs alltid vid uppgradering av DC-agentprogramvaran - det här kravet orsakas av windows-kärnbeteende.

Uppgraderingen av programvaran kan automatiseras med `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`hjälp av vanliga MSI-procedurer, till exempel .

Du kan utelämna `/norestart` flaggan om du föredrar att installera om datorn startas om automatiskt.

Cmdleten `Get-AzureADPasswordProtectionDCAgent` kan användas för att fråga programvaruversionen av alla installerade Azure AD Password Protection DC-agenter i en skog.

## <a name="next-steps"></a>Nästa steg

Nu när du har installerat de tjänster som du behöver för Azure AD Password Protection på dina lokala [servrar, aktivera on-prem Azure AD lösenordsskydd i Azure-portalen](howto-password-ban-bad-on-premises-operations.md) för att slutföra distributionen.
