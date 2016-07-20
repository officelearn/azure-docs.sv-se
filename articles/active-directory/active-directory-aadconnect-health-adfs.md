
<properties
    pageTitle="Använda Azure AD Connect Health med AD FS | Microsoft Azure"
    description="Den här sidan handlar om Azure AD Connect Health och hur du övervakar den lokala AD FS-infrastrukturen."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="stevenpo"
    editor="karavar"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/21/2016"
    ms.author="vakarand"/>

# Använda Azure AD Connect Health med AD FS
Följande dokumentation gäller specifikt för övervakningen av AD FS-infrastrukturen med Azure AD Connect Health.

## AD FS-aviseringar
Avsnittet om Azure AD Connect Health-aviseringar innehåller en lista över aktiva aviseringar. För varje avisering finns relevant information, lösningssteg och länkar till relaterad dokumentation. Om du väljer en aktiv eller åtgärdad avisering visas ett nytt blad med ytterligare information, samt de steg som du kan utföra för att åtgärda aviseringen liksom länkar till ytterligare dokumentation. Du kan också visa historiska data för tidigare åtgärdade aviseringar.

Om du väljer en avisering visas ytterligare information samt de steg som du kan utföra för att åtgärda aviseringen liksom länkar till ytterligare dokumentation.

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health/alert2.png)



## Användningsanalys för AD FS
Azure AD Connect Health-användningsanalys analyserar autentiseringstrafiken på dina federationsservrar. Om du markerar kryssrutan för användningsanalys öppnas bladet för användningsanalys, som visar mått och grupperingar.

>[AZURE.NOTE] Om du ska kunna använda användningsanalys med AD FS måste AD FS-granskning vara aktiverat. Mer information finns i [Aktivera granskning för AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health/report1.png)

Om du vill välja fler mått, ange ett tidsintervall eller ändra grupperingen högerklickar du bara på diagrammet med användningsanalysen och väljer Redigera diagram. Sedan kan du ange tidsintervallet, ändra eller välja mått och ändra grupperingen. Du kan visa autentiseringstrafikens fördelning baserat på olika ”mått” och gruppera varje mått med relevanta ”gruppera efter”-parametrar som beskrivs nedan.

| Mått | Gruppera efter | Vad grupperingen betyder och varför den är användbar? |
| ------ | -------- | -------------------------------------------- |
| Totalt antal förfrågningar: Totalt antal förfrågningar som bearbetas av federationstjänsten | Alla | Med det här alternativet visas antalet förfrågningar utan gruppering. |
|  | Program | Med det här alternativet grupperas antalet förfrågningar baserat på den förlitande parten i fråga. Den här grupperingen är användbar för att förstå vilket program som tar emot en viss procentandel av den totala trafiken. |
|  | Server | Med det här alternativet grupperas antalet förfrågningar baserat på servern som bearbetade förfrågan. Den här grupperingen är användbar för att förstå belastningsfördelningen av den totala trafiken. |
|  | Anslut till arbetsplats | Med det här alternativet grupperas antalet förfrågningar baserat på om förfrågningarna kommer från arbetsplatsanslutna enheter (kända). Den här grupperingen är användbar för att förstå om dina resurser används med enheter som är okända för identitetsinfrastrukturen. |
|  | Autentiseringsmetod | Med det här alternativet grupperas förfrågningarna baserat på den autentiseringsmetod som används för autentisering. Den här grupperingen är användbar för att förstå den gemensamma autentiseringsmetoden som används för autentisering. Följande är möjliga autentiseringsmetoder <ol> <li>Windows-integrerad autentisering (Windows)</li> <li>Formulärbaserad autentisering (formulär)</li> <li>Enkel inloggning (SSO, Single Sign On)</li> <li>X509-certifikatautentisering (certifikat)</li> <br>Observera att en begäran räknas som en enkel inloggning (SSO) om federationsservrarna tar emot förfrågan med en SSO-cookie. I så fall, om cookien är giltig, uppmanas inte användaren att ange autentiseringsuppgifter och får sömlös åtkomst till programmet. Detta är vanligt om du har flera förlitande parter som skyddas av federationsservrar. |
|  | Nätverksplats | Med det här alternativet grupperas förfrågningarna baserat på användarens nätverksplats. Platsen kan vara antingen intranät eller extranät. Den här grupperingen är användbar för att vet vilken procentandel av trafiken som kommer från intranätet och extranätet. |
| Totalt antal misslyckade begäranden: Totalt antal misslyckade förfrågningar som bearbetas av federationstjänsten. <br> (Det här måttet är bara tillgängligt på AD FS för Windows Server 2012 R2)| Feltyp | Med det här alternativet visas antalet fel baserat på fördefinierade feltyper. Den här grupperingen är användbar för att förstå de vanliga typerna av fel. <ul><li>Felaktigt användarnamn eller lösenord: Fel som beror på ett felaktigt användarnamn eller lösenord.</li> <li>”Utelåsning från extranät”: Fel som beror på att förfrågningarna togs emot från en användare som var utelåst från extranätet </li><li> ”Lösenordet har upphört att gälla”: Fel som beror på att användaren loggar in med ett lösenord som upphört att gälla.</li><li>”Inaktiverat konto”: Fel som beror på att användaren loggar in med ett inaktiverat konto.</li><li>”Enhetsautentisering”: Fel som beror på att användaren inte kan autentiseras med enhetsautentisering.</li><li>”Autentisering med användarcertifikat”: Fel som beror på att användaren inte kan autentiseras på grund av ett ogiltigt certifikat.</li><li>”MFA”: Fel som beror på att användaren inte kan autentiseras med hjälp av Multi-Factor Authentication.</li><li>”Andra autentiseringsuppgifter”: ”Utfärdandeauktorisering”: Fel som beror på auktoriseringsproblem.</li><li>”Utfärdandedelegering”: Fel som beror på utfärdandedelegeringsproblem.</li><li>”Tokengodkännande”: Fel som beror på att ADFS avvisar token från en tredjeparts identitetsprovider.</li><li>”Protokoll”: Fel som beror på protokollproblem.</li><li>”Okänt”: Samla in allt. Andra fel som inte passar i de angivna kategorierna.</li> |
|  | Server | Grupperar felen baserat på servern. Detta är användbart för att förstå felfördelningen mellan servrar. En ojämn fördelning kan vara ett tecken på att en server har felaktigt tillstånd. |
|  | Nätverksplats | Grupperar felen baserat på förfrågningarnas nätverksplats (intranät eller extranät). Detta är användbart för att förstå vilken typ av förfrågningar som misslyckas. |
|  | Program | Grupperar felen baserat på målprogrammet (förlitande part). Detta är användbart för att förstå vilket program som har flest antal fel. |
| Antal användare: Genomsnittligt antal unika användare som är aktiva i systemet | Alla | Visar genomsnittligt antal användare som använder federationstjänsten under det valda tidsintervallet. Användarna grupperas inte. <br>Det genomsnittliga antalet beror på det valda tidsintervallet. |
|  | Program | Grupperar det genomsnittliga antalet användare baserat på målprogrammet (förlitande part). Detta är användbart för att förstå hur många användare som använder olika program. |


