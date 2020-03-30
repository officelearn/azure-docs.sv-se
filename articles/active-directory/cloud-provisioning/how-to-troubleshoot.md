---
title: Felsökning av azure AD Connect-molnetablering
description: I den här artikeln beskrivs felsöka problem som kan uppstå med molnetableringsagenten.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549493"
---
# <a name="cloud-provisioning-troubleshooting"></a>Felsökning av molnetablering

Cloud etablering berör många olika saker och har många olika beroenden. Denna breda räckvidd kan ge upphov till olika problem. Den här artikeln hjälper dig att felsöka dessa problem. Det introducerar de typiska områdena för dig att fokusera på, hur man samlar in ytterligare information, och de olika tekniker du kan använda för att spåra problem.


## <a name="common-troubleshooting-areas"></a>Vanliga felsökningsområden

|Namn|Beskrivning|
|-----|-----|
|[Agent problem](#agent-problems)|Kontrollera att agenten har installerats korrekt och att den kommunicerar med Azure Active Directory (Azure AD).|
|[Problem med objektsynkronisering](#object-synchronization-problems)|Använd etableringsloggar för att felsöka problem med objektsynkronisering.|
|[Etablering av problem i karantän](#provisioning-quarantined-problems)|Förstå etablering karantän problem och hur du åtgärdar dem.|


## <a name="agent-problems"></a>Agent problem
Några av de första sakerna som du vill verifiera med agenten är:

-  Är det installerat?
-  Är agenten igång lokalt?
-  Är agenten i portalen?
-  Är agenten märkt som frisk?

Dessa objekt kan verifieras i Azure-portalen och på den lokala servern som kör agenten.

### <a name="azure-portal-agent-verification"></a>Verifiering av Azure Portal-agent

Följ dessa steg för att verifiera att agenten visas av Azure och är felfri.

1. Logga in på Azure Portal.
1. Till vänster väljer du **Azure Active Directory** > **Azure AD Connect**. Välj **Hantera etablering (förhandsgranskning)** i mitten .
1. På skärmen **Azure AD-etablering (förhandsversion)** väljer du **Granska alla agenter**.

   ![Granska alla agenter](media/how-to-install/install7.png)</br>
 
1. På skärmen **Lokala etableringsagenter** visas de agenter som du har installerat. Kontrollera att agenten i fråga är där och är märkt *Felfri*.

   ![Skärmen Lokalt etableringsagenter](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Verifiera porten

Om du vill kontrollera att Azure lyssnar på port 443 och att din agent kan kommunicera med den använder du följande verktyg:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Det här testet verifierar att dina agenter kan kommunicera med Azure via port 443. Öppna en webbläsare och gå till föregående URL från servern där agenten är installerad.

![Kontroll av porternas nåbarhet](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>På den lokala servern

Följ dessa steg för att kontrollera att agenten körs.

1. Öppna **Tjänster** på servern med agenten installerad genom att antingen navigera till den eller genom att gå till **Start** > **Run** > **Services.msc**.
1. Under **Tjänster**kontrollerar du att **Microsoft Azure AD Connect Agent Updater** och **Microsoft Azure AD Connect-etableringsagenten** finns där och att deras status *körs*.

   ![Skärmen Tjänster](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Installationsproblem för vanliga agenter

I följande avsnitt beskrivs några vanliga agentinstallationsproblem och typiska lösningar.

#### <a name="agent-failed-to-start"></a>Agenten kunde inte starta

Ett felmeddelande kan visas:

**Tjänsten Microsoft Azure AD Connect-etableringsagenten kunde inte startas. Kontrollera att du har tillräcklig behörighet för att starta systemtjänsterna.** 

Det här problemet orsakas vanligtvis av en grupprincip som förhindrade att behörigheter tillämpades på det lokala NT-tjänstloggkontot som skapats av installationsprogrammet (NT SERVICE\AADConnectProvisioningAgent). Dessa behörigheter krävs för att starta tjänsten.

LÃ¶s problemet genom att sÃ¥ att lÃ¶sa.

1. Logga in på servern med ett administratörskonto.
1. Öppna **tjänster** genom att antingen navigera till den eller genom att gå till **Start** > **Run** > **Services.msc**.
1. Dubbelklicka på **Microsoft Azure AD Connect-etableringsagent under** **Tjänster**.
1. Ändra **Det här kontot** till en domänadministratör på fliken **Logga** in. Starta sedan om tjänsten. 

   ![Fliken Logga in](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Agent time out eller certifikat är ogiltigt

Du kan få följande felmeddelande när du försöker registrera agenten.

![Felmeddelande om time out](media/how-to-troubleshoot/troubleshoot4.png)

Det här problemet orsakas vanligtvis av att agenten inte kan ansluta till hybrididentitetstjänsten och kräver att du konfigurerar en HTTP-proxy. LÃ¶s problemet genom att konfigurera en utgående proxy. 

Etableringsagenten stöder användning av en utgående proxy. Du kan konfigurera den genom att redigera agentkonfigurationsfilen *C:\Program\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Lägg till följande rader i den, mot slutet `</configuration>` av filen strax före den avslutande taggen.
Ersätt variablerna `[proxy-server]` `[proxy-port]` och med proxyserverns namn och portvärden.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Agentregistrering misslyckas med säkerhetsfel

Du kan få ett felmeddelande när du installerar molnetableringsagenten.

Det här problemet orsakas vanligtvis av att agenten inte kan köra PowerShell-registreringsskripten på grund av lokala PowerShell-körningsprinciper.

LÃ¶s problemet genom att ändra powershell-körningsprinciperna på servern. Du måste ha principer för maskin och användare som *odefinierade* eller *fjärrsignerade*. Om de är *inställda*som obegränsade visas det här felet. Mer information finns i [PowerShells körningsprinciper](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Loggfiler

Som standard avger agenten minimala felmeddelanden och stackspårningsinformation. Du hittar dessa spårningsloggar i mappen *C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace*.

Så här samlar du in ytterligare information om felsökning av agentrelaterade problem.

1. Stoppa tjänsten **Microsoft Azure AD Connect Provisioning Agent**.
1. Skapa en kopia av den ursprungliga konfigurationsfilen: *C:\Program\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Ersätt det `<system.diagnostics>` befintliga avsnittet med följande, och alla spårningsmeddelanden går till filen *ProvAgentTrace.log*.

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <add name="textWriterListener"/>
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
         </sharedListeners>
     </system.diagnostics>
    
   ```
1. Starta tjänsten **Microsoft Azure AD Connect Provisioning Agent**.
1. Använd följande kommando för att skugga filen och felsöka problem. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problem med objektsynkronisering

Följande avsnitt innehåller information om felsökning av objektsynkronisering.

### <a name="provisioning-logs"></a>Etableringsloggar

I Azure-portalen kan etableringsloggar användas för att spåra och felsöka problem med objektsynkronisering. Om du vill visa loggarna väljer du **Loggar**.

![Knappen Loggar](media/how-to-troubleshoot/log1.png)

Etableringsloggar ger en mängd information om tillståndet för de objekt som synkroniseras mellan din lokala Active Directory-miljö och Azure.

![Etablering loggar skärmen](media/how-to-troubleshoot/log2.png)

Du kan använda listrutorna högst upp på sidan för att filtrera vyn till noll på specifika problem, till exempel datum. Dubbelklicka på en enskild händelse för att se ytterligare information.

![Information om etablering av loggar](media/how-to-troubleshoot/log3.png)

Den här informationen innehåller detaljerade steg och var synkroniseringsproblemet uppstår. På så sätt kan du lokalisera den exakta platsen för problemet.


## <a name="provisioning-quarantined-problems"></a>Etablering av problem i karantän

Molnetablering övervakar hälsotillståndet för din konfiguration och placerar felaktiga objekt i karantäntillstånd. Om de flesta eller alla anrop som görs mot målsystemet konsekvent misslyckas på grund av ett fel, till exempel ogiltiga administratörsautentiseringsuppgifter, markeras etableringsjobbet som i karantän.

![Karantänstatus](media/how-to-troubleshoot/quarantine1.png)

Genom att välja status kan du se ytterligare information om karantänen. Du kan också hämta felkoden och meddelandet.

![Information om karantänstatus](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Lösa en karantän

- Använd Azure-portalen för att starta om etableringsjobbet. På agentkonfigurationssidan väljer du **Starta om etablering**.

  ![Starta om etablering](media/how-to-troubleshoot/quarantine3.png)

- Använd Microsoft Graph för att [starta om etableringsjobbet](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Du har full kontroll över vad du startar om. Du kan välja att rensa:
  - Spärrar om du vill starta om spärrräknaren som ackumuleras mot karantänstatus.
  - Karantän, för att ta bort programmet från karantän.
  - Vattenstämplar. 
  
  Använd följande begäran:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)



