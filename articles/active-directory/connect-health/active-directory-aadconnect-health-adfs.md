---
title: Använda Azure AD Connect Health med AD FS | Microsoft Docs
description: Den här sidan handlar om Azure AD Connect Health och hur du övervakar den lokala AD FS-infrastrukturen.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2018
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 317b7103dd3c5e6aada4f8d10e57204fcb6d968d
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887733"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Övervaka AD FS med Azure AD Connect Health
Följande dokumentation gäller specifikt för övervakningen av AD FS-infrastrukturen med Azure AD Connect Health. Mer information om övervakning av Azure AD Connect (Sync) med Azure AD Connect Health finns i [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md). Mer information om övervakning av Active Directory Domain Services med Azure AD Connect Health finns i [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>AD FS-aviseringar
Avsnittet om Azure AD Connect Health-aviseringar innehåller en lista över aktiva aviseringar. För varje avisering finns relevant information, lösningssteg och länkar till relaterad dokumentation.

Om du dubbelklickar en aktiv eller åtgärdad avisering visas ett nytt blad med ytterligare information, samt de steg som du kan utföra för att åtgärda aviseringen liksom länkar till relevant dokumentation. Du kan också visa historiska data för tidigare åtgärdade aviseringar.

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Användningsanalys för AD FS
Azure AD Connect Health-användningsanalys analyserar autentiseringstrafiken på dina federationsservrar. Om du dubbelklickar på kryssrutan för användningsanalys öppnas bladet för användningsanalys, som visar flera mått och grupperingar.

> [!NOTE]
> Om du ska kunna använda användningsanalys med AD FS måste AD FS-granskning vara aktiverat. Mer information finns i [Aktivera granskning för AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health/report1.png)

Om du vill välja fler mått, ange ett tidsintervall eller ändra grupperingen högerklickar du på diagrammet med användningsanalysen och väljer Redigera diagram. Sedan kan du ange tidsintervallet, välja ett annat mått och ändra grupperingen. Du kan visa autentiseringstrafikens fördelning baserat på olika ”mått” och gruppera varje mått med relevanta ”gruppera efter”-parametrar som beskrivs i följande avsnitt:

**Mått: Totalt antal förfrågningar**: Totalt antal förfrågningar som bearbetas av AD FS-servrarna.

|Gruppera efter | Vad grupperingen betyder och varför den är användbar? |
| --- | --- |
| Alla | Visar det totala antalet begäranden som bearbetas av alla AD FS-servrar.|
| Program | Grupperar antalet förfrågningar baserat på den förlitande parten i fråga. Den här grupperingen är användbar för att förstå vilket program som tar emot en viss procentandel av den totala trafiken. |
|  Server |Grupperar antalet förfrågningar baserat på servern som bearbetade förfrågan. Den här grupperingen är användbar för att förstå belastningsfördelningen av den totala trafiken.
| Anslut till arbetsplats |Grupperar antalet förfrågningar baserat på om förfrågningarna kommer från arbetsplatsanslutna enheter (kända). Den här grupperingen är användbar för att förstå om dina resurser används med enheter som är okända för identitetsinfrastrukturen. |
|  Autentiseringsmetod | Grupperar förfrågningarna baserat på den autentiseringsmetod som används för autentisering. Den här grupperingen är användbar för att förstå den gemensamma autentiseringsmetoden som används för autentisering. Följande är möjliga autentiseringsmetoder <ol> <li>Windows-integrerad autentisering (Windows)</li> <li>Formulärbaserad autentisering (formulär)</li> <li>Enkel inloggning (SSO, Single Sign On)</li> <li>X509-certifikatautentisering (certifikat)</li> <br>Observera att en begäran räknas som en enkel inloggning (SSO) om federationsservrarna tar emot förfrågan med en SSO-cookie. I så fall, om cookien är giltig, uppmanas inte användaren att ange autentiseringsuppgifter och får sömlös åtkomst till programmet. Detta är vanligt om du har flera förlitande parter som skyddas av federationsservrar. |
| Nätverksplats | Grupperar förfrågningarna baserat på användarens nätverksplats. Platsen kan vara antingen intranät eller extranät. Den här grupperingen är användbar för att vet vilken procentandel av trafiken som kommer från intranätet och extranätet. |


**Mått: Totalt antal misslyckande begäranden**: Totalt antal misslyckade begäranden som bearbetas av federationstjänsten. (Det här måttet är bara tillgängligt på AD FS för Windows Server 2012 R2)

|Gruppera efter | Vad grupperingen betyder och varför den är användbar? |
| --- | --- |
| Feltyp | Visar antalet fel baserat på fördefinierade feltyper. Den här grupperingen är användbar för att förstå de vanliga typerna av fel. <ul><li>Felaktigt användarnamn eller lösenord: Fel som beror på ett felaktigt användarnamn eller lösenord.</li> <li>”Utelåsning från extranät”: Fel som beror på att förfrågningarna togs emot från en användare som var utelåst från extranätet </li><li> ”Lösenordet har upphört att gälla”: Fel som beror på att användaren loggar in med ett lösenord som upphört att gälla.</li><li>”Inaktiverat konto”: Fel som beror på att användaren loggar in med ett inaktiverat konto.</li><li>”Enhetsautentisering”: Fel som beror på att användaren inte kan autentiseras med enhetsautentisering.</li><li>”Autentisering med användarcertifikat”: Fel som beror på att användaren inte kan autentiseras på grund av ett ogiltigt certifikat.</li><li>”MFA”: Fel som beror på att användaren inte kan autentiseras med hjälp av Multi-Factor Authentication.</li><li>”Andra autentiseringsuppgifter”: ”Utfärdandeauktorisering”: Fel som beror på auktoriseringsproblem.</li><li>”Utfärdandedelegering”: Fel som beror på utfärdandedelegeringsproblem.</li><li>”Tokengodkännande”: Fel som beror på att ADFS avvisar token från en tredjeparts identitetsprovider.</li><li>”Protokoll”: Fel som beror på protokollproblem.</li><li>”Okänt”: Samla in allt. Andra fel som inte passar i de angivna kategorierna.</li> |
| Server | Grupperar felen baserat på servern. Denna gruppering är användbar för att förstå felfördelningen mellan servrar. En ojämn fördelning kan vara ett tecken på att en server har felaktigt tillstånd. |
| Nätverksplats | Grupperar felen baserat på förfrågningarnas nätverksplats (intranät eller extranät). Denna gruppering är användbar för att förstå vilken typ av förfrågningar som misslyckas. |
|  Program | Grupperar felen baserat på målprogrammet (förlitande part). Denna gruppering är användbar för att förstå vilket program som har flest antal fel. |

**Mått: Antal användare** – Det genomsnittliga antalet unika användare som aktivt autentiserar med AD FS

|Gruppera efter | Vad grupperingen betyder och varför den är användbar? |
| --- | --- |
|Alla |Detta mått visar genomsnittligt antal användare som använder federationstjänsten under det valda tidsintervallet. Användarna grupperas inte. <br>Det genomsnittliga antalet beror på det valda tidsintervallet. |
| Program |Grupperar det genomsnittliga antalet användare baserat på målprogrammet (förlitande part). Denna gruppering är användbar för att förstå hur många användare som använder olika program. |

## <a name="performance-monitoring-for-ad-fs"></a>Prestandaövervakning för AD FS
Azure AD Connect Health-prestandaövervakning tillhandahåller övervakningsinformation för mått. Om du markerar rutan Övervakning öppnas ett blad med detaljerad information om måtten.

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health/perf1.png)

