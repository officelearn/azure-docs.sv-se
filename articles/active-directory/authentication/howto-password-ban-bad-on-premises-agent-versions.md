---
title: Versions historik för lösen ords skydds agent – Azure Active Directory
description: Dokument versions version och beteende ändrings historik
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd9b07f1f7aed479e94e77a5641130cb784dd69e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741974"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Versions historik för Azure AD Password Protection-Agent

## <a name="121250"></a>1.2.125.0

Utgivnings datum: 3/22/2019

* Åtgärda mindre skrivfel i händelse logg meddelanden
* Uppdatera EULA-avtalet till slutlig allmän tillgänglighets version

> [!NOTE]
> Build 1.2.125.0 är den allmänna tillgänglighets versionen. Tack för att alla har fått feedback om produkten!

## <a name="121160"></a>1.2.116.0

Utgivnings datum: 3/13/2019

* Nu rapporterar Get-AzureADPasswordProtectionProxy-och Get-AzureADPasswordProtectionDCAgent-cmdletarna program varu versionen och den aktuella Azure-klienten med följande begränsningar:
  * Program varu versionen och Azures klient data är bara tillgängliga för DC-agenter och proxyservrar som kör version 1.2.116.0 eller senare.
  * Azure-klientens data kan inte rapporteras förrän en omregistrering (eller förnyad) av proxyn eller skogen har inträffat.
* Proxy-tjänsten kräver nu att .NET 4,7 har installerats.
  * .NET 4,7 bör redan vara installerat på en helt uppdaterad Windows Server. Om detta inte är fallet kan du hämta och köra installations programmet som finns i [.NET Framework 4,7 Offline Installer för Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * På Server Core-system kan det vara nödvändigt att skicka flaggan/q till .NET 4,7-installations programmet för att det ska gå att slutföra.
* Proxy-tjänsten stöder nu automatisk uppgradering. Vid automatisk uppgradering används tjänsten Microsoft Azure AD Connect agent Updateer som installeras sida vid sida med proxy-tjänsten. Automatisk uppgradering är aktiverat som standard.
* Automatisk uppgradering kan aktive ras eller inaktive ras med hjälp av Set-AzureADPasswordProtectionProxyConfiguration cmdlet. Den aktuella inställningen kan frågas med hjälp av Get-AzureADPasswordProtectionProxyConfiguration cmdlet.
* Tjänstens binärfil för DC-agenttjänsten har döpts om till AzureADPasswordProtectionDCAgent.exe.
* Tjänstens binärfil för proxy-tjänsten har döpts om till AzureADPasswordProtectionProxy.exe. Brand Väggs regler kan behöva ändras i enlighet med detta om en brand vägg från tredje part används.
  * OBS! om en http-proxy-konfigurationsfil användes i en tidigare proxy-installation måste den byta namn (från *proxyservice.exe.config* till *AzureADPasswordProtectionProxy.exe.config*) efter uppgraderingen.
* Alla tids kontroller för tidsbegränsade funktioner har tagits bort från DC-agenten.
* Mindre fel korrigeringar och loggnings förbättringar.

## <a name="12650"></a>1.2.65.0

Utgivnings datum: 2/1/2019

Något

* DC-agenten och proxy service stöds nu på Server Core. Mininimum OS-krav är oförändrade från före: Windows Server 2012 för DC-agenter och Windows Server 2012 R2 för proxyservrar.
* Cmdletarna Register-AzureADPasswordProtectionProxy och Register-AzureADPasswordProtectionForest har nu stöd för enhets kodsbaserade Azure-autentiseringsläge.
* Get-AzureADPasswordProtectionDCAgent cmdlet ignorerar Mangled och/eller ogiltiga tjänst anslutnings punkter. Detta åtgärdar felet där domänkontrollanter ibland skulle visas flera gånger i utdata.
* Get-AzureADPasswordProtectionSummaryReport cmdlet ignorerar Mangled och/eller ogiltiga tjänst anslutnings punkter. Detta åtgärdar felet där domänkontrollanter ibland skulle visas flera gånger i utdata.
* Proxy PowerShell-modulen är nu registrerad från%ProgramFiles%\WindowsPowerShell\Modules. Datorns miljö variabel för PSModulePath ändras inte längre.
* En ny Get-AzureADPasswordProtectionProxy-cmdlet har lagts till för att hjälpa till att identifiera registrerade proxyservrar i en skog eller domän.
* DC-agenten använder en ny mapp i Sysvol-resursen för replikering av lösen ords principer och andra filer.

   Plats för gammal mapp:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ny mapplats:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Den här ändringen gjordes för att undvika falska, överblivna GPO-varningar.)

   > [!NOTE]
   > Ingen migrering eller delning av data görs mellan den gamla mappen och den nya mappen. Äldre versioner av DC-agenten fortsätter att använda den gamla platsen tills den har uppgraderats till den här versionen eller senare. När alla DC-agenter kör version 1.2.65.0 eller senare kan den gamla SYSVOL-mappen tas bort manuellt.

