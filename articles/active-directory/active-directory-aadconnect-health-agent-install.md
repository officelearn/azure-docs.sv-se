---
title: Installation av Azure AD Connect Health Agent | Microsoft Docs
description: Den här sidan om Azure AD Connect Health innehåller information om agentinstallationen för AD FS och Sync.
services: active-directory
documentationcenter: ''
author: karavar
manager: femila
editor: karavar

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/05/2016
ms.author: vakarand

---
# Installation av Azure AD Connect Health Agent
Det här dokumentet beskriver hur du installerar och konfigurerar Azure AD Connect Health-agenterna. Du kan ladda ned agenterna [här](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

## Krav
Följande tabell är en lista över kraven för att använda Azure AD Connect Health.

| Krav | Beskrivning |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health är en Azure AD Premium-funktion som kräver Azure AD Premium. </br></br>Mer information finns i [Komma igång med Azure AD Premium](active-directory-get-started-premium.md) </br>Information om hur du startar en kostnadsfri 30-dagars utvärderingsversion finns i [Starta en utvärderingsversion.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Du måste vara en global administratör i din Azure AD för att komma igång med Azure AD Connect Health |Som standard kan endast globala administratörer installera och konfigurera hälsoagenter för att sätta igång, få åtkomst till portalen och utföra åtgärder i Azure AD Connect Health. Mer information finns i [Administrera Azure AD-katalogen](active-directory-administer.md). <br><br> Du kan använda rollbaserad åtkomstkontroll för att ge andra användare i organisationen åtkomst till Azure AD Connect Health. Mer information finns i [Rollbaserad åtkomstkontroll för Azure AD Connect Health.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Viktigt!** Det konto som du använder när du installerar agenter måste vara ett arbets- eller skolkonto. Det kan inte vara ett Microsoft-konto. Mer information finns i [Registrera dig för Azure som en organisation](sign-up-organization.md) |
| Azure AD Connect Health Agent installeras på varje målserver |Azure AD Connect Health kräver att en agent installeras på målservrarna för att kunna tillhandahålla de data som visas på portalen. </br></br>Exempelvis måste agenten installeras på AD FS-servrarna, AD FS-proxyservrarna och AD FS-webbprogramproxyservrarna för att kunna hämta data i din lokala AD FS-infrastruktur. På samma sätt måste agenten installeras på domänkontrollanterna för att hämta data i din lokala AD DS-infrastruktur. </br></br>**Viktigt!** Det konto som du använder när du installerar agenter måste vara ett arbets- eller skolkonto. Det kan inte vara ett Microsoft-konto. Mer information finns i [Registrera dig för Azure som en organisation](sign-up-organization.md) |
| Utgående anslutning till Azure-tjänstens slutpunkter |Under installation och körning kräver agenten anslutning till Azure AD Connect Health-tjänstens slutpunkter. Om du har blockerat utgående anslutningar kontrollerar du att följande slutpunkter finns med i listan över tillåtna anslutningar: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net - Port: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
| Brandväggsportar på servern som agenten körs på. |Följande brandväggsportar måste vara öppna för att agenten ska kunna kommunicera med Azure AD Health-tjänstens slutpunkter.</br></br><li>TCP/UDP-port 443</li><li>TCP/UDP-port 5671</li> |
| Tillåt följande webbplatser om Förbättrad säkerhet i Internet Explorer är aktiverat |Följande webbplatser måste tillåtas om Förbättrad säkerhet i Internet Explorer är aktiverat på servern som agenten ska installeras på.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>Federationsservern för din organisation måste vara betrodd av Azure Active Directory. Exempel: https://sts.contoso.com</li> |

## Installera Azure AD Connect Health Agent för AD FS
Starta agentinstallationen genom att dubbelklicka på EXE-filen som du laddade ned. Klicka på Installera på den första skärmen.

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Klicka på Konfigurera nu när installationen är klar.

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

En kommandotolk öppnas följt av PowerShell-kod som kör Register-AzureADConnectHealthADFSAgent. När du får ett meddelande att du ska logga in på Azure ska du logga in.

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

PowerShell fortsätter efter inloggningen. När den är klar kan du stänga PowerShell så är konfigurationen klar.

Nu bör tjänsterna starta automatiskt och agenten övervakar och samlar in data. Tänk på att du ser varningar i PowerShell-fönstret om du inte uppfyller alla krav som beskrevs i föregående avsnitt. Kontrollera att du uppfyller kraven [här](active-directory-aadconnect-health-agent-install.md#requirements) innan du installerar agenten. Skärmbilden nedan är ett exempel på dessa fel.

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Kontrollera att agenten har installerats genom att leta efter följande tjänster på servern. Dessa tjänster bör köras om du har slutfört konfigurationen. Annars startar de inte förrän konfigurationen är klar.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

### Agentinstallation på Windows Server 2008 R2-servrar
Steg för Windows Server 2008 R2-servrar:

1. Kontrollera att servern körs med Service Pack 1 eller högre.
2. Inaktivera Förbättrad säkerhet i Internet Explorer för agentinstallationen:
3. Installera Windows PowerShell 4.0 på alla servrar innan du installerar AD Health-agenten. Så här installerar du Windows PowerShell 4.0:
   * Installera [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) via följande länk för att hämta offlineinstallationsprogrammet.
   * Installera PowerShell ISE (från Windows-funktioner)
   * Installera [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Installera Internet Explorer version 10 eller senare på servern. (Detta krävs av Health Service för att autentisera dig med dina Azure-administratörsautentiseringsuppgifter.)
4. Mer information om hur du installerar Windows PowerShell 4.0 på Windows Server 2008 R2 finns i wiki-artikeln [här](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### Aktivera granskning för AD FS
> [!NOTE]
> Detta avsnitt gäller endast för AD FS-federationsservrar.
> 
> 

För att funktionen Användningsanalys ska kunna samla in och analysera data behöver Azure AD Connect Health-agenten informationen i AD FS-granskningsloggarna. Dessa loggar är inte aktiverade som standard. Följ stegen nedan för att aktivera AD FS-granskning och hitta AD FS-granskningsloggarna på dina AD FS-servrar.

#### Så här aktiverar du granskning för AD FS 2.0
1. Klicka på **Starta**, peka på **Program**, peka på **Administrationsverktyg** och klicka sedan på **Lokal säkerhetsprincip**.
2. Navigera till mappen **Säkerhetsinställningar\Lokala principer\User Rights Management** och dubbelklicka sedan på Generera säkerhetsgranskningar.
3. På fliken **Lokal säkerhetsinställning** kontrollerar du att AD FS 2.0-tjänstkontot visas. Om det inte visas klickar du på **Lägg till användare eller grupp**, lägger till det i listan och klickar på **OK**.
4. Öppna en kommandotolk med förhöjd behörighet och kör följande kommando för att aktivera granskning: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Stäng Lokal säkerhetsprincip och öppna sedan snapin-modulen för hantering. Öppna snapin-modulen för hantering genom att klicka på **Start**, peka på **Program**, peka på **Administrationsverktyg** och klicka på AD FS 2.0 Management.
6. Klicka på Redigera egenskaper för Federation Service i fönstret Åtgärder.
7. Klicka på fliken **Händelser** i dialogrutan **Egenskaper för Federation Service**.
8. Markera kryssrutorna **Lyckade granskningar** och **Misslyckade granskningar**.
9. Klicka på **OK**.

#### Så här aktiverar du granskning för AD FS i Windows Server 2012 R2
1. Öppna **Lokal säkerhetsprincip** genom att öppna **Serverhanteraren** på startskärmen, eller Serverhanteraren i verktygsfältet på skrivbordet, och klicka sedan på **Verktyg/lokal säkerhetsprincip**.
2. Gå till mappen **Säkerhetsinställningar\Lokala principer\Tilldelning av användarrättigheter** och dubbelklicka sedan på **Generera säkerhetsgranskningar**.
3. På fliken **Lokal säkerhetsinställning** kontrollerar du att AD FS-tjänstkontot visas. Om det inte visas klickar du på **Lägg till användare eller grupp**, lägger till det i listan och klickar på **OK**.
4. Öppna en kommandotolk med förhöjd behörighet och kör följande kommando för att aktivera granskning: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Stäng **Lokal säkerhetsprincip** och öppna sedan snapin-modulen för **AD FS-hantering** (klicka på Verktyg i Serverhanteraren och välj sedan AD FS Management).
6. Klicka på **Redigera egenskaper för Federation Service** i fönstret Åtgärder.
7. Klicka på fliken **Händelser** i dialogrutan Egenskaper för Federation Service.
8. Markera kryssrutorna för **lyckade och misslyckade granskningar** och klicka sedan på **OK**.

#### Så här hittar du AD FS-granskningsloggar
1. Öppna **Loggboken**.
2. Gå till Windows-loggar och välj **Säkerhet**.
3. Klicka på **Filtrera aktuella loggar** till höger.
4. Välj **AD FS-granskning** under Händelsekälla.

![AD FS-granskningsloggar](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [!WARNING]
> Om du har en grupprincip som inaktiverar AD FS-granskning kan Azure AD Connect Health-agenten inte samla in information. Kontrollera att du inte har någon grupprincip som kan inaktivera granskning.
> 
> 

[//]: # (Start of Agent Proxy Configuration Section)

## Installera Azure AD Connect Health-agenten för synkronisering
Azure AD Connect Health-agenten för synkronisering installeras automatiskt i den senaste versionen av Azure AD Connect. Om du vill använda Azure AD Connect för synkronisering måste du ladda ned den senaste versionen av Azure AD Connect och installera den. Du kan hämta den senaste versionen [här](http://www.microsoft.com/download/details.aspx?id=47594).

Kontrollera att agenten har installerats genom att leta efter följande tjänster på servern. Dessa tjänster bör köras om du har slutfört konfigurationen. Annars startar de inte förrän konfigurationen är klar.

* Azure AD Connect Health Sync Insights Service
* Azure AD Connect Health Sync Monitoring Service

![Verifiera Azure AD Connect Health för synkronisering](./media/active-directory-aadconnect-health-sync/services.png)

> [!NOTE]
> Kom ihåg att användningen av Azure AD Connect Health kräver Azure AD Premium. Om du inte har Azure AD Premium kan du inte slutföra konfigurationen på Azure-portalen. Mer information finns på [kravsidan](active-directory-aadconnect-health-agent-install.md#requirements).
> 
> 

## Manuell registrering av Azure AD Connect Health för synkronisering
Om registreringen av agenten för Azure AD Connect Health för synkronisering misslyckas efter installationen av Azure AD Connect kan du använda följande PowerShell-kommando för att registrera agenten manuellt.

> [!IMPORTANT]
> Du behöver bara använda det här PowerShell-kommandot om agentregistreringen misslyckas efter installationen av Azure AD Connect.
> 
> 

PowerShell-kommandot nedan krävs ENDAST om registreringen av hälsoagenten misslyckas efter en lyckad installation och konfiguration av Azure AD Connect. Azure AD Connect Health-tjänsterna startar efter att agenten har registrerats.

Du kan registrera Azure AD Connect Health-agenten för synkronisering manuellt med hjälp av följande PowerShell-kommando:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Kommandot stöder följande parametrar:

* AttributeFiltering: $true (standard) – Om Azure AD Connect inte synkroniserar standardattributen och har anpassats att använda en filtrerad attributuppsättning. Annars $false.
* StagingMode: $false (standard) – Om Azure AD Connect-servern INTE är i mellanlagringsläge och $true om servern är konfigurerad att vara i mellanlagringsläge.

När du uppmanas att autentisera använder du samma globala administratörskonto (till exempel admin@domain.onmicrosoft.com) som användes för att konfigurera Azure AD Connect.

## Installera Azure AD Connect Health Agent för AD FS
Starta agentinstallationen genom att dubbelklicka på EXE-filen som du laddade ned. Klicka på Installera på den första skärmen.

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Klicka på Konfigurera nu när installationen är klar.

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

En kommandotolk öppnas följt av PowerShell-kod som kör Register-AzureADConnectHealthADFSAgent. När du får ett meddelande att du ska logga in på Azure ska du logga in.

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

PowerShell fortsätter efter inloggningen. När den är klar kan du stänga PowerShell så är konfigurationen klar.

Nu bör tjänsterna starta automatiskt och agenten övervakar och samlar in data. Tänk på att du ser varningar i PowerShell-fönstret om du inte uppfyller alla krav som beskrevs i föregående avsnitt. Kontrollera att du uppfyller kraven [här](active-directory-aadconnect-health-agent-install.md#requirements) innan du installerar agenten. Skärmbilden nedan är ett exempel på dessa fel.

![Verifiera Azure AD Connect Health för AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Kontrollera att agenten har installerats genom att leta efter följande tjänster på domänkontrollanten. 

* Azure AD Connect Health AD DS Insights Service
* Azure AD Connect Health AD DS Monitoring Service

Dessa tjänster bör köras om du har slutfört konfigurationen. Annars startar de inte förrän konfigurationen är klar.

![Verifiera Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## Installera Azure AD Connect Health Agent för AD DS på Server Core.
När du har installerat .exe-filen kan du slutföra registreringen genom att använda följande PowerShell-kommando:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## Konfigurera Azure AD Connect Health-agenter att använda HTTP-proxy
Du kan konfigurera Azure AD Connect Health-agenter att fungera med en HTTP-proxy.

> [!NOTE]
> * ”Netsh WinHttp set ProxyServerAddress” fungerar inte eftersom agenten använder System.Net för att skicka webbförfrågningar i stället för Microsoft Windows HTTP Services.
> * Den konfigurerade HTTP-proxyadressen används för att skicka krypterade https-meddelanden.
> * Autentiserade proxyservrar (med HTTPBasic) stöds inte.
> 
> 

### Ändra hälsoagentens proxykonfiguration
Du kan välja följande alternativ när du konfigurerar Azure AD Connect Health Agent att använda en HTTP-proxy.

> [!NOTE]
> Du måste starta om alla Azure AD Connect Health Agent-tjänster för att proxyinställningarna ska uppdateras. Kör följande kommando:<br>
> Restart-Service AdHealth*
> 
> 

#### Importera befintlig proxyinställningar
##### Importera från Internet Explorer
Proxyinställningarna i Internet Explorer HTTP kan importeras för att användas av Azure AD Connect Health-agenter. Kör följande PowerShell-kommando på var och en av de servrar som kör Health-agenten:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importera från WinHTTP
Proxyinställningarna i WinHTTP kan importeras för att användas av Azure AD Connect Health-agenter. Kör följande PowerShell-kommando på var och en av de servrar som kör Health-agenten:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Ange proxyadresser manuellt
Du kan ange en proxyserver manuellt genom att köra följande PowerShell-kommando på alla servrar som kör hälsoagenten:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exempel: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443*

* ”address” kan vara ett DNS-omvandlingsbart servernamn eller en IPv4-adress
* ”port” kan utelämnas. Om det utelämnas används 443 som standardport.

#### Rensa den befintliga proxykonfigurationen
Du kan rensa den befintliga proxykonfigurationen genom att köra följande kommando:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### Läsa de aktuella proxyinställningarna
Du kan använda följande kommando för att läsa de konfigurerade proxyinställningarna:

    Get-AzureAdConnectHealthProxySettings


## Testa anslutningen till Azure AD Connect Health-tjänsten
Problem kan uppstå som kan gör att Azure AD Connect Health-agentens anslutning till Azure AD Connect Health-tjänsten bryts. Exempel på sådana problem är nätverksproblem, behörighetsproblem osv.

Om agenten inte kan skicka data till Azure AD Connect Health-tjänsten under mer än två timmar visas en varning om att ”hälsotjänstens data inte är uppdaterade”. Du kan bekräfta om den berörda Azure AD Connect Health-agenten kan överföra data till Azure AD Connect Health-tjänsten genom att köra följande PowerShell-kommando:

    Test-AzureADConnectHealthConnectivity -Role ADFS

Rollparametern har för närvarande följande värden:

* ADFS
* Sync
* ADDS

Du kan använda flaggan -ShowResults i kommandot om du vill visa detaljerade loggar. Använd följande exempel:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

> [!NOTE]
> Du måste slutföra agentregistreringen innan du kan använda anslutningsverktyget. Om det inte går att slutföra agentregistreringen kontrollerar du att du uppfyller alla [krav](active-directory-aadconnect-health-agent-install.md#requirements) för Azure AD Connect Health. Det här anslutningstestet utförs som standard under agentregistreringen.
> 
> 

## Relaterade länkar
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!--HONumber=Oct16_HO1-->