Om du väljer filteralternativet längst upp i bladet kan du filtrera efter server och visa måtten för enskilda servrar. Om du vill ändra måttet högerklickar du i övervakningsdiagrammet under övervakningsbladet och väljer Redigera diagram (eller klickar på Redigera diagram). Från det nya bladet som öppnas kan du sedan välja ytterligare mått i listrutan och ange ett tidsintervall som du vill visa prestandadata för.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>De 50 användarna med flest misslyckade inloggningar med användarnamn/lösenord
Ett av de vanligaste skälen till en misslyckad autentiseringsbegäran på en AD FS-server är en begäran med ogiltiga autentiseringsuppgifter, dvs. ett felaktigt användarnamn eller lösenord. Påverkar vanligtvis användare på grund av komplexa lösenord, glömt lösenord eller skrivfel.

Men det finns andra skäl till ett oväntat antal begäranden som hanteras av AD FS-servrar, exempelvis: ett program som lagrar användarens autentiseringsuppgifter i cacheminnet och autentiseringsuppgifterna upphör att gälla eller en obehörig användare som försöker logga in på ett konto med ett antal välkända lösenord. Dessa två exempel är giltiga skäl som kan leda till en ökning i begäranden.

Azure AD Connect Health för AD FS tillhandahåller en rapport över de 50 användarna med flest misslyckade inloggningsförsök på grund av ett ogiltigt användarnamn eller lösenord. Rapporten skapas genom att bearbeta granskningshändelserna som genereras av alla AD FS-servrar i servergrupperna.

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health-adfs/report1a.png)