* DC-agenten och proxy-tjänsten kommer nu att identifiera och ta bort Mangled kopior av deras respektive tjänst anslutnings punkter.
* Varje DC-agent tar regelbundet bort Mangled och föråldrade tjänst anslutnings punkter i sin domän, för både DC-agent och proxy service-anslutnings punkter. Både anslutnings punkter för DC-agent och proxy-tjänst anses vara inaktuella om Pulsslagets tidstämpel är äldre än sju dagar.
* DC-agenten kommer nu att förnya skogs certifikatet efter behov.
* Proxyservern kommer nu att förnya proxy-certifikatet vid behov.
* Uppdateringar av algoritmen för lösen ords verifiering: den globala listan över förbjudna lösen ord och kundspecifika förbjudna lösen ord (om konfigurerad) kombineras före lösen ords valideringar. Ett angivet lösen ord kan nu avvisas (Miss lyckas eller granskas endast) om det innehåller tokens från både den globala och den kundspecifika listan. Dokumentationen för händelse loggen har uppdaterats för att avspegla detta. Se [övervaka lösen ords skydd i Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Prestanda-och robusta korrigeringar
* Förbättrad loggning

> [!WARNING]
> Tidsbegränsad funktionalitet: DC-agenttjänsten i den här versionen (1.2.65.0) slutar att bearbeta begär Anden om lösen ords verifiering från den 1 september 2019.  DC agent-tjänster i tidigare versioner (se listan nedan) slutar att bearbeta den 1 juli 2019. DC-agenttjänsten i alla versioner loggar 10021-händelser till administratörs händelse loggen under de två månaderna, vilket leder till att dessa deadlines. Alla begränsningar för tids gräns tas bort i den kommande GA-versionen. Proxy agent-tjänsten är inte tidsbegränsad i någon version, men bör fortfarande uppgraderas till den senaste versionen för att kunna dra nytta av alla efterföljande fel korrigeringar och andra förbättringar.

## <a name="12250"></a>1.2.25.0

Utgivnings datum: 11/01/2018

Fixe

* DC-agenten och proxy-tjänsten bör inte längre fungera på grund av misslyckade certifikat förtroenden.
* DC-agenten och proxy-tjänsten har ytterligare korrigeringar för FIPS-kompatibla datorer.
* Proxy service fungerar nu som den ska i en nätverks miljö med endast TLS 1,2.
* Mindre prestanda och robusta korrigeringar
* Förbättrad loggning

Något

* Minimi kraven på operativ system nivå för proxy-tjänsten är nu Windows Server 2012 R2. Minimi kraven på operativ system nivå för DC-agenttjänsten finns kvar på Windows Server 2012.
* Proxy-tjänsten kräver nu .NET version 4.6.2.
* Algoritmen för lösen ords verifiering använder en utökad Character normaliserings-tabell. Detta kan resultera i att lösen ord nekas som har godkänts i tidigare versioner.

## <a name="12100"></a>1.2.10.0

Utgivnings datum: 8/17/2018

Fixe

* Register-AzureADPasswordProtectionProxy och Register-AzureADPasswordProtectionForest stöder nu Multi-Factor Authentication
* Register-AzureADPasswordProtectionProxy kräver en WS2012 eller senare domänkontrollant i domänen för att undvika krypterings fel.
* DC-agenttjänsten är mer tillförlitlig om du begär en ny lösen ords princip från Azure vid start.
* DC-agenttjänsten begär en ny lösen ords princip från Azure varje timme om det behövs, men kommer nu att göra det på en slumpmässigt vald start tid.
* DC-agenttjänsten kommer inte längre att orsaka en obegränsad fördröjning i New DC-annonsering när den installeras på en server innan dess befordran som en replik.
* DC-agenttjänsten kommer nu att respektera konfigurations inställningen "Aktivera lösen ords skydd på Windows Server Active Directory"
* Både DC-agenten och proxy installations program kommer nu att ha stöd för uppgradering på plats när du uppgraderar till framtida versioner.

> [!WARNING]
> Uppgradering på plats från version 1.1.10.3 stöds inte och leder till ett installations fel. Om du vill uppgradera till version 1.2.10 eller senare måste du först avinstallera DC-agenten och proxy service-programvaran och sedan installera den nya versionen från grunden. Omregistrering av proxy-tjänsten för lösen ords skydd i Azure AD krävs.  Du behöver inte registrera skogen igen.

> [!NOTE]
> Uppgraderingar på plats av DC Agent-programvaran kräver en omstart.

* DC agent och proxy service stöder nu körning på en server som kon figurer ATS för att endast använda FIPS-kompatibla algoritmer.
* Mindre prestanda och robusta korrigeringar
* Förbättrad loggning

## <a name="11103"></a>1.1.10.3

Utgivnings datum: 6/15/2018

Första offentliga för hands version

## <a name="next-steps"></a>Nästa steg

[Distribuera lösen ords skydd i Azure AD](howto-password-ban-bad-on-premises-deploy.md)
