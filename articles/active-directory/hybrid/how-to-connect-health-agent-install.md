---
title: Installation av Azure AD Connect Health Agent | Microsoft Docs
description: Den här sidan om Azure AD Connect Health innehåller information om agentinstallationen för AD FS och Sync.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 975933a97b089cb208ecd7ff4461a893364262ff
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422373"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Installation av Azure AD Connect Health Agent

Det här dokumentet beskriver hur du installerar och konfigurerar Azure AD Connect Health-agenterna. Du kan ladda ned agenterna [här](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Krav

Följande tabell är en lista över kraven för att använda Azure AD Connect Health.

| Krav | Beskrivning |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health är en Azure AD Premium-funktion som kräver Azure AD Premium. <br /><br />Mer information finns i [Komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) <br />Information om hur du startar en kostnadsfri 30-dagars utvärderingsversion finns i [Starta en utvärderingsversion.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Du måste vara en global administratör i din Azure AD för att komma igång med Azure AD Connect Health |Som standard kan endast globala administratörer installera och konfigurera hälsoagenter för att sätta igång, få åtkomst till portalen och utföra åtgärder i Azure AD Connect Health. Mer information finns i [Administrera Azure AD-katalogen](../fundamentals/active-directory-administer.md). <br /><br /> Med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du tillåta åtkomst till Azure AD Connect Health till andra användare i din organisation. Mer information finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC) för Azure AD Connect Health.](how-to-connect-health-operations.md#manage-access-with-azure-rbac) <br /><br />**Viktigt!** Det konto som du använder när du installerar agenter måste vara ett arbets- eller skolkonto. Det kan inte vara ett Microsoft-konto. Mer information finns i [Registrera dig för Azure som en organisation](../fundamentals/sign-up-organization.md) |
| Azure AD Connect Health Agent installeras på varje målserver | Azure AD Connect Health kräver att hälsotillståndsagenterna är installerade och konfigurerade på målservrarna för att kunna ta emot data och tillhandahålla funktioner för övervakning och analys. <br /><br />För att exempelvis få data från AD FS-infrastrukturen måste agenten installeras på AD FS och proxyservrarna för webbappen. På samma sätt måste agenten installeras på domänkontrollanterna för att hämta data i din lokala AD DS-infrastruktur. <br /><br /> |
| Utgående anslutning till Azure-tjänstens slutpunkter | Under installation och körning kräver agenten anslutning till Azure AD Connect Health-tjänstens slutpunkter. Om du har blockerat utgående anslutningar med Brandväggar kontrollerar du att följande slutpunkter finns med i listan över tillåtna anslutningar. Se [Utgående anslutning för webbadresser](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) |
|Utgående anslutningar baserat på IP-adresser | Information om IP-adressbaserad filtrering för brandväggar finns i [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).|
| TLS-kontroll för utgående trafik filtreras eller inaktive ras | Det kan hända att agent registrerings steget eller data överförings åtgärderna Miss lyckas om det finns en TLS-kontroll eller upphör för utgående trafik på nätverks lagret. Läs mer om [att konfigurera TLS-kontroll](/previous-versions/tn-archive/ee796230(v=technet.10)) |
| Brandväggsportar på servern som agenten körs på |Följande brandväggsportar måste vara öppna för att agenten ska kunna kommunicera med Azure AD Health-tjänstens slutpunkter.<br /><br /><li>TCP-port 443</li><li>TCP-port 5671</li> <br />Observera att port 5671 inte längre krävs för den senaste versionen av agenten. Uppgradera till den senaste versionen så att endast port 443 krävs. Läs mer om att [aktivera brandväggsportar](/previous-versions/sql/sql-server-2008/ms345310(v=sql.100)) |
| Tillåt följande webbplatser om Förbättrad säkerhet i Internet Explorer är aktiverat |Följande webbplatser måste tillåtas om Förbättrad säkerhet i Internet Explorer är aktiverat på servern som agenten ska installeras på.<br /><br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>Federationsservern för din organisation måste vara betrodd av Azure Active Directory. Till exempel: https:\//sts.contoso.com</li> Läs mer om [hur du konfigurerar IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Om du har en proxyserver i nätverket kan du läsa Obs!|
| Kontrollera att PowerShell v4.0 eller senare har installerats | <li>Windows Server 2012 levereras med PowerShell v3.0, vilket inte är tillräckligt för agenten.</li><li>Windows Server 2012 R2 och senare levereras med en tillräckligt ny version av PowerShell.</li>|
|Inaktivera FIPS|FIPS stöds inte av Azure AD Connect Health-agenter.|

> [!IMPORTANT]
> Det finns inte stöd för att installera Azure AD Connect Health Agent på Windows Server Core.


> [!NOTE]
> Om du har en mycket låst och mycket begränsad miljö måste du lägga till de URL: er som anges i tjänst slut punkts listorna nedan förutom de som anges i den tillåtna säkerhets konfigurationen för IE utökad säkerhet ovan. 


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Utgående anslutning till Azure-tjänstens slutpunkter

 Under installation och körning kräver agenten anslutning till Azure AD Connect Health-tjänstens slutpunkter. Om utgående anslutning blockeras med hjälp av brand väggar kontrollerar du att följande URL: er inte blockeras som standard. Inaktivera inte säkerhetsövervakning eller inspektion av dessa URL: er, men tillåt dem som andra Internet trafik. De tillåter kommunikation med Azure AD Connect Health tjänst slut punkter. Lär dig hur du [kontrollerar utgående anslutningar med test-AzureADConnectHealthConnectivity](#test-connectivity-to-azure-ad-connect-health-service).

| Domänmiljö | Nödvändiga Azure-tjänsteslutpunkter |
| --- | --- |
| Offentlig | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-port: 5671 (krävs inte i den senaste versionen av agenten)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *de här slutpunkten används endast för identifieringssyften under registreringen.</li> |
| Azure Tyskland | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de *den här slutpunkten används endast i identifieringssyfte under registreringen.</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *de här slutpunkten används endast för identifieringssyften under registreringen.</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Ladda ned och installera Azure AD Connect Health-agenten

* Säkerställ att du [uppfyller kraven](how-to-connect-health-agent-install.md#requirements) för Azure AD Connect Health.
* Kom igång med Azure AD Connect Health för AD FS
    * [Hämta Azure AD Connect Health Agent för AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Se installationsinstruktionerna](#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Kom igång med Azure AD Connect Health för synkronisering
    * [Hämta och installera den senaste versionen av Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). Hälsoagenten för synkronisering installeras som en del av installationen av Azure AD Connect (version 1.0.9125.0 eller senare).
* Kom igång med Azure AD Connect Health för AD DS
    * [Ladda ned Azure AD Connect Health Agent för AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Se installationsinstruktionerna](#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Installera Azure AD Connect Health Agent för AD FS

> [!NOTE]
> AD FS-servern bör inte vara samma som din synkroniseringsserver. Installera inte AD FS-agenten på din synkroniseringsserver.
>

Kontrollera att ditt AD FS-värdnamn är unikt och att det inte finns i AD FS-tjänsten innan du installerar.
Starta agentinstallationen genom att dubbelklicka på EXE-filen som du laddade ned. Klicka på Installera på den första skärmen.

![Azure AD Connect Health AD FS installera start](./media/how-to-connect-health-agent-install/install1.png)

Klicka på Konfigurera nu när installationen är klar.

![Slutför Azure AD Connect Health AD FS installationen](./media/how-to-connect-health-agent-install/install2.png)

Detta startar ett PowerShell-fönster för att initiera agentregistreringsprocessen. När du uppmanas loggar du in med ett Azure AD-konto som har behörighet att utföra agentregistreringen. Som standard har det globala administratörskontot åtkomst.

![Azure AD Connect Health AD FS konfigurera inloggning](./media/how-to-connect-health-agent-install/install3.png)

PowerShell fortsätter efter inloggningen. När den är klar kan du stänga PowerShell så är konfigurationen klar.

I det här läget bör agenttjänsterna startas automatiskt så att agenten överför de data som krävs till molntjänsten på ett säkert sätt.

Tänk på att du ser varningar i PowerShell-fönstret om du inte uppfyller alla krav som beskrevs i föregående avsnitt. Kontrollera att du uppfyller kraven [här](how-to-connect-health-agent-install.md#requirements) innan du installerar agenten. Skärmbilden nedan är ett exempel på dessa fel.

![Azure AD Connect Health AD FS konfigurera skript](./media/how-to-connect-health-agent-install/install4.png)

Kontrollera att agenten har installerats genom att leta efter följande tjänster på servern. Dessa tjänster bör köras om du har slutfört konfigurationen. Annars startar de inte förrän konfigurationen är klar.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Azure AD Connect Health AD FS tjänster](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Aktivera granskning för AD FS

> [!NOTE]
> Detta avsnitt gäller endast för AD FS-servrar. Du behöver inte följa dessa steg på proxyservrarna för webbappen.
>

För att funktionen Användningsanalys ska kunna samla in och analysera data behöver Azure AD Connect Health-agenten informationen i AD FS-granskningsloggarna. Dessa loggar är inte aktiverade som standard. Följ stegen nedan för att aktivera AD FS-granskning och hitta AD FS-granskningsloggarna på dina AD FS-servrar.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Så här aktiverar du granskning för AD FS i Windows Server 2012 R2

1. Öppna **Lokal säkerhetsprincip** genom att öppna **Serverhanteraren** på startskärmen, eller Serverhanteraren i verktygsfältet på skrivbordet, och klicka sedan på **Verktyg/lokal säkerhetsprincip**.
2. Gå till mappen **Säkerhetsinställningar\Lokala principer\Tilldelning av användarrättigheter** och dubbelklicka sedan på **Generera säkerhetsgranskningar**.
3. På fliken **Lokal säkerhetsinställning** kontrollerar du att AD FS-tjänstkontot visas. Om det inte visas klickar du på **Lägg till användare eller grupp** , lägger till det i listan och klickar på **OK**.
4. Öppna en kommandotolk med förhöjd behörighet och kör följande kommando för att aktivera granskning: ```auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable```
5. Stäng **Lokal säkerhetsprincip**.
<br />   -- **Följande steg krävs bara för primära AD FS-servrar.** -- <br />
6. Öppna snapin-modulen **AD FS-hantering** (klicka på Verktyg i Serverhanteraren och välj sedan AD FS-hantering).
7. Klicka på **Redigera egenskaper för Federation Service** i fönstret **Åtgärder**.
8. I dialog rutan **federationstjänst egenskaper** klickar du på fliken **händelser** .
9. Markera kryssrutorna för **lyckade och misslyckade granskningar** och klicka sedan på **OK**.
10. Utförlig loggning kan aktive ras via PowerShell med kommandot: ```Set-AdfsProperties -LOGLevel Verbose``` .

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Så här aktiverar du granskning för AD FS i Windows Server 2016

1. Öppna **Lokal säkerhetsprincip** genom att öppna **Serverhanteraren** på startskärmen, eller Serverhanteraren i verktygsfältet på skrivbordet, och klicka sedan på **Verktyg/lokal säkerhetsprincip**.
2. Gå till mappen **Säkerhetsinställningar\Lokala principer\Tilldelning av användarrättigheter** och dubbelklicka sedan på **Generera säkerhetsgranskningar**.
3. På fliken **Lokal säkerhetsinställning** kontrollerar du att AD FS-tjänstkontot visas. Om det inte visas klickar du på **Lägg till användare eller grupp** och lägger till AD FS-tjänstkontot i listan, och klickar sedan på **OK**.
4. Öppna en kommandotolk med förhöjd behörighet och kör följande kommando för att aktivera granskning: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. Stäng **Lokal säkerhetsprincip**.
<br />   -- **Följande steg krävs bara för primära AD FS-servrar.** -- <br />
6. Öppna snapin-modulen **AD FS-hantering** (klicka på Verktyg i Serverhanteraren och välj sedan AD FS-hantering).
7. Klicka på **Redigera egenskaper för Federation Service** i fönstret **Åtgärder**.
8. I dialog rutan **federationstjänst egenskaper** klickar du på fliken **händelser** .
9. Markera kryssrutorna för **lyckade och misslyckade granskningar** och klicka sedan på **OK**. Detta bör vara aktiverat som standard.
10. Öppna ett PowerShell-fönstret och kör följande kommando: ```Set-AdfsProperties -AuditLevel Verbose```.

Observera att granskningsnivån ”basic” är aktiverad som standard. Läs mer om [AD FS-granskningsförbättringen i Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Så här hittar du AD FS-granskningsloggar

1. Öppna **Loggboken**.
2. Gå till Windows-loggar och välj **Säkerhet**.
3. Klicka på **Filtrera aktuella loggar** till höger.
4. Välj **AD FS-granskning** under Händelsekälla.

    Och [vanliga frågor och svar](reference-connect-health-faq.md#operations-questions) för spårningsloggar.

![AD FS-granskningsloggar](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> En grupprincip kan inaktivera AD FS-granskning. Om AD FS-granskning har inaktiverats är användningsanalysdata om inloggningsaktiviteter inte tillgängliga. Kontrollera att du inte har någon grupprincip som kan inaktivera AD FS-granskning.
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Installera Azure AD Connect Health-agenten för synkronisering

Azure AD Connect Health-agenten för synkronisering installeras automatiskt i den senaste versionen av Azure AD Connect. Om du vill använda Azure AD Connect för synkronisering måste du ladda ned den senaste versionen av Azure AD Connect och installera den. Du kan hämta den senaste versionen [här](https://www.microsoft.com/download/details.aspx?id=47594).

Kontrollera att agenten har installerats genom att leta efter följande tjänster på servern. Dessa tjänster bör köras om du har slutfört konfigurationen. Annars startar de inte förrän konfigurationen är klar.

* Azure AD Connect Health Sync Insights Service
* Azure AD Connect Health Sync Monitoring Service

![Verifiera Azure AD Connect Health för synkronisering](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Kom ihåg att användningen av Azure AD Connect Health kräver Azure AD Premium. Om du inte har Azure AD Premium kan du inte slutföra konfigurationen på Azure-portalen. Mer information finns på [kravsidan](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Manuell registrering av Azure AD Connect Health för synkronisering

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

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Installera Azure AD Connect Health Agent för AD FS

Starta agentinstallationen genom att dubbelklicka på EXE-filen som du laddade ned. Klicka på Installera på den första skärmen.

![Starta Azure AD Connect Health Agent för AD DS-installation](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Klicka på Konfigurera nu när installationen är klar.

![Slutför Azure AD Connect Health Agent för installation av AD DS](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

En kommandotolk öppnas följt av PowerShell-kod som kör Register-AzureADConnectHealthADFSAgent. När du får ett meddelande att du ska logga in på Azure ska du logga in.

![Azure AD Connect Health Agent för AD DS konfigurera inloggning](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

PowerShell fortsätter efter inloggningen. När den är klar kan du stänga PowerShell så är konfigurationen klar.

Nu bör tjänsterna starta automatiskt och agenten övervakar och samlar in data. Tänk på att du ser varningar i PowerShell-fönstret om du inte uppfyller alla krav som beskrevs i föregående avsnitt. Kontrollera att du uppfyller kraven [här](how-to-connect-health-agent-install.md#requirements) innan du installerar agenten. Skärmbilden nedan är ett exempel på dessa fel.

![Azure AD Connect Health Agent för AD DS-konfiguration av skript](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Kontrollera att agenten har installerats genom att leta efter följande tjänster på domänkontrollanten.

* Azure AD Connect Health AD DS Insights Service
* Azure AD Connect Health AD DS Monitoring Service

Dessa tjänster bör köras om du har slutfört konfigurationen. Annars startar de inte förrän konfigurationen är klar.

![Azure AD Connect Health Agent för AD DS-tjänster](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quick-agent-installation-in-multiple-servers"></a>Snabb agent installation på flera servrar

1. Skapa ett användar konto i Azure AD med ett lösen ord.
2. Tilldela **ägar** rollen för det här lokala AAD-kontot i Azure AD Connect Health via portalen. Följ stegen [här](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Tilldela rollen till alla tjänst instanser. 
3. Hämta MSI-filen för. exe i den lokala domänkontrollanten för installation.
4. Kör följande skript för att registrera dig. Ersätt parametrarna med det nya användar kontot som skapats och lösen ordet. 

```powershell
AdHealthAddsAgentSetup.exe /quiet
Start-Sleep 30
$userName = "NEWUSER@DOMAIN"
$secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
 
Register-AzureADConnectHealthADDSAgent -Credential $myCreds

```

1. När du är färdig kan du ta bort åtkomsten för det lokala kontot genom att göra något av följande: 
    * Ta bort roll tilldelningen för det lokala kontot för AAD Connect Health
    * Rotera lösen ordet för det lokala kontot. 
    * Inaktivera lokalt AAD-konto
    * Ta bort det lokala AAD-kontot  

## <a name="agent-registration-using-powershell"></a>Agentregistrering med PowerShell

När du har installerat lämplig agent-setup.exe, kan du utföra steget agentregistrering med hjälp av följande PowerShell-kommandon, beroende på rollen. Öppna ett PowerShell-fönster och kör lämpligt kommando:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

De här kommandona accepterar ”Autentiseringsuppgift” som en parameter för att slutföra registreringen på ett icke-interaktivt sätt eller på en Server-Core-dator.
* Autentiseringsuppgiften kan registreras i en PowerShell-variabel som skickas som en parameter.
* Du kan ange en Azure AD-identitet som har åtkomst för att registrera agenterna och INTE har aktiverat MFA.
* Som standard har globala administratörer behörighet att utföra registrering av agenten. Du kan även låta andra mindre privilegierade identiteter utföra det här steget. Läs mer om [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Konfigurera Azure AD Connect Health-agenter att använda HTTP-proxy

Du kan konfigurera Azure AD Connect Health-agenter att fungera med en HTTP-proxy.

> [!NOTE]
> * ”Netsh WinHttp set ProxyServerAddress” fungerar inte eftersom agenten använder System.Net för att skicka webbförfrågningar i stället för Microsoft Windows HTTP Services.
> * Den konfigurerade HTTP-proxyadressen används för att skicka krypterade https-meddelanden.
> * Autentiserade proxyservrar (med HTTPBasic) stöds inte.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Ändra hälsoagentens proxykonfiguration

Du kan välja följande alternativ när du konfigurerar Azure AD Connect Health Agent att använda en HTTP-proxy.

> [!NOTE]
> Du måste starta om alla Azure AD Connect Health Agent-tjänster för att proxyinställningarna ska uppdateras. Kör följande kommando:<br />
> Restart-Service AzureADConnectHealth *
>
>

#### <a name="import-existing-proxy-settings"></a>Importera befintlig proxyinställningar

##### <a name="import-from-internet-explorer"></a>Importera från Internet Explorer

Proxyinställningarna i Internet Explorer HTTP kan importeras för att användas av Azure AD Connect Health-agenter. Kör följande PowerShell-kommando på var och en av de servrar som kör Health-agenten:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

##### <a name="import-from-winhttp"></a>Importera från WinHTTP

Proxyinställningarna i WinHTTP kan importeras för att användas av Azure AD Connect Health-agenter. Kör följande PowerShell-kommando på var och en av de servrar som kör Health-agenten:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Ange proxyadresser manuellt

Du kan ange en proxyserver manuellt genom att köra följande PowerShell-kommando på alla servrar som kör hälsoagenten:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Exempel: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443*

* ”address” kan vara ett DNS-omvandlingsbart servernamn eller en IPv4-adress
* ”port” kan utelämnas. Om det utelämnas används 443 som standardport.

#### <a name="clear-existing-proxy-configuration"></a>Rensa den befintliga proxykonfigurationen

Du kan rensa den befintliga proxykonfigurationen genom att köra följande kommando:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Läsa de aktuella proxyinställningarna

Du kan använda följande kommando för att läsa de konfigurerade proxyinställningarna:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testa anslutningen till Azure AD Connect Health-tjänsten

Problem kan uppstå som kan gör att Azure AD Connect Health-agentens anslutning till Azure AD Connect Health-tjänsten bryts. Exempel på sådana problem är nätverksproblem, behörighetsproblem osv.

Om agenten inte kan skicka data till Azure AD Connect Health-tjänsten under mer än två timmar visas en varning om att ”hälsotjänstens data inte är uppdaterade”. Du kan bekräfta om den berörda Azure AD Connect Health-agenten kan överföra data till Azure AD Connect Health-tjänsten genom att köra följande PowerShell-kommando:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Rollparametern har för närvarande följande värden:

* ADFS
* Synkronisera
* ADDS

> [!NOTE]
> Du måste slutföra agentregistreringen innan du kan använda anslutningsverktyget. Om det inte går att slutföra agentregistreringen kontrollerar du att du uppfyller alla [krav](how-to-connect-health-agent-install.md#requirements) för Azure AD Connect Health. Det här anslutningstestet utförs som standard under agentregistreringen.
>
>

## <a name="related-links"></a>Relaterade länkar

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health-åtgärder](how-to-connect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
* [Azure AD Connect Health versions historik](reference-connect-health-version-history.md)