## Prestandaövervakning för AD FS
Azure AD Connect Health-prestandaövervakning tillhandahåller övervakningsinformation för mått. Om du markerar rutan Övervakning öppnas ett blad med detaljerad information om måtten.


![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health/perf1.png)


Om du väljer filteralternativet längst upp i bladet kan du filtrera efter server och visa måtten för enskilda servrar. Om du vill ändra måtten högerklickar du bara i övervakningsdiagrammet under övervakningsbladet och väljer Redigera diagram. Från det nya bladet som öppnas kan du sedan välja ytterligare mått i listrutan och ange ett tidsintervall som du vill visa prestandadata för.

## Rapporter för AD FS
Azure AD Connect Health tillhandahåller rapporter om aktivitet och prestanda för AD FS. Dessa rapporter hjälper administratörer att få insyn i aktiviteter på deras AD FS-servrar.

### De 50 användarna med flest misslyckade inloggningar med användarnamn/lösenord

Ett av de vanligaste skälen till en misslyckad autentiseringsbegäran på en AD FS-server är en begäran med ogiltiga autentiseringsuppgifter, dvs. ett felaktigt användarnamn eller lösenord. Detta inträffar vanligtvis på grund av komplexa lösenord, bortglömda lösenord eller skrivfel.

Men det finns andra orsaker som kan resultera i att ett oväntat antal förfrågningar av den här typen hanteras av AD FS-servrarna. Exempel på detta är om ett program cachelagrar autentiseringsuppgifter för användare och autentiseringsuppgifterna upphör att gälla eller om en obehörig användare försöker logga in på en användares konto med en serie välkända lösenord.

Azure AD Connect Health för AD FS tillhandahåller en rapport över de 50 användarna med flest misslyckade inloggningsförsök på grund av ett ogiltigt användarnamn eller lösenord. Tjänsten gör detta genom att bearbeta granskningshändelserna som genereras av alla AD FS-servrar i servergrupperna

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health-adfs/report1a.png)

I den här rapporten kan du enkelt få tillgång till följande typer av information:

- Totalt antal misslyckade begäranden med fel användarnamn/lösenord de senaste 30 dagarna
- Genomsnittligt antal användare som misslyckades med inloggningen på grund av ett felaktigt användarnamn/lösenord varje dag.

Om du klickar på den här delen kommer du till huvudbladet för rapporter som innehåller ytterligare information. Bladet innehåller till exempel ett diagram med information som hjälper dig att skapa en baslinje för begäranden med felaktiga användarnamn eller lösenord och listan över de 50 användarna med flest misslyckade försök.

Diagrammet innehåller följande information:

- Totalt antal misslyckade inloggningar på grund av ett felaktigt användarnamn/lösenord per dag.
- Totalt antal unika användare med misslyckade inloggningar per dag.

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health-adfs/report2a.png)

Rapporten innehåller följande:

| Rapportobjekt | Beskrivning
| ------ | -------- |
|Användar-ID| Visar användar-ID:t som användes. Observera att värdet är vad användaren skrev och i vissa fall ser du även det felaktiga användar-ID:t som användes.|
|Misslyckade försök|Visar totalt antal misslyckade försök för det specifika användar-ID:t. Tabellen sorteras med flest antal misslyckade försök i fallande ordning.|
|Senaste fel|Visar tidsstämpeln då det senaste felet uppstod.



>[AZURE.NOTE] Den här rapporten uppdateras automatiskt varannan timme med den nya informationen som samlats in inom den tidsperioden. Därför kanske inloggningsförsök under de senaste två timmarna inte finns med i rapporten.



## Relaterade länkar

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Jun16_HO2-->