I den här rapporten kan du enkelt få tillgång till följande typer av information:

* Totalt antal misslyckade begäranden med fel användarnamn/lösenord de senaste 30 dagarna
* Genomsnittligt antal användare som misslyckades med inloggningen på grund av ett felaktigt användarnamn/lösenord varje dag.

Om du klickar på den här delen kommer du till huvudbladet för rapporter som innehåller ytterligare information. Det här bladet innehåller ett diagram med trendinformation för att skapa en baslinje om begäranden med felaktigt användarnamn eller lösenord. Dessutom skapar den en lista med över upp 50 användare med antalet misslyckade försök under den gångna veckan. Observera att de 50 översta användarna från den senaste veckan kan hjälpa dig att identifiera ökningar i antalet felaktiga lösenord.  

Diagrammet innehåller följande information:

* Totalt antal misslyckade inloggningar på grund av ett felaktigt användarnamn/lösenord per dag.
* Totalt antal unika användare med misslyckade inloggningar per dag.
* Klientens IP-adress för senaste begäran

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health-adfs/report3a.png)

Rapporten innehåller följande information:

| Rapportobjekt | Beskrivning |
| --- | --- |
| Användar-ID |Visar användar-ID:t som användes. Det här värdet är vad användaren har angett, vilket i vissa fall är fel användar-ID. |
| Misslyckade försök |Visar totalt antal misslyckade försök för det specifika användar-ID:t. Tabellen sorteras med flest antal misslyckade försök i fallande ordning. |
| Senaste fel |Visar tidsstämpeln då det senaste felet uppstod. |
| IP-adress för senaste fel |Visar klient-IP-adress från senaste felaktiga begäran. Om du ser flera IP-adresser i det här värdet kan det omfatta klientens IP-adress för vidarebefordran tillsammans med IP-adressen för användarens senaste begärandeförsök.  |

> [!NOTE]
> Den här rapporten uppdateras automatiskt var tolfte timme med den nya informationen som samlats in inom den tidsperioden. Därför kanske inloggningsförsök under de senaste 12 timmarna inte finns med i rapporten.
>
>

## <a name="risky-ip-report-public-preview"></a>Rapport för riskfyllda IP-adresser (förhandsversion)
AD FS-kunder kan göra slutpunkter tillgängliga för lösenordsautentisering på Internet för att tillhandahålla autentiseringstjänster till slutanvändare som ska få åtkomst till SaaS-program, till exempel Office 365. I det här fallet är det möjligt för någon obehörig att försöka logga in på ditt AD FS-system genom att gissa slutanvändarens lösenord och få åtkomst till programresurser. AD FS har innehållit en utelåsningsfunktion för extranätskonton som förhindrar dessa typer av angrepp sedan AD FS i Windows Server 2012 R2. Om du har en lägre version rekommenderar vi starkt att du uppgraderar ditt AD FS-system till Windows Server 2016. <br />
Dessutom är det möjligt att en enskild IP-adress kan försöka utföra flera inloggningar mot flera användare. I dessa fall kan antalet försök per användare ligga under tröskelvärdet för kontots utelåsningsskydd i AD FS. Azure AD Connect Health innehåller nu en ”rapport för riskfyllda IP-adresser” som identifierar det här tillståndet och meddelar administratörer om detta inträffar. Följande är viktiga fördelar med den här rapporten: 
- Identifiering av IP-adresser som överskrider ett tröskelvärde för misslyckade lösenordsbaserade inloggningar
- Stöd för misslyckade inloggningar på grund av felaktiga lösenord eller på grund av extranätsutelåsning
- E-postavisering som varnar administratörer när detta inträffar med anpassningsbara e-postinställningar
- Anpassningsbara tröskelvärdesinställningar som överensstämmer med säkerhetsprinciper i organisationen
- Nedladdningsbara rapporter för offlineanalys och integrering med andra system via automatisering

