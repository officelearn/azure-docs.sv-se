---
title: Azure AD Connect fel sökning av moln etablering
description: I det här dokumentet beskrivs hur du felsöker problem som kan uppstå med moln etablerings agenten.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794242"
---
# <a name="cloud-provisioning-troubleshooting"></a>Fel sökning av moln etablering

Moln etablering vidrör många olika saker och har många olika beroenden.  Detta kan ge upphov till olika problem.  Det här dokumentet är utformat för att komma igång med att felsöka problemen.  I det här dokumentet får du en introduktion till de typiska områden som du bör fokusera på, hur du samlar in ytterligare information och olika tekniker som kan användas för att spåra problem.  


## <a name="common-troubleshooting-areas"></a>Vanliga fel söknings områden

|Namn|Beskrivning|
|-----|-----|
|[Agent problem](#agent-issues)|Kontrol lera att agenten har installerats på rätt sätt och att den kommunicerar med Azure AD.|
|[Problem med synkronisering av objekt](#object-synchronization-issues)|Använd etablerings loggar för att felsöka problem med att synkronisera objekt.|
|[Etablering av karantän problem](#provisioning-quarantined-issues)|Lär dig hur du kan åtgärda karantän problem och hur du åtgärdar dem.|


## <a name="agent-issues"></a>Agent problem
Några av de första saker som du vill verifiera med agenten är:


-  är det installerat?
-  körs agenten lokalt?
-  är agenten i portalen?
-  har agenten marker ATS som felfri?  

Dessa objekt kan verifieras i Azure Portal och på den lokala server som kör-agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agent verifiering

För att verifiera att agenten ses av Azure och är felfritt följer du dessa steg:

1. Logga in på Azure Portal.
2. Till vänster väljer du **Azure Active Directory**, klickar på **Azure AD Connect** och i mitten väljer du **Hantera etablering (för hands version)** .
3.  På skärmen **Azure AD Provisioning (för hands version)** klickar du på **Granska alla agenter**.
 ![Azure AD-etablering](media/how-to-install/install7.png)</br>
 
4. På **skärmen lokala etablerings agenter** visas de agenter som du har installerat.  Kontrol lera att agenten i fråga finns där och har marker ATS som **felfri**.
 ![etablerings agenter](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Verifiera porten

För att verifiera att Azure lyssnar på port 443 och att agenten kan kommunicera med den, kan du använda följande verktyg:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Det här testet kontrollerar att dina agenter kan kommunicera med Azure via port 443.  Öppna en webbläsare och gå till ovanstående URL från servern där agenten är installerad.
 ![Tjänster](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>På den lokala servern

Verifiera att agenten körs genom att följa dessa steg:

1.  På den server där agenten är installerad öppnar du **tjänster** genom att antingen navigera till den eller genom att gå till starta/köra/Services. msc.
2.  Under **tjänster**kontrollerar du att **Microsoft Azure AD ansluter agent uppdaterings** **agenten och Microsoft Azure AD ansluta etablerings agenten** finns där, och statusen **körs**.
 ![Tjänster](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>Vanliga problem med agent installation

Följande är några vanliga problem med agent installation och vad den typiska lösningen är.

#### <a name="agent-failed-to-start"></a>Det gick inte att starta agenten

Om du får ett fel meddelande om tillstånd:

**Det gick inte att starta tjänsten Microsfoft Azure AD Connect etablerings agenten.  Kontrol lera att du har behörighet att starta system tjänsterna.** 

Detta orsakas vanligt vis av en grup princip som hindrade behörigheter från att tillämpas på det lokala NT-tjänstens inloggnings konto som skapats av installations programmet (NT SERVICE\AADConnectProvisioningAgent). dessa behörigheter krävs för att starta tjänsten.

Använd följande steg för att lösa problemet:

1.  Logga in på servern med ett administratörs konto
2.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till starta/köra/Services. msc.
3.  Under **tjänster** dubbelklickar du på **Microsoft Azure AD ansluta etablerings agent**
4. På fliken ändrar du "inloggnings konto" till en domän administratör och startar om tjänsten. 

 ![Services](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Agentens tids gräns eller certifikat är ogiltigt

Du kan få följande fel om du försöker registrera agenten.

 ![Services](media/how-to-troubleshoot/troubleshoot4.png)

Detta orsakas vanligt vis av att agenten inte kan ansluta till hybrid identitets tjänsten och kräver konfigurering av HTTP-proxy.  För att lösa det här konfigurerar du en utgående proxy. 

Etablerings agenten stöder användning av utgående proxy. Du kan konfigurera den genom att redigera agentens konfigurations fil **C:\Program Files\Microsoft Azure AD Connect etablering Agent\AADConnectProvisioningAgent.exe.config**. Lägg till följande rader i den i slutet av filen precis innan du stänger `</configuration>`-taggen.
Ersätt variablerna [proxy-server] och [proxy-port] med proxyserverns namn och port värden.

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

#### <a name="agent-registration-fails-with-security-error"></a>Agent registreringen Miss lyckas med ett säkerhets fel

Följande fel kan visas när du installerar Cloud Provisioning-agenten.

Detta orsakas vanligt vis av att agenten inte kan köra PowerShell-registrerings skripten på grund av lokala PowerShell-körnings principer.

Lös detta genom att ändra körnings principerna för PowerShell på servern. Du måste ha dator-och användar principer som "odefinierade" eller "RemoteSigned". Om den är "obegränsad" visas det här felet.  Mer information finns i [principer för körning av PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Loggfiler

Som standard avger agenten mycket minimala fel meddelanden och stack spårnings information. Du kan hitta dessa spårnings loggar i mappen: **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect-etablering Agent\Trace**

Använd följande steg för att samla in ytterligare information om fel sökning av Agent-relaterade problem.

1. Stoppa tjänsten **Microsoft Azure AD ansluta etablerings agenten**
2. Skapa en kopia av den ursprungliga konfigurations filen: **C:\Program Files\Microsoft Azure AD Connect etablering Agent\AADConnectProvisioningAgent.exe.config**
3. Ersätt den befintliga < system. Diagnostics >-avsnittet med följande och alla spårnings meddelanden kommer att gå till filen **ProvAgentTrace. log**

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
4. Starta tjänsten **Microsoft Azure AD ansluta etablerings agenten**
5. Du kan använda följande kommando för att ange fil-och fel söknings problem: 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>Problem med synkronisering av objekt

Följande avsnitt innehåller information om fel sökning av objekt synkronisering.

### <a name="provisioning-logs"></a>Etableringsloggar

I Azure Portal kan etablerings loggar användas för att spåra och felsöka problem med synkronisering av objekt.  Om du vill visa loggarna väljer du **loggar**.
 ![etablerings loggar](media/how-to-troubleshoot/log1.png)

Etablerings loggarna ger en enorm information om status för de objekt som synkroniseras mellan din lokala AD-miljö och Azure.

 ![Etableringsloggar](media/how-to-troubleshoot/log2.png)

Du kan använda List rutorna överst på sidan för att filtrera vyn till noll i vid specifika problem, datum osv.  Att dubbelklicka på en enskild händelse ger ytterligare detaljerad information.

 ![Etableringsloggar](media/how-to-troubleshoot/log3.png)

Den här informationen ger detaljerade anvisningar och var synkroniseringsproblem inträffar.  Det innebär att du kan ha noll i och hitta den exakta punkten av problemet.


## <a name="provisioning-quarantined-issues"></a>Etablering av problem i karantän

Moln etablering övervakar hälso tillståndet för din konfiguration och placerar Felaktiga objekt i tillståndet "karantän". Om de flesta eller alla anrop som görs mot mål systemet fungerar konsekvent på grund av ett fel, till exempel ogiltiga administratörsautentiseringsuppgifter, markeras etablerings jobbet som i karantän.

 ![Vistelse](media/how-to-troubleshoot/quarantine1.png)

Genom att klicka på statusen kan du se mer information om karantänen.  Du kan också hämta fel koden och meddelandet.

 ![Vistelse](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>Lösa en karantän

- Använd Azure Portal för att starta om etablerings jobbet. På sidan agent konfiguration väljer du **starta om etablering**.

  ![Vistelse](media/how-to-troubleshoot/quarantine3.png)

- Använd Microsoft Graph för att [starta om etablerings jobbet](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Du har fullständig kontroll över vad du vill starta om. Du kan välja att rensa escrows (om du vill starta om depositions-räknaren som påförs i karantäns status), rensa karantän (för att ta bort programmet från karantän) eller rensa vattenstämplar. Använd följande begäran:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)



