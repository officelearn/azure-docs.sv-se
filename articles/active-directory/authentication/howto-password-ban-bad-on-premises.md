---
title: Distribuera förhandsversionen av Azure AD lösenord protection
description: Distribuera Azure AD lösenord skydd förhandsgranskning för att förbjuda felaktiga lösenord på lokalt
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 6fda373f832d6e24d1252587a19c88b0f464dda6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232419"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Förhandsversion: Distribuera lösenordsskydd för Azure AD

|     |
| --- |
| Azure AD-lösenordsskydd och lösenordslistan över anpassade förbjudna är förhandsversion funktioner i Azure Active Directory. Läs mer om förhandsgranskningar [kompletterande användningsvillkor för Microsoft Azure-förhandsgranskningar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Nu när vi har en förståelse av [metod för tvingande skyddet av Azure AD-lösenord för Windows Server Active Directory](concept-password-ban-bad-on-premises.md), nästa steg är att planera och köra distributionen.

## <a name="deployment-strategy"></a>Distributionsstrategi

Föreslår vi att alla distributionen startar i granskningsläge. Granskningsläget är den första standardinställningen om lösenord kan fortsätta att vara satt och något som skulle blockera skapa poster i händelseloggen. När fullständigt proxyservrar och DC agenter distribueras i granskningsläge regular övervakning ska göras för att avgöra vilken inverkan lösenordsprincip tvingande skulle få på användare och miljön om principen har tvingats fram.

Många organisationer hitta under steget granskning:

* De behöver för att förbättra befintliga operativa processer för att använda fler säkra lösenord.
* Känner igen att regelbundet välja osäkra lösenord
* De behöver för att informera användare om den kommande ändringen i säkerhet tvingande, hur det kan ha på dem och hjälpa dem att bättre förstå hur de kan välja mer säkra lösenord.

När funktionen har körts i granskningsläge i rimlig tid, tvingande konfiguration kan bläddra från **granska** till **framtvinga** vilket kräver mer säkra lösenord. Fokuserad övervakning under denna tid är en bra idé.

## <a name="known-limitation"></a>Kända begränsningar

Det finns en känd begränsning i av förhandsversionen av Azure AD lösenord skydd proxy. Användning av innehavaradministratörskonton som kräver MFA stöds inte. En framtida uppdatering av Azure AD lösenord skydd proxy stöder Proxyregistrering med administratörskonton som kräver MFA.

## <a name="single-forest-deployment"></a>En enkel skog distribution

Förhandsversionen av Azure AD-lösenordsskydd distribueras med proxytjänsten på upp till två servrar och DC-agenttjänsten inkrementellt kan distribueras till alla domänkontrollanter i Active Directory-skogen.

![Hur arbetar tillsammans för Azure AD lösenordskomponenter för skydd](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Ladda ned programvaran

Det finns två nödvändiga installationsprogram för Azure AD-lösenordsskydd som kan hämtas från den [Microsoft download center](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Installera och konfigurera Azure AD lösenord skydd proxy-tjänsten

1. Välj en eller flera servrar i Azure AD lösenord skydd proxy-tjänsten.
   * Varje dessa tjänster kan endast ange lösenordsprinciper för en enskild skog och värddatorn måste vara domänansluten till en domän (rot- och underordnade både lika stöds) i den skogen. För Azure AD lösenord protection proxy service att utföra sitt uppdrag, måste det finnas nätverksanslutningen mellan minst en Domänkontrollant i varje domän i skogen och värddator för Azure AD lösenord skydd Proxy.
   * Det finns stöd för att installera och köra Azure AD lösenord skydd proxy-tjänsten på en domänkontrollant i testsyfte men kräver internet-anslutning.

   > [!NOTE]
   > Förhandsversion stöder högst två (2)-proxyservrar per skog.

2. Installera tjänsten för lösenord för Proxy-programvara med AzureADPasswordProtectionProxy.msi MSI-paketet.
   * Installationen av programmet kräver inte en omstart. Installationen av programmet kan automatiseras med hjälp av MSI standardprocedurerna, till exempel: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. Öppna ett PowerShell-fönster som administratör.
   * Azure AD-lösenordsskydd Proxy programvaran innehåller en ny PowerShell-modulen med namnet AzureADPasswordProtection. Följande steg baseras på kör olika cmdletar från PowerShell-modulen och förutsätter att du har öppnat en ny PowerShell-fönster och importerade ny modul på följande sätt:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > Installationen av programvaran ändrar den värddatorn PSModulePath miljövariabeln. Du kan behöva öppna ett nytt PowerShell-konsolfönster för den här ändringen ska börja gälla så att powershell-modulen AzureADPasswordProtection kan importeras och användas.

   * Kontrollera att tjänsten körs med följande PowerShell-kommando: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Resultatet bör ge upphov till ett resultat med den **Status** ett ”körs” resultat returneras.

4. Registrera proxyn.
   * När steg 3 har slutförts Azure AD lösenord skydd proxy-tjänsten körs på datorn, men ännu har inte de behörighet som krävs för att kommunicera med Azure AD. Registrering med Azure AD som krävs för att aktivera denna möjlighet med hjälp av den `Register-AzureADPasswordProtectionProxy` PowerShell-cmdlet. Cmdlet måste global administratör autentiseringsuppgifter för din Azure-klient som lokal administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen. När den har slutförts för en viss proxy-tjänst, ytterligare anrop för `Register-AzureADPasswordProtectionProxy` att fungera, men behövs inte.
      * Cmdleten kan köras på följande sätt:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         Exemplet fungerar bara om den inloggade användaren också är administratör för Active Directory-domän för rotdomänen. Ett alternativ är att ange autentiseringsuppgifter för nödvändiga domänen via den `-ForestCredential` parameter.

   > [!TIP]
   > Det kan finnas en betydande fördröjning (antal sekunder) första gången denna cmdlet har körts för en viss Azure-klient innan cmdleten är slutfört. Om ett fel rapporteras bör inte den här fördröjningen anses oroväckande.

   > [!NOTE]
   > Registreringen av tjänsten Azure AD lösenord protection proxy förväntas vara ett enstaka steg av tjänsten. Proxytjänsten utför automatiskt andra nödvändiga maintainenance från och med nu och senare. När den har slutförts för en viss skog ytterligare anrop av 'Registrera AzureADPasswordProtectionProxy' att fungera men är onödiga.

5. Registrera skogen.
   * Lokala Active Directory-skogen måste initieras med autentiseringsuppgifterna som krävs för att kommunicera med Azure med hjälp av `Register-AzureADPasswordProtectionForest` Powershell-cmdlet. Cmdlet måste global administratör autentiseringsuppgifter för din Azure-klient som lokal administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen. Det här steget körs en gång per skog.
      * Cmdleten kan köras på följande sätt:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         Exemplet fungerar bara om den inloggade användaren också är administratör för Active Directory-domän för rotdomänen. Ett alternativ är att ange autentiseringsuppgifter för nödvändiga domänen via parametern - ForestCredential.

         > [!NOTE]
         > Det spelar ingen roll vilken proxyserver som ovan ovan körs när om flera proxyservrar är installerade i din miljö.

         > [!TIP]
         > Det kan finnas en betydande fördröjning (antal sekunder) första gången denna cmdlet har körts för en viss Azure-klient innan cmdleten är slutfört. Om ett fel rapporteras bör inte den här fördröjningen anses oroväckande.

   > [!NOTE]
   > Registrering av Active Directory-skogen förväntas vara ett enstaka steg av skogen. Domain controller agenter som körs i skogen utför automatiskt andra nödvändiga maintainenance från och med nu och senare. När den har slutförts för en viss skog ytterligare anrop av `Register-AzureADPasswordProtectionForest` att fungera, men behövs inte.

6. Valfritt: Konfigurera Azure AD lösenord skydd proxy-tjänsten för att lyssna på en viss port.
   * RPC via TCP används av Azure AD-lösenordsskydd DC Agentprogrammet på domänkontrollanter för att kommunicera med Azure AD lösenord skydd proxy-tjänsten. Som standard lyssnar Azure AD-lösenordsskydd lösenord princip Proxy-tjänsten på alla tillgängliga dynamiska RPC-slutpunkt. Om det behövs på grund av nätverkstopologin eller krav för brandväggen konfigureras tjänsten i stället så att den lyssnar på en viss TCP-port.
      * Använd för att konfigurera tjänsten körs under en statisk port i `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

      * För att konfigurera tjänsten körs under en dynamisk port, Använd samma procedur men StaticPort tillbaka till noll, t.ex:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

   > [!NOTE]
   > Azure AD lösenord skydd proxy-tjänsten kräver en manuell omstart efter varje ändring i portkonfigurationen för. Du behöver inte starta om programmet DC agent-tjänsten körs på domänkontrollanter efter konfigurationsändringar av den här typen.

   * Den aktuella konfigurationen av tjänsten kan efterfrågas med den `Get-AzureADPasswordProtectionProxyConfiguration` som visas i följande exempel:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Installera Azure AD lösenord skydd DC agent-tjänsten

* Installera Azure AD lösenord skydd DC agent service programvara med hjälp av den `AzureADPasswordProtectionDCAgent.msi` MSI-paketet:
   * Programvaruinstallationen kräver en omstart på Installera och avinstallera på grund av kravet på operativsystem att lösenord filter-DLL: er endast läsas in eller minnet på en omstart.
   * Det går för att installera tjänsten DC-agent på en dator som inte ännu är en domänkontrollant. I det här fallet startar tjänsten och kör men ska annars inaktiveras tills när datorn befordras en domänkontrollant.

   Installationen av programmet kan automatiseras med hjälp av MSI standardprocedurerna, till exempel:  `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Detta kommando msiexec leder till att startas; Detta kan undvikas genom att ange den `/norestart` flaggan.

När du installerade på en domänkontrollant och startas om, har Azure AD-lösenordsskydd DC Agent Programvaruinstallation slutförts. Ingen konfiguration är obligatorisk eller möjligt.

## <a name="multiple-forest-deployments"></a>Distributioner med flera skogar

Det finns inga ytterligare krav för att distribuera Azure AD-lösenordsskydd över flera skogar. Varje skog konfigureras separat enligt beskrivningen i avsnittet skog distribution. Varje Azure AD-lösenordsskydd Proxy stöder bara domänkontrollanter från skogen som den är ansluten till. Azure AD lösenord protection-programmet i en viss skog är inte medveten om av Azure AD lösenord protection program som distribueras i en annan skog oavsett konfigurationer för Active Directory-förtroende.

## <a name="read-only-domain-controllers"></a>Skrivskyddade domänkontrollanter

Lösenordet changes\sets aldrig bearbetas och sparat på skrivskyddade domänkontrollanter (RODC) i stället överförs dessa till skrivbara domänkontrollanter. Det finns därför behöver du inte installera DC-agenten på skrivskyddade domänkontrollanter.

## <a name="high-availability"></a>Hög tillgänglighet

Det största problemet med att säkerställa hög tillgänglighet av Azure AD-lösenordsskydd är tillgängligheten för proxy-servrar när domänkontrollanter i en skog försöker ladda ned nya principer eller andra data från Azure. Förhandsversion stöder högst två proxyservrar per skog. Varje DC-agenten använder en algoritm för enkelt resursallokering format när du bestämmer vilka proxyserver för att anropa och hoppar över proxy-servrar som inte svarar.
Vanliga problem med hög tillgänglighet begränsas avsiktligt av Agentprogrammet som Domänkontrollant. DC-agentens ett lokalt cacheminne med den nyligen hämtade lösenordsprincipen. DC-agenterna fortsätter att genomdriva sin cachelagrade lösenordsprincip även om alla registrerade proxyservrar inaktiveras av någon anledning. En rimlig uppdateringsfrekvensen för lösenordsprinciper i en stor distribution är vanligtvis på ordern dagar inte timmar eller mindre.   Därför orsakar kort avbrott i proxyservrar inte betydande påverkan på användningen av Azure AD-lösenord skyddsfunktionen eller dess säkerhetsfördelarna.

## <a name="next-steps"></a>Nästa steg

Nu när du har installerat de tjänster som krävs för skydd av Azure AD-lösenord på lokala servrar kan du slutföra de [efter installera konfiguration och rapportinformation samla](howto-password-ban-bad-on-premises-operations.md) att slutföra din distribution.

[Översikt över Azure AD-lösenordsskydd](concept-password-ban-bad-on-premises.md)