> [!NOTE]
> Om du ska kunna använda rapporten måste AD FS-granskning vara aktiverat. Mer information finns i [Aktivera granskning för AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Om du vill ha åtkomst till förhandsversionen måste du ha behörighet som global administratör eller [säkerhetsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader).  
> 

### <a name="what-is-in-the-report"></a>Vad finns i rapporten
Varje objekt i rapporten för riskfyllda IP-adresser visar sammanställd information om misslyckade AD FS-inloggningsaktiviteter där angivet tröskelvärde har överskridits. Den innehåller följande information: ![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health-adfs/report4a.png)

| Rapportobjekt | Beskrivning |
| ------- | ----------- |
| Tidsstämpel | Visar tidsstämpeln baserat på Azure-portalens lokala tid när tidsperioden för identifiering startar.<br /> Alla dagliga händelser genereras vid midnatt UTC-tid. <br />Varje timhändelse har en tidsstämpel som är avrundad till timmens början. Första aktivitetens starttid från ”firstAuditTimestamp” hittar du i den exporterade filen. |
| Utlösartyp | Visar tidsperiod för identifieringstypen. De sammanställda utlösartyperna visas per timme eller per dag. Det här är användbart vid identifiering av en råstyrkeattack med hög frekvens, jämfört med en långsam attack där antalet försök är fördelade över hela dagen. |
| IP-adress | En enskild riskfylld IP-adress som antingen har ett felaktigt lösenord eller en extranätsutelåsning vid inloggning. Detta kan vara en IPv4- eller IPv6-adress. |
| Antal felaktiga lösenord | Antalet felaktiga lösenord från IP-adressen under tidsperioden för identifiering. Felaktiga lösenord kan inträffa flera gånger för vissa användare. Observera att detta inte inkluderar misslyckade försök på grund av utgångna lösenord. |
| Antal extranätsutelåsningar | Antalet extranätsutelåsningar från IP-adressen under tidsperioden för identifiering. Extranätsutelåsningar kan inträffa flera gånger för vissa användare. Detta visas bara om extranätsutelåsningen har konfigurerats i AD FS (version 2012 R2 eller senare). <b>Obs!</b> Vi rekommenderar starkt att aktivera den här funktionen om du tillåter extranätsinloggningar med lösenord. |
| Försök för unika användare | Antalet försök för unika användarkonton från IP-adressen under tidsperioden för identifiering. Detta är en mekanism för att särskilja ett mönster vid en enda användarattack jämfört med attackmönster mot flera användare.  |

Nedanstående rapportobjekt innebär från kl. 18:00 till 19:00 den 28/2 2018, innehöll IP-adressen <i>104.2XX.2XX.9</i> inte några felaktiga lösenord, men 284 extranätsutelåsningar. 14 unika användare påverkades inom kriterierna. Händelsen överskred tröskelvärdet per timme i rapporten. 

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health-adfs/report4b.png)

> [!NOTE]
> - Det är bara aktiviteter som överskrider angivet tröskelvärde visas i rapportlistan. 
> - Den här rapporten kan spåras bakåt i högst 30 dagar.
> - Aviseringsrapporten visar inte Exchange IP-adresser eller privata IP-adresser. De ingår dock fortfarande i exportlistan. 
>

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health-adfs/report4c.png)

### <a name="load-balancer-ip-addresses-in-the-list"></a>IP-adresser för belastningsutjämnare i listan
Sammanställning av belastningsutjämnarens misslyckade inloggningar aktiviteter och uppnått tröskelvärde. Om du ser IP-adresser för belastningsutjämnare är det mycket troligt att en extern belastningsutjämnare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn. Konfigurera belastningsutjämnaren korrekt för att vidarebefordra klientens IP-adress. 

### <a name="download-risky-ip-report"></a>Ladda ner rapport för riskfyllda IP-adresser 
Med hjälp av funktionen **Ladda ned** kan listan med riskfyllda IP-adresser under de senaste 30 dagarna exporteras från Connect Health-portalen. Exportresultatet inkluderar alla misslyckade AD FS-inloggningar i varje tidsfönster, så du kan anpassa filtreringen efter exporten. Förutom markerade sammanställningar i portalen visar exportresultatet också mer information om misslyckade inloggningar per IP-adress:

|  Rapportobjekt  |  Beskrivning  | 
| ------- | ----------- | 
| firstAuditTimestamp | Visar den första tidstämpel då misslyckade aktiviteter startades under tidsperioden för identifieringen.  | 
| lastAuditTimestamp | Visar den sista tidstämpel då misslyckade aktiviteter avslutades under tidsperioden för identifieringen.  | 
| attemptCountThresholdIsExceeded | Flagga ifall de aktuella aktiviteterna överstiger tröskelvärdet för aviseringar.  | 
| isWhitelistedIpAddress | Flagga ifall IP-adressen är filtrerad från avisering och rapportering. Privata IP-adresser (<i>10.x.x.x, 172.x.x.x och 192.168.x.x</i>) och Exchange-IP-adresser är filtrerade och markeras som True. Om du ser privata IP-adressintervall är det mycket troligt att en extern belastningsutjämnare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn.  | 

### <a name="configure-notification-settings"></a>Konfigurera inställningar för meddelanden
Administratörskontakter i rapporten kan uppdateras i **Meddelandeinställningar**. Som standard är aviseringar om riskfyllda IP-adresser inaktiverade. Du kan aktivera meddelanden genom att visa/dölja knappen under ”Get email notifications for IP addresses exceeding failed activity threshold report” (Hämta e-postmeddelanden för IP-adresser som överstiger tröskelvärdet för misslyckade aktiviteter). Precis som i inställningar av allmänna aviseringar i Connect Health, kan du anpassa vilka mottagare som ska få meddelanden om riskfyllda IP-adresser härifrån. Du kan även meddela alla globala administratörer när ändringen är gjord. 

### <a name="configure-threshold-settings"></a>Konfigurera inställningar av tröskelvärden
Aviseringströskelvärdet kan uppdateras i Tröskelinställningar. Systemet har tröskelvärdet inställt som standard. Det finns fyra kategorier i tröskelinställningarna för rapporten om riskfyllda IP-adresser:

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health-adfs/report4d.png)

| Tröskelobjekt | Beskrivning |
| --- | --- |
| (Felaktig U/P + extranätsutelåsning)/dag  | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet felaktiga lösenord plus antalet extranätsutelåsningar överskrider den per **dag**. |
| (Felaktig U/P + extranätsutelåsning)/timme | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet felaktiga lösenord plus antalet extranätsutelåsningar överskrider den per **timme**. |
| Extranätsutelåsning/dag | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet extranätsutelåsningar överskrider den per **dag**. |
| Extranätsutelåsning/timme| Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet extranätsutelåsningar överskrider den per **timme**. |

> [!NOTE]
> - Ändringar av rapportens tröskelvärde tillämpas en timme efter ändringen. 
> - Befintliga rapporterade objekt påverkas inte av tröskeländringen. 
> - Vi rekommenderar att du analyserar antalet händelser i din miljö och justerar tröskelvärdet i enlighet med detta. 
>
>

### <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
1. Varför ser jag privata IP-adressintervall i rapporten?  <br />
Privata IP-adresser (<i>10.x.x.x, 172.x.x.x och 192.168.x.x</i>) och Exchange-IP-adresser är filtrerade och markeras som True i IP-vitlistan. Om du ser privata IP-adressintervall är det mycket troligt att en extern belastningsutjämnare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn.

2. Varför ser jag IP-adresser för belastningsutjämnare i rapporten?  <br />
Om du ser IP-adresser för belastningsutjämnare är det mycket troligt att en extern belastningsutjämnare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn. Konfigurera belastningsutjämnaren korrekt för att vidarebefordra klientens IP-adress. 

3. Hur gör jag för att blockera IP-adressen?  <br />
Du bör lägga till identifierade skadliga IP-adresser i brandväggen eller blockera dem i Exchange.   <br />

4. Varför visas inte några objekt i rapporten? <br />
   - De misslyckade inloggningsaktiviteterna överskrider inte tröskelinställningarna. 
   - Kontrollera att det inte finns någon aktiv varning om att ”Hälsotjänsten är inte uppdaterad” i din AD FS-serverlista.  Läs mer om [felsökning av den här aviseringen](active-directory-aadconnect-health-data-freshness.md).
   - Granskningar är inte aktiverade i AD FS-servergrupperna.
 
5. Varför ser jag ingen åtkomst till rapporten?  <br />
Du måste ha behörighet som global administratör eller [säkerhetsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader). Kontakta en global administratör för att få åtkomst.


## <a name="related-links"></a>Relaterade länkar
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
